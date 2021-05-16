# Maven 常用命令

Maven 库：

http://repo2.maven.org/maven2/

Maven 依赖查询：

http://mvnrepository.com/

## 一、Maven 常用命令：

**1. 创建 Maven 的普通 Java 项目：**

```
mvn archetype:create
    -DgroupId=packageName
    -DartifactId=projectName
```

**2. 创建 Maven 的 Web 项目：**

```
mvn archetype:create
    -DgroupId=packageName
    -DartifactId=webappName
    -DarchetypeArtifactId=maven-archetype-webapp
```

**3. 反向生成 maven 项目的骨架：**

```
mvn archetype:generate
```

你是怎么创建你的 maven 项目的?是不是像这样:

```
mvn archetype:create -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.ryanote -Dartifact=common
```

如果你还再用的话,那你就 out 了,现代人都用 mvn archetype:generate 了,它将创建项目这件枯燥的事更加人性化,你再也不需要记那么多的 archetypeArtifactId,你只需输入 archetype:generate,剩下的就是做”选择题”了.

缩写写法：

```
mvn archetype:generate -DgroupId=otowa.user.dao -DartifactId=user-dao -Dversion=0.01-SNAPSHOT
```

**4. 编译源代码：**

```
mvn compile
```

**5. 编译测试代码：**

```
mvn test-compile
```

**6. 运行测试:**

```
mvn test
```

**7. 产生 site：**

```
mvn site
```

**8. 打包：**

```
mvn package
```

**9. 在本地 Repository 中安装 jar：**

```
mvn install
例：installing D:\xxx\xx.jar to D:\xx\xxxx
```

**10. 清除产生的项目：**

```
mvn clean
```

**11. 生成 eclipse 项目：**

```
mvn eclipse:eclipse
```

**12. 生成 idea 项目：**

```
mvn idea:idea
```

**13. 组合使用 goal 命令，如只打包不测试：**

```
mvn -Dtest package
```

**14. 编译测试的内容：**

```
mvn test-compile
```

**15. 只打 jar 包:**

```
mvn jar:jar
```

**16. 只测试而不编译，也不测试编译：**

( -skipping 的灵活运用，当然也可以用于其他组合命令)

```
mvn test -skipping compile -skipping test-compile
```

**17. 清除 eclipse 的一些系统设置:**

```
mvn eclipse:clean
```

**18.查看当前项目已被解析的依赖：**

```
mvn dependency:list
```

**19.上传到私服：**

```
mvn deploy
```

**20. 强制检查更新，由于快照版本的更新策略(一天更新几次、隔段时间更新一次)存在，如果想强制更新就会用到此命令:**

```
mvn clean install-U
```

**21. 源码打包：**

```
mvn source:jar
或
mvn source:jar-no-fork
```

### mvn compile 与 mvn install、mvn deploy 的区别

1. mvn compile，编译类文件
2. mvn install，包含 mvn compile，mvn package，然后上传到本地仓库
3. mvn deploy,包含 mvn install,然后，上传到私服

**22、跳过测试打包**

```sh
mvn install -DskipTests
#或者
mvn install -Dmaven.test.skip=true
```

## 二、其他命令:

一般使用情况是这样，首先通过 cvs 或 svn 下载代码到本机，然后执行 mvn eclipse:eclipse 生成 ecllipse 项目文件，然后导入到 eclipse 就行了；修改代码后执行 mvn compile 或 mvn test 检验，也可以下载 eclipse 的 maven 插件。

**1. 显示版本信息 :**

```
mvn -version/-v
```

**2. 创建 mvn 项目:**

```
mvn archetype:create -DgroupId=com.oreilly -DartifactId=my-app
```

**3. 生成 target 目录，编译、测试代码，生成测试报告，生成 jar/war 文件 :**

```
mvn package
```

**4. 运行项目于 jetty 上:**

```
mvn jetty:run
```

**5. 显示详细错误 信息:**

```
mvn -e
```

**6. 验证工程是否正确，所有需要的资源是否可用:**

```
mvn validate
```

**7. 在集成测试可以运行的环境中处理和发布包:**

```
mvn integration-test
```

**8. 运行任何检查，验证包是否有效且达到质量标准:**

```
mvn verify
```

**9. 产生应用需要的任何额外的源代码，如 xdoclet :**

```
mvn generate-sources
```

**10. 使用 help 插件的 describe 目标来输出 Maven Help 插件的信息:**

```
mvn help:describe -Dplugin=help
```

**11. 使用 Help 插件输出完整的带有参数的目标列 :**

```
mvn help:describe -Dplugin=help -Dfull
```

