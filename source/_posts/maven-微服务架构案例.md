---
title: maven-微服务架构案例
top: false
cover: false
toc: true
mathjax: true
tags:
  - maven
categories:
  - 工作
typora-root-url: ../
date: 2022-08-09 10:35:10
password:
summary:
---





# 第一节 创建工程

## 1、创建工程

![images](data:image/png;base64,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)

| 工程名                          | 地位   | 说明               |
| ------------------------------- | ------ | ------------------ |
| **demo-imperial-court-ms-show** | 父工程 | 总体管理各个子工程 |
| demo01-imperial-court-gateway   | 子工程 | 网关               |
| **demo02-user-auth-center**     | 子工程 | 用户中心           |
| demo03-emp-manager-center       | 子工程 | 员工数据维护中心   |
| demo04-memorials-manager-center | 子工程 | 奏折数据维护中心   |
| demo05-working-manager-center   | 子工程 | 批阅奏折工作中心   |
| **demo06-mysql-data-provider**  | 子工程 | MySQL 数据提供者   |
| demo07-redis-data-provider      | 子工程 | Redis 数据提供者   |
| **demo08-base-api**             | 子工程 | 声明 Feign 接口    |
| **demo09-base-entity**          | 子工程 | 实体类             |
| **demo10-base-util**            | 子工程 | 工具类             |

## 2、建立工程间依赖关系

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img002.a402cf04.png)





# 第二节 父工程管理依赖

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img003.223b16ed.png)

```xml
<dependencyManagement>
    <dependencies>

        <!-- SpringCloud 依赖导入 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Hoxton.SR9</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <!-- SpringCloud Alibaba 依赖导入 -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.6.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <!-- SpringBoot 依赖导入 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.3.6.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <!-- 通用 Mapper 依赖 -->
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.1.5</version>
        </dependency>

        <!-- Druid 数据源依赖 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>

        <!-- JPA 依赖 -->
        <dependency>
            <groupId>javax.persistence</groupId>
            <artifactId>persistence-api</artifactId>
            <version>1.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```



# 第三节 打基础

## 1、demo10-base-util

### ①ImperialCourtConst 常量类

```java
public class ImperialCourtConst {

    public static final String LOGIN_FAILED_MESSAGE = "账号、密码错误，不可进宫！";
    public static final String ACCESS_DENIED_MESSAGE = "宫闱禁地，不得擅入！";

}
```



### ②字符串加密工具类

```java
public class MD5Util {

    /**
     * 针对明文字符串执行MD5加密
     * @param source
     * @return
     */
    public static String encode(String source) {

        // 1.判断明文字符串是否有效
        if (source == null || "".equals(source)) {
            throw new RuntimeException("用于加密的明文不可为空");
        }

        // 2.声明算法名称
        String algorithm = "md5";

        // 3.获取MessageDigest对象
        MessageDigest messageDigest = null;
        try {
            messageDigest = MessageDigest.getInstance(algorithm);
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }

        // 4.获取明文字符串对应的字节数组
        byte[] input = source.getBytes();

        // 5.执行加密
        byte[] output = messageDigest.digest(input);

        // 6.创建BigInteger对象
        int signum = 1;
        BigInteger bigInteger = new BigInteger(signum, output);

        // 7.按照16进制将bigInteger的值转换为字符串
        int radix = 16;
        String encoded = bigInteger.toString(radix).toUpperCase();

        return encoded;
    }

}
```



### ③登录失败异常

```java
public class LoginFailedException extends RuntimeException {

    public LoginFailedException() {
    }

    public LoginFailedException(String message) {
        super(message);
    }

    public LoginFailedException(String message, Throwable cause) {
        super(message, cause);
    }

    public LoginFailedException(Throwable cause) {
        super(cause);
    }

    public LoginFailedException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace) {
        super(message, cause, enableSuppression, writableStackTrace);
    }
}
```



### ④远程方法调用统一返回结果

