## 2.3.2 .m2

```
mvn help:system #打印所有的java系统属性和环境变量
```

## 2.4 设置HTTP代理

```
 <proxies>
    <proxy>
      <id>my-proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>x.x.x.x</host>
      <port>1080</port>
      <!--当代理服务需要认证时需配
      <username></username>
      <password></password>
      指定哪些主机不需要代理，这里支持|和*
      <nonProxyHosts>localhost|127.0.0.1</nonProxyHosts>
      -->  
    </proxy>
 </proxies>

```

## 2.7 Maven安装最佳实践
#### 2.7.1 设置MAVEN_OPTS环境变量
1. mvn实际执行的是Java命令，所以可以通过设置MAVEN_OPTS环境变量设置Java命令的参数
2. 由于Java默认最大可用内存比较小，通常设置MAVEN_OPTS值为-Xms128m -Xmx512m
3. 尽量修改环境变量，别直接在mvn脚本中改，因为maven一升级就得重新设置

#### 2.7.2 配置用户范围settings.xml
1. $MAVEN_HOME/conf/settings.xml全局的
2. ~/.m2/settings.xml用户的（推荐）便于升级

#### 2.7.3 不要使用IDE中的MAVEN

## 3.1 编写POM

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
        <modelVersion>4.0.0</modelVersion> 
        <groupId>pers.liy.helloworld</groupId>
        <artifactId>hello-world</artifactId>
        <version>1.0-SNAPSHOT</version>
        <name>Maven Hello World Project</name>
</project>

```
1. modelVersion：pom模型的版本，Maven2和Maven3是4.0.0
2. 坐标：groupId、artifactId、version
3. groupId：项目属于哪个组，eg，googlecode公司的myapp项目就是com.googlecode.myapp
4. artifactId：当前项目在组中的唯一ID
5. version：当前项目的版本
6. SNAPSHOT：快照，说明该项目还处在开发中，不稳定
7. name：项目名称，非必须，推荐写
8. pom稳定后日常开发基本不涉及修改pom

## 3.2 编写主代码
```
package pers.liy.helloworld;

public class HelloWorld {

    public String sayHello() {
        return "Hello world!";
    }

    public void main(String[] args) {
        System.out.println(new HelloWorld().sayHello());
    }
}

```
1. src/main/java目录，maven约定会自动搜寻该目录找到项目主代码
2. 包名应与groupId吻合
3. mvn clean compile编译
4. clean是删除target/
5. clean:clean、resources:resources、compiler:compile对应一些Maven插件:目标

## 3.3 编写测试代码
1. src/test/java目录，maven中默认测试代码目录
2. 添加JUnit依赖
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
	<groupId>pers.liy.helloworld</groupId>
	<artifactId>hello-world</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>Maven Hello World Project</name>

    <dependencies>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.7</version>
        <scope>test</scope>
    </dependencies>
</project>

```
3. maven自动从中央仓库下载依赖：http://repo1.maven.org/maven2/
4. scope为依赖范围，若依赖范围为test表示只对测试生效。也就是说，在测试代码中import JUnit代码完全没问题，但在主代码中import JUnit就会编译出错。
5. scope默认是compile，表示对主代码和测试代码都有效
```
package pers.liy.helloworld;

import static org.junit.Assert.assertEquals;
import org.junit.Test;

public class HelloWorldTest {
    @Test
    public void testSayHello() {
        HelloWorld helloWorld = new HelloWorld();
        String result = helloWorld.sayHello();
        assertEquals("Hello maven!", result);
    }
}

```
6. mvn clean test
7. surefire是maven中负责执行测试的插件

## 3.4 打包与运行
1. pom中没有指定打包类型，使用默认打包类型jar
2. mvn clean package进行打包
3. jar:jar就是jar插件的jar目标将项目主代码打成artifact-version.jar输出到target/目录下
4. mvn clean install让其他maven项目直接能引用这个jar包
5. install:install将刚打包的jar安装到本地maven仓库中，其他项目才能使用它
6. 默认mvn clean package打包的jar没有把main方法的类信息打到manifest中，不可执行
7. 为了生成可执行的jar文件，需要用maven-shade-plugin
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>pers.liy.helloworld</groupId>
    <artifactId>hello-world</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>Maven Hello World Project</name>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.7</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <transformers>
                        <transformer implementation = "org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>pers.liy.helloworld.HelloWorld</mainClass>
                        </transformer>
                    </transformers>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>

```
8. mvn clean package之后在jar包中的META-INF/MANIFEST.MF中多了
```
Main-Class: pers.liy.helloworld.HelloWorld
```

## 3.5 Archetype生成项目骨架
1. maven3运行mvn archetype:generate
2. maven2运行mvn org.apache.maven.plugins:maven-archetype-plugin:2.0-alpha-5:generate。
3. 因为maven2直接运行mvn archetype:generate不安全，没有指定版本，会自动现在最新版本，可能将不稳定的SNAPSHOP下载下来导致运行失败。maven3中自动下载最新稳定版本，因此安全。
4. 格式含义是wgroupId:artifactId:version:goal
5. 注意执行mvn archetype:generate会从国外网站下载资源，需要配置国外代理







