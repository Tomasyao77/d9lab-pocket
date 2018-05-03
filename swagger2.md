## swagger介绍 `解放双手，提高生产力`

不断增长的业务压力使得服务端的开发运维变得愈加困难，随着前后端的分离，接口文档成了沟通两者的重要纽带。如何高效地编写接口文档是一门技术活，是一种不可或缺的能力。

传统的方式是编写word文档，使用showdoc等在线编辑工具纯文字写作。这些流程很枯燥乏味，效率也很低，并且一个项目的需求不断变化，后端的接口也需要动态调整，之后还要及时更新接口文档，十分麻烦。

swagger是一个流行的API开发框架，技术成熟，在国外十分流行。并且有大牛将spring与swagger整合成了插件，我们只需要在maven项目中引入相关依赖，然后对接口类与方法做相应的注解配置，运行项目后接口文档就自动生成了。

> 参考链接
[https://blog.csdn.net/y534560449/article/details/53694443](https://blog.csdn.net/y534560449/article/details/53694443)
[https://blog.csdn.net/u012476983/article/details/54090423](https://blog.csdn.net/u012476983/article/details/54090423)
[https://www.cnblogs.com/getupmorning/p/7267076.html](https://www.cnblogs.com/getupmorning/p/7267076.html)


## 开始使用
> 1.在pom.xml中引入两个依赖，注意排除spring相关，因为我们项目中已经有了。版本2.7，不要太低。

```
<!--swagger2 springfox集成swagger2-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.7.0</version>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-core</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-beans</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-context</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-context-support</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-aop</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-tx</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-orm</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-jdbc</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-web</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-webmvc</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.springframework</groupId>
                    <artifactId>spring-oxm</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.7.0</version>
        </dependency>
```
> 2.建立swagger配置类，由于是分布式系统，所以在每个系统中都要存在各自的文件

```
package edu.whut.pocket.base.util;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;
/**
 * Author: zouy
 * Unit: D9lab
 * Date: 2018-05-02 16:52
 */
@EnableWebMvc//必须存在
@EnableSwagger2//必须存在
@Configuration//必须存在
//必须存在 扫描的API Controller package name 也可以直接扫描class (basePackageClasses)
@ComponentScan(basePackages = {"edu.whut.pocket.*.controller"})
public class Swagger2Config {
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                ;
    }
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("口袋光谷-区块链商城")
                .description("用户子系统(设置,权限等)接口文档")
                .termsOfServiceUrl("http://localhost:8080/v2/api-docs")
                .version("1.0.0")
                .build();
    }
}
```
`注意该类要能被spring扫描到，需在sping配置文件中配置包扫描。`
> 3.在类上使用@Api等注解配置，在方法上使用@ApiOperation等注解配置

我们在AdminController类上使用@Api注解描述该类在接口文档中的相应信息。
```
@Api(tags = {"管理员"}, description = "登录|管理机构|分配权限|数据统计|推送短信", produces = MediaType.APPLICATION_JSON_VALUE)
@RestController
@RequestMapping("/user/admin")
public class AdminController {
 ......
}
```
我们在AdminController类中的getOneAdmin方法上使用@ApiOperation描述改方法在接口文档中的相应信息。
```
@ApiOperation(value = "获取账号", httpMethod = "POST", produces = MediaType.APPLICATION_JSON_VALUE,notes = "获取一个管理员,内容管理员或商户管理员"
)
    @RequestMapping(value = "/getOneAdmin", method = RequestMethod.POST)
    public Map getOneAdmin(HttpServletRequest request, int userId, int id) throws Exception {
        ResponseMap map = ResponseMap.getInstance();
        Admin admin = adminService.getOneAdmin(id);
        if(admin == null){
            return map.putFailure("获取失败", -1);
        }
        return map.putValue(admin, "获取成功");
    }
```
