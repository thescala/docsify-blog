## HttpClientUtils 工具类

引入依赖：

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.13</version>
</dependency>
 <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### 基于`httpclient`实现：

```java
package com.example.commons.utils;


import org.apache.http.HttpEntity;
import org.apache.http.NameValuePair;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.conn.ssl.DefaultHostnameVerifier;
import org.apache.http.conn.util.PublicSuffixMatcher;
import org.apache.http.conn.util.PublicSuffixMatcherLoader;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.net.URL;
import java.nio.charset.StandardCharsets;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;


public class HttpClientUtil {

    private final RequestConfig requestConfig = RequestConfig.custom().setSocketTimeout(15000)
            .setConnectTimeout(15000)
            .setConnectionRequestTimeout(15000)
            .build();

    private static HttpClientUtil instance = null;

    private HttpClientUtil() {
    }

    public static HttpClientUtil getInstance() {
        if (instance == null) {
            instance = new HttpClientUtil();
        }
        return instance;
    }

    /**
     * 发送 post请求
     *
     * @param httpUrl 地址
     */
    public String doPost(String httpUrl) {
        // 创建httpPost
        HttpPost httpPost = new HttpPost(httpUrl);
        return doPost(httpPost);
    }

    /**
     * 发送 post请求
     *
     * @param httpUrl 地址
     * @param params  参数(格式:key1=value1&key2=value2)
     */
    public String doPost(String httpUrl, String params) {
        HttpPost httpPost = new HttpPost(httpUrl);
        try {
            //设置参数
            StringEntity stringEntity = new StringEntity(params, StandardCharsets.UTF_8);
            stringEntity.setContentType("application/x-www-form-urlencoded");
            httpPost.setEntity(stringEntity);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return doPost(httpPost);
    }

    /**
     * 发送 post请求
     *
     * @param httpUrl 地址
     * @param maps    参数
     */
    public String doPost(String httpUrl, Map<String, String> maps) {
        HttpPost httpPost = new HttpPost(httpUrl);// 创建httpPost
        httpPost.setHeader("Content-Type", "application/x-www-form-urlencoded;charset=" + StandardCharsets.UTF_8);
        httpPost.setHeader("User-Agent", "Mozilla/5.0 (iPhone; CPU iPhone OS 9_1 like Mac OS X) AppleWebKit/601.1.");
        // 创建参数队列
        List<NameValuePair> nameValuePairs = new ArrayList<>();
        for (String key : maps.keySet()) {
            nameValuePairs.add(new BasicNameValuePair(key, maps.get(key)));
        }
        try {
            httpPost.setEntity(new UrlEncodedFormEntity(nameValuePairs, StandardCharsets.UTF_8));
        } catch (Exception e) {
            e.printStackTrace();
        }
        return doPost(httpPost);
    }


    /**
     * 发送Post请求
     *
     * @param httpPost HttpPost
     * @return String
     */
    private String doPost(HttpPost httpPost) {
        CloseableHttpClient httpClient = null;
        CloseableHttpResponse response = null;
        HttpEntity entity;
        String responseContent = null;
        try {
            // 创建默认的httpClient实例.
            httpClient = HttpClients.createDefault();
            httpPost.setConfig(requestConfig);
            // 执行请求
            response = httpClient.execute(httpPost);
            entity = response.getEntity();
            responseContent = EntityUtils.toString(entity, StandardCharsets.UTF_8);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                // 关闭连接,释放资源
                if (response != null) {
                    response.close();
                }
                if (httpClient != null) {
                    httpClient.close();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return responseContent;
    }

    /**
     * 发送 get请求
     *
     * @param httpUrl String
     */
    public String doGet(String httpUrl) {
        HttpGet httpGet = new HttpGet(httpUrl);// 创建get请求
        return doGet(httpGet);
    }

    /**
     * 发送 get请求Https
     *
     * @param httpUrl String
     */
    public String doHttpsGet(String httpUrl) {
        HttpGet httpGet = new HttpGet(httpUrl);// 创建get请求
        return doHttpsGet(httpGet);
    }

    /**
     * 发送Get请求
     *
     * @param httpGet HttpGet
     * @return String
     */
    private String doGet(HttpGet httpGet) {
        CloseableHttpClient httpClient = null;
        CloseableHttpResponse response = null;
        HttpEntity entity;
        String content = null;
        try {
            // 创建默认的httpClient实例.
            httpClient = HttpClients.createDefault();
            httpGet.setConfig(requestConfig);
            // 执行请求
            response = httpClient.execute(httpGet);
            entity = response.getEntity();
            content = EntityUtils.toString(entity, StandardCharsets.UTF_8);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            release(response,httpClient);
        }
        return content;
    }

    /**
     * 发送Get请求Https
     *
     * @param httpGet HttpGet
     * @return String
     */
    private String doHttpsGet(HttpGet httpGet) {
        CloseableHttpClient httpClient = null;
        CloseableHttpResponse response = null;
        HttpEntity entity;
        String content = null;
        try {
            // 创建默认的httpClient实例.
            PublicSuffixMatcher publicSuffixMatcher = PublicSuffixMatcherLoader.load(new URL(httpGet.getURI().toString()));
            DefaultHostnameVerifier hostnameVerifier = new DefaultHostnameVerifier(publicSuffixMatcher);
            httpClient = HttpClients.custom().setSSLHostnameVerifier(hostnameVerifier).build();
            httpGet.setConfig(requestConfig);
            // 执行请求
            response = httpClient.execute(httpGet);
            entity = response.getEntity();
            content = EntityUtils.toString(entity, StandardCharsets.UTF_8);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
           release(response,httpClient);
        }
        return content;
    }

    private void release(CloseableHttpResponse response,CloseableHttpClient httpClient){
        if (response != null) {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        if (httpClient != null) {
            try {
                httpClient.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

}

```