```java
/**
 * 统一整个项目中远程方法调用返回的结果
 * @author Lenovo
 *
 * @param <T>
 */
public class ResultEntity<T> {
	
	public static final String SUCCESS = "SUCCESS";
	public static final String FAILED = "FAILED";
	
	// 用来封装当前请求处理的结果是成功还是失败
	private String result;
	
	// 请求处理失败时返回的错误消息
	private String message;
	
	// 要返回的数据
	private T data;
	
	/**
	 * 请求处理成功且不需要返回数据时使用的工具方法
	 * @return
	 */
	public static <Type> ResultEntity<Type> successWithoutData() {
		return new ResultEntity<Type>(SUCCESS, null, null);
	}
	
	/**
	 * 请求处理成功且需要返回数据时使用的工具方法
	 * @param data 要返回的数据
	 * @return
	 */
	public static <Type> ResultEntity<Type> successWithData(Type data) {
		return new ResultEntity<Type>(SUCCESS, null, data);
	}
	
	/**
	 * 请求处理失败后使用的工具方法
	 * @param message 失败的错误消息
	 * @return
	 */
	public static <Type> ResultEntity<Type> failed(String message) {
		return new ResultEntity<Type>(FAILED, message, null);
	}
	
	public ResultEntity() {
		
	}

	public ResultEntity(String result, String message, T data) {
		super();
		this.result = result;
		this.message = message;
		this.data = data;
	}

	@Override
	public String toString() {
		return "ResultEntity [result=" + result + ", message=" + message + ", data=" + data + "]";
	}

	public String getResult() {
		return result;
	}

	public void setResult(String result) {
		this.result = result;
	}

	public String getMessage() {
		return message;
	}

	public void setMessage(String message) {
		this.message = message;
	}

	public T getData() {
		return data;
	}

	public void setData(T data) {
		this.data = data;
	}

}
```



## 2、demo09-base-entity

### ①引入依赖

```xml
<dependency>
    <groupId>javax.persistence</groupId>
    <artifactId>persistence-api</artifactId>
</dependency>
```



### ②创建实体类

在 MySQL 数据提供服务中用到的通用 Mapper 技术需要借助 **@Table 注解**将实体类和数据库表关联起来。

```java
@Table(name = "t_emp")
public class Emp implements Serializable {

    private Integer empId;

    private String empName;

    private String empPosition;

    private String loginAccount;

    private String loginPassword;

    public Integer getEmpId() {
        return empId;
    }

    public void setEmpId(Integer empId) {
        this.empId = empId;
    }

    public String getEmpName() {
        return empName;
    }

    public void setEmpName(String empName) {
        this.empName = empName == null ? null : empName.trim();
    }

    public String getEmpPosition() {
        return empPosition;
    }

    public void setEmpPosition(String empPosition) {
        this.empPosition = empPosition == null ? null : empPosition.trim();
    }

    public String getLoginAccount() {
        return loginAccount;
    }

    public void setLoginAccount(String loginAccount) {
        this.loginAccount = loginAccount == null ? null : loginAccount.trim();
    }

    public String getLoginPassword() {
        return loginPassword;
    }

    public void setLoginPassword(String loginPassword) {
        this.loginPassword = loginPassword == null ? null : loginPassword.trim();
    }
}
```





# 第四节 用户登录认证服务：提供端

## 1、总体分析

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img004.e6803319.png)



## 2、注册中心

在本地启动 Nacos 注册中心：

```sh
d:\software\nacos\bin>startup.cmd -m standalone
```





## 3、声明接口，暴露服务

### ①接口文档

[点此查看接口文档](http://heavy_code_industry.gitee.io/code_heavy_industry/pro002-maven/chapter08/api.html)



### ② Feign 接口代码

#### [1]接口位置

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img005.4502365c.png)



#### [2]引入依赖

```xml
<!-- OpenFeign 专用依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>

<!-- 提供 Emp 实体类使用 -->
<dependency>
    <groupId>com.atguigu.demo</groupId>
    <artifactId>demo09-base-entity</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>

<!-- 提供 ResultEntity 工具类使用 -->
<dependency>
    <groupId>com.atguigu.demo</groupId>
    <artifactId>demo10-base-util</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```



