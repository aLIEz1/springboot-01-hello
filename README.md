# springboot自动配置原理

:flags:

pom.xml

- spring-boot-dependencies:核心依赖在父工程中
- 我们在写或者引入springboot依赖的时候不需要指定版本，因为有这些版本仓库



starter：

- springboot中的启动场景
- 比如引入了如下依赖

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

springboot就会自动导入web环境所有的依赖

- springboot会将所有的功能场景，都变成一个个的启动器

- 如果我们要使用什么功能，只需要找到相应的启动器就可以了 `starter`

  

`@SpringBootApplication`

```java
package com.lm;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author super
 */
@SpringBootApplication
public class Springboot01HelloApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot01HelloApplication.class, args);
    }

}

```

```java
@SpringBootApplication:springboot的主启动类
	@SpringBootConfiguration:springboot的配置类
    	@Configuration:spring的配置类
            @Component:说明这也是一个spring的组件
	@EnableAutoConfiguration:开启自动配置
        @AutoConfigurationPackage:自动配置包
            @Import({Registrar.class}):自动配置包注册
        @Import({AutoConfigurationImportSelector.class}):自动配置导入选择器
```



获取候选的配置

```java
List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
```



实现类

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```



springboot 所有的自动配置都是在启动的时候扫描并加载：`spring.factories`

但是不一定生效，要判断条件是否成立，只要到瑞了对应的starter，就有对应的启动器了，有了启动器我们自动装配就会生效，然后就配置成功

1. springboot在启动的时候，从类路径下/META-INF/spring.factories获取指定的值
2. 将这些自动配置的类导入容器，自动配置类就会生效，帮我们进行自动配置
3. 以前我们需要手动配置的东西，现在springboot帮我们做了
4. 整合JavaEE，解决方案和自动配置的东西都在spring-boot-autoconfigure.jar这个包下
5. 它会把所有需要导入的组件（类）以类名的方式返回，这些组件就会被添加进容器
6. 容器中也会存在相当多的xxxAutoConfiguration的这种文件(@Bean)，就是这些类会给容器中导入了这个场景需要的所有组件,并自动配置@Configuration，`JavaConfig`
7. 有了自动配置类,免去了我们手动编写配置文件的工作

![自动配置原理分析.png](https://yanxuan.nosdn.127.net/3ae8535e6b17d5af7d7e77716efff7b8.png)