# ObjectMapper 定制

在使用 json 格式时，springboot 默认使用 ObjectMapper 实例来序列化响应及反序列化请求

但是在序列化或反序列化 `java.time.LocalDate`, `java.time.LocalTime`, `java.time.LocalDateTime`时，
序列化后的格式常常不是我们想要的。
当需要自定义日期格式(如：“yyyy-MM-dd HH:mm:ss”)，可以通过几种方式进行定制

## 覆盖 ObjectMapper

```java
@Configuration
public class CustomConfig {
    @Bean(name = "customModule")
    public Module javaTimeModule() {
        JavaTimeModule javaTimeModule = new JavaTimeModule();
        // 日期序列化
        javaTimeModule.addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
        // 日期反序列化
        javaTimeModule.addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));

        return javaTimeModule;
    }

    @Bean
    @Primary
    public ObjectMapper customObjectMapper(@Qualifier("customModule") Module javaTimeModule) {
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.registerModule(javaTimeModule)
                    .disable(SerializationFeature.FAIL_ON_EMPTY_BEANS)
                    .enable(SerializationFeature.INDENT_OUTPUT);
        return objectMapper;
    }
}
```

## Jackson2ObjectMapperBuilderCustomizer

它应用于通过 Jackson2ObjectMapperBuilder 创建的默认 ObjectMapper。

```java
@Configuration
public class CustomConfig {
    private static final LocalDateTimeSerializer LOCAL_DATETIME_SERIALIZER = new LocalDateTimeSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));

    @Bean
    public Jackson2ObjectMapperBuilderCustomizer jackson2ObjectMapperBuilderCustomizer() {
        return builder -> builder.indentOutput(true).failOnEmptyBeans(false)
                .serializers(LOCAL_DATETIME_SERIALIZER);
    }
}
```

## Jackson2ObjectMapperBuilder

默认情况下，Spring Boot 在构建 ObjectMapper 时会使用此构建器

```java
@Configuration
public class CustomConfig {
    private static final LocalDateTimeSerializer LOCAL_DATETIME_SERIALIZER = new LocalDateTimeSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));

    @Bean
    public Jackson2ObjectMapperBuilder builder() {
        return new Jackson2ObjectMapperBuilder().indentOutput(true).failOnEmptyBeans(false).serializers(LOCAL_DATETIME_SERIALIZER);
    }
}
```

## MappingJackson2HttpMessageConverter

`HttpMessageConvertersAutoConfiguration`通过`@import`方式让`JacksonHttpMessageConvertersConfiguration`配置生效，
默认情况下，`JacksonHttpMessageConvertersConfiguration`通过`@Bean`的方式配置了消息转换器`MappingJackson2HttpMessageConverter`

```java
@Configuration
public class CustomConfig {
    private static final LocalDateTimeSerializer LOCAL_DATETIME_SERIALIZER = new LocalDateTimeSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));

    @Bean
    public MappingJackson2HttpMessageConverter mappingJackson2HttpMessageConverter() {
        Jackson2ObjectMapperBuilder builder = new Jackson2ObjectMapperBuilder().serializers(LOCAL_DATETIME_SERIALIZER).featuresToDisable(SerializationFeature.FAIL_ON_EMPTY_BEANS);
        return new MappingJackson2HttpMessageConverter(builder.build());
    }
}
```

## 其他常用的自定义配置

```java
@Configuration
public class CustomConfig {

    @Bean
    @Primary
    public ObjectMapper getObjectMapper() {
        ObjectMapper objectMapper = new ObjectMapper();
        // PropertyNamingStrategy.LOWER_CAMEL_CASE : 驼峰命名，字段的首字母小写. {"birdName":"dayan","birdType":1,"birdWeight":100}
        // PropertyNamingStrategy.LOWER_CAMEL_CASE : 驼峰命名，字段的首字母大写. {"BirdName":"dayan","BirdSex":1,"BirdWeight":100}
        // PropertyNamingStrategy.SNAKE_CASE       : 字段小写，多个单词以下划线_分隔. {"bird_name":"dayan","bird_sex":1,"bird_weight":100}
        // PropertyNamingStrategy.KEBAB_CASE       : 字段小写，多个单词以中横线-分隔. {"bird-name":"dayan","bird-sex":1,"bird-weight":100}
        // PropertyNamingStrategy.LOWER_CASE       : 字段小写，多个单词间无分隔符. {"birdname":"dayan","birdsex":1,"birdweight":100}
        // PropertyNamingStrategy.LOWER_DOT_CASE   : 字段小写，多个单词以点号.分隔. {"bird.name":"dayan","bird.sex":1,"bird.weight":100}
//        去除掉对getter和setter的依赖
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
//        当属性的值为空（null）时，不进行序列化
        objectMapper.setSerializationInclusion(JsonInclude.Include.NON_NULL);
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
        objectMapper.setTimeZone(TimeZone.getTimeZone("GMT+8"));
//        SerializationFeature.INDENT_OUTPUT ： 美化json输出
        objectMapper.enable(SerializationFeature.INDENT_OUTPUT);
//        如果pojo没有public的方法或属性时，不报错
        objectMapper.disable(SerializationFeature.FAIL_ON_EMPTY_BEANS);
//        序列化JSON串时，在值上打印出对象类型
        objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL);
//       objectMapper.disable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS) : "2021-05-19T16:52:56.285+0000"
//       objectMapper.disable(SerializationFeature.WRITE_DATE_KEYS_AS_TIMESTAMPS) : 1621443317760
//       DateFormat.getDateInstance() "2021-5-20"
        objectMapper.setDateFormat(DateFormat.getDateTimeInstance());
        return objectMapper;
    }
}

```