#### [3]接口代码

**注意**：@FeignClient 注解中指定的是提供服务的微服务名称，要和注册中心注册的一致

```java
// @FeignClient 注解将当前接口标记为服务暴露接口
// name 属性：指定被暴露服务的微服务名称
@FeignClient(name = "demo06-mysql-data-provider")
public interface MySQLProvider {

    @RequestMapping("/remote/get/emp/by/login/info")
    ResultEntity<Emp> getEmpByLoginInfo(

            // @RequestParam 无论如何不能省略
            @RequestParam("loginAccount") String loginAccount,
            @RequestParam("loginPassword") String loginPassword);
}
```



## 4、实现接口

### ①所在工程

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img006.4bee7822.png)

### ②引入依赖

```xml
<!-- Nacos 服务注册发现启动器 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>

<!--通用mapper启动器依赖-->
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
</dependency>

<!--mysql驱动-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>

<!--druid启动器依赖-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
</dependency>

<!--web启动器依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!--编码工具包-->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
</dependency>

<!--单元测试启动器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>

<!--热部署 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
<dependency>
    <groupId>com.atguigu.demo</groupId>
    <artifactId>demo09-base-entity</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>

<dependency>
    <groupId>com.atguigu.demo</groupId>
    <artifactId>demo10-base-util</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```



### ③Java 代码

#### [1]总体结构

![images](data:image/png;base64,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)

#### [2]EmpMapper

继承 tk.mybatis.mapper.common.Mapper 后就可以使用通用 Mapper 提供的常规代码实现。除非有非常规需求，否则我们自己什么都不用写。

![images](data:image/png;base64,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)

```java
public interface EmpMapper extends Mapper<Emp> {
}
```



#### [3]Service 接口

![images](data:image/png;base64,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)

```java
public interface EmpService {
    Emp getEmpByLoginInfo(String loginAccount, String loginPassword);
}
```



#### [4]Service 实现

```java
@Service
@Transactional(propagation = Propagation.REQUIRES_NEW, readOnly = true)
public class EmpServiceImpl implements EmpService {

    @Autowired
    private EmpMapper empMapper;

    @Override
    public Emp getEmpByLoginInfo(String loginAccount, String loginPassword) {

        String encodedLoginPassword = MD5Util.encode(loginPassword);

        Example example = new Example(Emp.class);

        example
                .createCriteria()
                .andEqualTo("loginAccount", loginAccount)
                .andEqualTo("loginPassword", encodedLoginPassword);

        List<Emp> empList = empMapper.selectByExample(example);

        if (empList == null || empList.size() == 0) {
            throw new LoginFailedException(ImperialCourtConst.LOGIN_FAILED_MESSAGE);
        }

        return empList.get(0);
    }
}
```



#### [5]EmpController

![images](data:image/png;base64,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)

```java
@RestController
public class EmpController {

    @Autowired
    private EmpService empService;

    @RequestMapping("remote/get/emp/by/login/info")
    ResultEntity<Emp> getEmpByLoginInfo(
            @RequestParam("loginAccount") String loginAccount,
            @RequestParam("loginPassword") String loginPassword) {

        try {
            Emp emp = empService.getEmpByLoginInfo(loginAccount, loginPassword);

            return ResultEntity.successWithData(emp);

        } catch (Exception e) {
            e.printStackTrace();
            String message = e.getMessage();
            return ResultEntity.failed(message);
        }

    }

}
```



#### [6]主启动类

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img019.e387b0f7.png)

```java
// 为了让当前微服务对接（注册或发现服务）注册中心
@EnableDiscoveryClient

// SpringBoot 标配注解
@SpringBootApplication

// 扫描通用 Mapper 的 Mapper 接口所在包
// 这个注解全类名：tk.mybatis.spring.annotation.MapperScan
// basePackage 属性：指定要扫描的 Mapper 接口所在的包
@MapperScan(basePackages = "com.atguigu.imperial.court.mapper")
public class MainType {

    public static void main(String[] args) {
        SpringApplication.run(MainType.class, args);
    }

}
```