**12. 获取单个目标的信息,设置 mojo 参数和 plugin 参数。此命令列出了 Compiler 插件的 compile 目标的所有信息 :**

```
mvn help:describe -Dplugin=compiler -Dmojo=compile -Dfull
```

**13. 列出所有 Maven Exec 插件可用的目标:**

```
mvn help:describe -Dplugin=exec -Dfull
```

**14. 看这个“有效的 (effective)”POM，它暴露了 Maven 的默认设置 :**

```
mvn help:effective-pom
```

**15. 想要查看完整的依赖踪迹，包含那些因为冲突或者其它原因而被拒绝引入的构件，打开 Maven 的调试标记运行 :**

```
mvn install -X
```

**16. 给任何目标添加 maven.test.skip 属性就能跳过测试 :**

```
mvn install -Dmaven.test.skip=true
```

**17. 构建装配 Maven Assembly 插件是一个用来创建你应用程序特有分发包的插件 :**

```
mvn install assembly:assembly
```

**18. 生成 Wtp 插件的 Web 项目 :**

```
mvn -Dwtpversion=1.0 eclipse:eclipse
```

**19. 清除 Eclipse 项目的配置信息(Web 项目) :**

```
mvn -Dwtpversion=1.0 eclipse:clean
```

**20. 将项目转化为 Eclipse 项目 :**

```
mvn eclipse:eclipse
```

### **21. mvn exec 命令可以执行项目中的 main 函数 :**

```
首先需要编译java工程：mvn compile
不存在参数的情况下：mvn exec:java -Dexec.mainClass="***.Main"
存在参数：mvn exec:java -Dexec.mainClass="***.Main" -Dexec.args="arg0 arg1 arg2"
指定运行时库：mvn exec:java -Dexec.mainClass="***.Main" -Dexec.classpathScope=runtime
```

**22. 打印出已解决依赖的列表 :**

```
mvn dependency:resolve
```

**23. 打印整个依赖树 :**

```
mvn dependency:tree
```

### 在应用程序用使用多个存储库

```
<repositories>
    <repository>
        <id>Ibiblio</id>
        <name>Ibiblio</name>
        <url>http://www.ibiblio.org/maven/</url>
    </repository>
    <repository>
        <id>PlanetMirror</id>
        <name>Planet Mirror</name>
        <url>http://public.planetmirror.com/pub/maven/</url>
    </repository>
</repositories>

mvn deploy:deploy-file -DgroupId=com -DartifactId=client -Dversion=0.1.0 -Dpackaging=jar -Dfile=d:\client-0.1.0.jar -DrepositoryId=maven-repository-inner -Durl=ftp://xxxxxxx/opt/maven/repository/
```

### **发布第三方 Jar 到本地库中**

```
mvn install:install-file -DgroupId=com -DartifactId=client -Dversion=0.1.0 -Dpackaging=jar -Dfile=d:\client-0.1.0.jar


-DdownloadSources=true

-DdownloadJavadocs=true
```

## 三，附加

```
mvn help:describe
```

你是否因为记不清某个插件有哪些 goal 而痛苦过,你是否因为想不起某个 goal 有哪些参数而苦恼,那就试试这个命令吧,它会告诉你一切的.

参数: 1. -Dplugin=pluginName 2. -Dgoal(或-Dmojo)=goalName:与-Dplugin 一起使用,它会列出某个插件的 goal 信息,

如果嫌不够详细,同样可以加-Ddetail.(注:一个插件 goal 也被认为是一个 “Mojo”)

下面大家就运行 mvn help:describe -Dplugin=help -Dmojo=describe 感受一下吧!

```
mvn tomcat:run
```

用了 maven 后,你再也不需要用 eclipse 里的 tomcat 来运行 web 项目(实际工作中经常会发现用它会出现不同步更新的情况),只需在对应目录里运行 mvn tomat:run 命令,然后就可在浏览器里运行查看了

当然你也可以在命令里加参数来实现特定的功能,

下面几个比较常用:

- 跳过测试:-Dmaven.test.skip(=true)

* 指定端口:-Dmaven.tomcat.port=9090

* 忽略测试失败:-Dmaven.test.failure.ignore=true

当然,如果你的其它关联项目有过更新的话,一定要在项目根目录下运行 mvn clean install 来执行更新,再运行 mvn tomcat:run 使改动生效.

```
mvnDebug tomcat:run
```

这条命令主要用来远程测试,它会监听远程测试用的 8000 端口,在 eclipse 里打开远程测试后,它就会跑起来了,设断点,调试,一切都是这么简单.上面提到的那几个参数在这里同样适用.

```
mvn dependency:sources
```

故名思义,有了它,你就不用到处找源码了,运行一下,你项目里所依赖的 jar 包的源码就都有了