### 通过 restTempleate 实现

```java

package com.example.commons.utils;

import org.apache.http.HttpEntityEnclosingRequest;
import org.apache.http.HttpRequest;
import org.apache.http.NoHttpResponseException;
import org.apache.http.client.HttpRequestRetryHandler;
import org.apache.http.client.protocol.HttpClientContext;
import org.apache.http.config.Registry;
import org.apache.http.config.RegistryBuilder;
import org.apache.http.conn.socket.ConnectionSocketFactory;
import org.apache.http.conn.socket.PlainConnectionSocketFactory;
import org.apache.http.conn.ssl.SSLConnectionSocketFactory;
import org.apache.http.conn.ssl.TrustStrategy;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.impl.conn.PoolingHttpClientConnectionManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.client.HttpComponentsClientHttpRequestFactory;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.http.converter.StringHttpMessageConverter;
import org.springframework.web.client.RestTemplate;

import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLException;
import javax.net.ssl.SSLHandshakeException;
import java.io.InterruptedIOException;
import java.net.UnknownHostException;
import java.nio.charset.StandardCharsets;
import java.security.KeyManagementException;
import java.security.KeyStoreException;
import java.security.NoSuchAlgorithmException;
import java.security.cert.X509Certificate;
import java.util.List;
import java.util.Objects;

/**
 * @author admin
 */
@Configuration
public class RestTemplateUtil {

    @Bean
    public RestTemplate restTemplate() {
        TrustStrategy acceptingTrustStrategy = (X509Certificate[] chain, String authType) -> true;
        SSLContext sslContext = null;
        try {
            sslContext = org.apache.http.ssl.SSLContexts.custom()
                    .loadTrustMaterial(null, acceptingTrustStrategy)
                    .build();
        } catch (NoSuchAlgorithmException | KeyManagementException | KeyStoreException e) {
            e.printStackTrace();
        }

        SSLConnectionSocketFactory csf = new SSLConnectionSocketFactory(Objects.requireNonNull(sslContext));

        Registry<ConnectionSocketFactory> registry = RegistryBuilder.<ConnectionSocketFactory>create()
                .register("http", PlainConnectionSocketFactory.getSocketFactory())
                .register("https", csf)
                .build();
        PoolingHttpClientConnectionManager connectionManager = new PoolingHttpClientConnectionManager(registry);
        // 设置连接池最大并发连接数
        connectionManager.setMaxTotal(1000);
        // 每个路由的默认最大连接数
        connectionManager.setDefaultMaxPerRoute(300);
        int retryCount = 3;
        CloseableHttpClient httpClient = HttpClients.custom()
                .setConnectionManager(connectionManager)
                .setRetryHandler(retryHandler(retryCount))
                .build();

        HttpComponentsClientHttpRequestFactory requestFactory = new HttpComponentsClientHttpRequestFactory();

        requestFactory.setHttpClient(httpClient);
        requestFactory.setConnectionRequestTimeout(3000);
        requestFactory.setConnectTimeout(3000);
        requestFactory.setReadTimeout(10000);

        RestTemplate restTemplate = new RestTemplate(requestFactory);
        List<HttpMessageConverter<?>> list = restTemplate.getMessageConverters();
        for (HttpMessageConverter<?> httpMessageConverter : list) {
            if (httpMessageConverter instanceof StringHttpMessageConverter) {
                ((StringHttpMessageConverter) httpMessageConverter).setDefaultCharset(StandardCharsets.UTF_8);
                break;
            }
        }
        return restTemplate;
    }

    private HttpRequestRetryHandler retryHandler(int retryCount) {
        return (e, count, httpContext) -> {
            if (count >= retryCount) {
                return false;
            }
            if (e instanceof NoHttpResponseException) {
                // 掉线
                return true;
            }
            if (e instanceof SSLHandshakeException) {
                // 重试SSL握手异常
                return false;
            }
            if (e instanceof InterruptedIOException) {
                // 超时
                return false;
            }
            if (e instanceof UnknownHostException) {
                // 服务器不可达
                return true;
            }
            if (e instanceof SSLException) {
                // SSL握手异常
                return false;
            }

            HttpClientContext clientContext = HttpClientContext.adapt(httpContext);
            HttpRequest request = clientContext.getRequest();
            // 如果请求是幂等的，就再次尝试
            return !(request instanceof HttpEntityEnclosingRequest);
        };
    }
}

```

参考：https://www.cnblogs.com/fnlingnzb-learner/p/10833004.html