### ④YAML 配置文件

![images](data:image/png;base64,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)

```yaml
server:
  port: 10001
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://192.168.198.100:3306/db_imperial_court
    username: root
    password: atguigu
    type: com.alibaba.druid.pool.DruidDataSource
  application:
    name: demo06-mysql-data-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
```





# 第五节 用户登录认证服务：消费端

## 1、所在工程

![images](data:image/png;base64,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)

## 2、引入依赖

```xml
<!-- Nacos 服务注册发现启动器 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>

<!-- web启动器依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- 视图模板技术 thymeleaf -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<dependency>
    <groupId>com.atguigu.demo</groupId>
    <artifactId>demo08-base-api</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```



## 3、YAML 配置文件

![images](data:image/png;base64,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)

```yaml
server:
  port: 10002
spring:
  application:
    name: demo02-user-auth-center
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
```



> TIP
>
> 就 Thymeleaf 而言，有两个常用属性，但我们全部都使用的是默认值，所以可以省略。

## 4、显示首页

### ①配置 view-controller

![images](data:image/png;base64,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)

```java
@SpringBootConfiguration
public class DemoConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("portal");
    }
}
```



### ②Thymeleaf 视图模板页面

![images](data:image/png;base64,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)

```html
<!DOCTYPE html>
<html lang="en" xml:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>

<form th:action="@{/consumer/do/login}" method="post">
    <p style="color: red;font-weight: bold;" th:if="${not #strings.isEmpty(authMessage)}" th:text="${authMessage}">
        这里根据条件显示登录失败消息</p>
    <p style="color: red;font-weight: bold;" th:if="${not #strings.isEmpty(systemMessage)}" th:text="${systemMessage}">
        这里根据条件显示系统消息</p>
    账号：<input type="text" name="loginAccount"/><br/>
    密码：<input type="password" name="loginPassword"><br/>
    <button type="submit">进宫</button>
</form>
</body>
</html>
```



## 5、登录验证

### ①流程图

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img020.c5cd1fc4.png)

### ②主启动类

**注意**：一定要标记 @EnableFeignClients 注解。

![images](data:image/png;base64,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)

```java
@EnableFeignClients
@EnableDiscoveryClient
@SpringBootApplication
public class MainType {

    public static void main(String[] args) {
        SpringApplication.run(MainType.class, args);
    }

}
```



### ③AuthController

#### [1]装配远程接口分析

![images](data:image/png;base64,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)

```java
@Controller
public class AuthController {

    // 1、本地使用 @Autowired 注解装配远程接口类型即可实现方法的远程调用，
    // 看起来就像是调用本地方法一样，我们管这种特性叫方法的声明式远程调用。
    // 2、凭啥通过 @Autowired 注解就能够导入远程接口对应的 bean
    //      ①当前环境包含 Feign 相关 jar 包。
    //      ②当前微服务的主启动类上标记 @EnableFeignClients
    //      ③符合 SpringBoot 自动扫描包的约定规则：默认情况下主启动类所在的包、以及主启动类所在包的子包都会被自动扫描
    //          主启动类所在包：     com.atguigu.imperial.court
    //          被扫描的接口所在的包：com.atguigu.imperial.court.api
    @Autowired
    private MySQLProvider mySQLProvider;

}
```



#### [2]执行登录验证的方法

```java
@RequestMapping("/consumer/do/login")
public String doLogin(@RequestParam("loginAccount") String loginAccount,
                      @RequestParam("loginPassword") String loginPassword, HttpSession session, Model model) {

    // 1、调用远程接口根据登录账号、密码查询 Emp 对象
    ResultEntity<Emp> resultEntity = mySQLProvider.getEmpByLoginInfo(loginAccount, loginPassword);

    // 2、验证远程接口调用是否成功
    String result = resultEntity.getResult();
    
    if ("SUCCESS".equals(result)) {

        // 3、从 ResultEntity 中获取查询得到的 Emp 对象
        Emp emp = resultEntity.getData();
        
        // 4、将 Emp 对象存入 Session 域
        session.setAttribute("loginInfo", emp);

        // 5、前往 target 页面
        return "target";
    } else {
        
        // 6、获取失败消息
        String message = resultEntity.getMessage();
        
        // 7、将失败消息存入模型
        model.addAttribute("message", message);
        
        // 8、回到登录页面
        return "index";

    }
}
```





# 第六节 部署运行

## 1、最终目标

![images](data:image/png;base64,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)

## 2、微服务打包

### ①修改 MySQL 连接信息

![images](data:image/png;base64,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)

```yaml
server:
  port: 10001
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    
    # 当前微服务和 MySQL 位于同一个服务器上，需要把访问地址改成 localhost
    url: jdbc:mysql://localhost:3306/db_imperial_court
    username: root
    password: atguigu
    type: com.alibaba.druid.pool.DruidDataSource
  application:
    name: demo06-mysql-data-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
```



### ②在父工程执行 install 命令

#### [1]Why parent？工程间关系梳理

正确的安装顺序：

- ①父工程：pro07-demo-imperial-court-micro-service
- ②被依赖的 module：demo10-base-util 或 demo09-base-entity
- ③当前 module：demo06-mysql-data-provider

![images](data:image/png;base64,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)

#### [2]执行命令

![images](data:image/png;base64,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)

### ③生成微服务可运行 jar 包

#### [1]应用微服务打包插件

可以以 SpringBoot 微服务形式直接运行的 jar 包包括：

- 当前微服务本身代码
- 当前微服务所依赖的 jar 包
- 内置 Tomcat（Servlet 容器）
- 与 jar 包可以通过 java -jar 方式直接启动相关的配置

要加入额外的资源、相关配置等等，仅靠 Maven 自身的构建能力是不够的，所以要通过 build 标签引入下面的插件。

```xml
<!-- build 标签：用来配置对构建过程的定制 -->
<build>
    <!-- plugins 标签：定制化构建过程中所使用到的插件 -->
	<plugins>
        <!-- plugin 标签：一个具体插件 -->
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
		</plugin>
	</plugins>
</build>
```

加入这个插件后的效果：

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img026.51697e69.png)

提示：IDEA 对于我们这里 build 标签里加入的 plugin 的配置没有能够很好的识别到插件的版本。如果我们能够保证其它操作都正常执行完成，准备工作都准备好了，那么这里我们判定是 IDEA 识别能力不足导致。一切以实际执行的结果为准：**运行结果是最高权威**

请对 demo02-user-auth-center 和 demo06-mysql-data-provider 都添加上面的 build 配置。

#### [2]执行插件目标

请对 demo02-user-auth-center 和 demo06-mysql-data-provider 都执行下面的命令：

- clean 子命令：清理之前构建的结果
- package 子命令：我们真正要调用的 spring-boot:repackage 要求必须将当前微服务本身的 jar 包提前准备好，所以必须在它之前执行 package 子命令。
- spring-boot:repackage 子命令：调用 spring-boot 插件的 repackage 目标
- -Dmaven.test.skip=true 参数：跳过测试

```sh
mvn clean package spring-boot:repackage -Dmaven.test.skip=true
```



## 3、执行部署

### ①启动 Nacos

```sh
sh /opt/nacos/bin/startup.sh -m standalone
```



### ②上传微服务 jar 包

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img027.03f0c407.png)

### ②启动微服务

```sh
nohup java -jar demo06-mysql-data-provider-1.0-SNAPSHOT.jar>demo06.log 2>&1 &
nohup java -jar demo02-user-auth-center-1.0-SNAPSHOT.jar>demo02.log 2>&1 &
```



[nohup命令详解(opens new window)](http://heavy_code_industry.gitee.io/code_heavy_industry/pro006-Linux/lecture/chapter03/verse04-07-nohup.html)

