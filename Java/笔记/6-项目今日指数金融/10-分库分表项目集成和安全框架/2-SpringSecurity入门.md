#  认证授权-SpringSecurity

**学习目标**

- 了解认证授权基本概念及权限数据模型
- 了解常见的认证方式
- 了解什么是JWT及JWT的数据组成和使用
- 掌握SpringSecurity的基本入门操作

# 第二章  认证授权概述

## 1、认证授权概念介绍

### 1.1  什么是认证

​	在互联网中，我们每天都会使用到各种各样的APP和网站，在使用过程中通常还会遇到需要注册登录的情况，输入你的用户名和密码才能正常使用，也就是说成为这个应用的合法身份才可以访问应用的资源，这个过程就是认证。认证是为了保护系统的隐私数据与资源，用户的身份合法方可访问该系统的资源。

​	当然认证的方式有很多，常见的账号密码登录，手机验证码登录，指纹登录，刷脸登录等等。

​	简单说: 认证就是让系统知道我们是谁。

### 1.2 什么是授权

​	认证是为了保护身份的合法性，授权则是为了更细粒度的对数据进行划分，授权是在认证通过的前提下发生的。控制不同的用户能够访问不同的资源。

 	  **授权是用户认证通过后根据用户的权限来控制用户访问资源的过程**，拥有资源的访问权限则正常访问，没有权限则拒绝访问。

   例如视频网站的VIP用户，可以查看到普通用户看不到的资源信息。



## 2、权限数据模型

​	授权过程中，我们需要知道如何对用户访问的资源进行控制，需要了解一些简单的授权数据模型。

授权可以非常简单的理解成谁（Who）对什么资源（What）进行怎么样（How）的操作。

| 名词   | 含义             | 备注                                       |
| ---- | -------------- | ---------------------------------------- |
| Who  | 主体(Subject)    | 一般指用户，也可以是应用程序                           |
| What | 资源(Resource)   | 例如商品信息，订单信息，页面按钮或程序中的接口等信息               |
| How  | 权限(Permission) | 规定了用户或程序对资源操作的许可。例如普通用户只能查看订单，管理员可修改或删除订单，这是因为普通用户和管理员用户对订单资源的操作权限不一样。 |

**1.2.1 主体、资源、权限的关系图：** 

<img src="assets/image-20210116112712514.png" alt="image-20210116112712514" style="zoom:67%;" />  

主体、资源、权限相关的数据模型如下： 

A. 主体（用户id、账号、密码、...） 

B. 资源（资源id、资源名称、访问地址、...） 

C. 权限（权限id、权限标识、权限名称、资源id、...） 

D. 主体和权限关系（用户id、权限id、...）

显然，多个主体与资源之间的权限关联存在冗余，可引入角色关联对应的权限信息；

**1.2.2 主体、资源、权限的表结构关系：**  

<img src="assets/image-20210116114510176.png" alt="image-20210116114510176" style="zoom:67%;" /> 



​	我们发现权限中包含了一个资源ID，多个权限可指向一个资源，我们是否可以直接**在权限信息中把资源信息包含进来**呢？当然，这也是很多企业开发中的做法，将权限和资源合并为 权限(权限ID、权限标识、权限名称、资源名称、资源访问地址、...)

<img src="assets/image-20210116114816668.png" alt="image-20210116114816668" style="zoom:67%;" /> 



## 3、RBAC权限模型

### 3.1 介绍

​	如何实现授权？业界通常基于RBAC模型（Role-Based Access Control -> 基于角色的访问控制）实现授权。 RBAC认为授权实际就是who,what,how三者之间的关系(3W)，即who对what进行how的操作。

### 3.2 基于角色访问控制

RBAC基于角色的访问控制（Role-Based Access Control）是按角色进行授权，比如：主体的角色为总经理可以查询企业运营报表，查询员工工资信息等，访问控制流程如下： 

<img src="assets/image-20210116122425370.png" alt="image-20210116122425370" style="zoom: 67%;" /> 

根据上图中的判断逻辑，授权代码可表示如下： 

```java
if(主体.hasRole("总经理角色标识")){
    //查询工资 
}else{
    //权限不足
}
```

如果上图中查询工资所需要的角色变化为总经理和部门经理，此时就需要修改判断逻辑为“判断用户的角色是否是 总经理或部门经理”，修改代码如下： 

```java
if(主体.hasRole("总经理角色标识") || 主体.hasRole("部门经理角色标识")){ 
	//查询工资 
}else{
    //权限不足
}
```

根据上边的例子发现，当需要修改角色的权限时就需要修改授权的相关代码，系统可扩展性差。 

### 3.3 基于资源访问控制

RBAC基于资源的访问控制(Resource-Based Access Control)是按资源(或权限)进行授权。

<img src="assets/image-20210116122635809.png" alt="image-20210116122635809" style="zoom:67%;" /> 

同样是上面的需求，这时候我们的代码变成了

```
if(Subject.hasPermission("查询员工工资的权限标识")){
	// 查询员工工资
}
```

优点：

系统设计时定义好查询工资的权限标识，即使查询工资所需要的角色变化为总经理和部门经理也不需要修授权代码，**系统可扩展性强**。

## 4、常见认证方式

认证(登录)几乎是任何一个系统的标配，web 系统、APP、PC客户端等都需要注册、登录、授权。 

### 4.1 Cookie-Session

​	早期互联网以 web 为主，客户端是浏览器，所以 Cookie-Session 方式最那时候最常用的方式，直到现在，一些 web 网站依然用这种方式做认证；

**认证过程大致如下：**

A. 用户输入用户名、密码或者用短信验证码方式登录系统；

B. 服务端验证后，创建一个 Session 记录用户登录信息 ，并且将 SessionID 存到 cookie，响应回浏览器；

C. 下次客户端再发起请求，自动带上 cookie 信息，服务端通过 cookie 获取 Session 信息进行校验；

<img src="assets/image-20210116130304029.png" alt="image-20210116130304029" style="zoom:67%;" /> 

**弊端**

- 只能在 web 场景下使用，如果是 APP 中，不能使用 cookie 的情况下就不能用了；
- 即使能在 web 场景下使用，也要考虑跨域问题，因为 cookie 不能跨域；（域名或者ip一致，端口号一致，协议要一致）
- cookie 存在 CSRF（跨站请求伪造）的风险；
- 如果是分布式服务，需要考虑 Session 同步（同步）问题；
- session-cookie机制是有状态的方式（后端保存主题的用户信息-浪费后端服务器内存）


### 4.2 jwt令牌无状态认证

JSON Web Token（JWT-字符串）是一个非常轻巧的规范。这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的信息。

**认证过程: **

A. 依然是用户登录系统；

B. 服务端验证，并通过指定的算法生成令牌返回给客户端;

C. 客户端拿到返回的 Token，存储到 local storage/session Storate/Cookie中；

D. 下次客户端再次发起请求，将 Token 附加到 header 中；

E. 服务端获取 header 中的 Token ，通过相同的算法对 Token 进行验证，如果验证结果相同，则说明这个请求是正常的，没有被篡改。这个过程可以完全不涉及到查询 Redis 或其他存储；

<img src="assets/image-20210116135838264.png" alt="image-20210116135838264" style="zoom: 80%;" />  

**优点**

A. 使用 json 作为数据传输，有广泛的通用型，并且体积小，便于传输；

B. 不需要在服务器端保存相关信息，节省内存资源的开销；

C. jwt 载荷部分可以存储业务相关的信息（非敏感的），例如用户信息、角色等；

## 5 常见技术实现

| 技术                 | 概述                                       |
| ------------------ | ---------------------------------------- |
| Apache Shiro       | Apache旗下的一款安全框架                          |
| **SpringSecurity** | Spring家族的一部分, Spring体系中提供的安全框架, 包含认证、授权两个大的部分 |
| CAS                | CAS是一个单点登录(SSO)服务，开始是由耶鲁大学的一个组织开发，后来归到apereo去管 |
| 自行实现               | 自行通过业务代码实现（基于filter过滤器或者springmvc拦截器+AOP）, 实现繁琐, 代码量大 |

# 第二章 JWT入门

## 1.Jwt介绍

### 1.1 JWT简介

​	JSON Web Token（JWT）是为了在网络应用环境间传递声明而执行的一种基于JSON的开放标准。这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的信息。该token被设计为紧凑且安全的，特别适用于前后端无状态认证的场景。

### 1.2.Jwt组成

- **头部（Header）（非敏感）**
  - 头部用于描述关于该JWT的最基本的信息，例如数据类型以及签名所用的算法等，本质是一个JSON格式对象；
  - 举例说明
    - {"typ":"JWT","alg":"HS256"}  解释：在头部指明了签名算法是HS256算法，整个JSON对象被BASE64编码形成JWT头部字符串信息；
    - BASE64编码详见：https://tool.oschina.net/encrypt，编码后的字符串：eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9
    - 值得注意的是BASE64不是加密算法，可进行正向编码和反向解码处理;
- **载荷（playload）（非敏感数据）**
  - 载荷就是存放有效信息的地方，该部分的信息是可以自定义的；
  - 载荷payload格式:{"sub":"1234567890","name":"John Doe","admin":true}
  - 载荷相关的JSON对象经过BASE64编码形成JWT第二部分：eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG7CoERvZSIsImFkbWluIjp0cnVlfQ==
- **签证（signature）**
  - jwt的第三部分是一个签证信息，这个签证信息由三部分组成：<u>签名算法( header (base64后的).payload (base64后的) . secret )</u>
  - 这个部分需要base64加密后的header和base64加密后的payload使用.连接组成的字符串，然后通过header中声明的加密方式进行加盐secret秘钥组合加密，然后就构成了jwt的第三部分：TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ

最后将这三部分用●连接成一个完整的字符串,构成了最终的jwt:

**eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9**.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG7CoERvZSIsImFkbWluIjp0cnVlfQ==.**TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ**

## 2、JWT使用

### 2.1 工程引入JWT依赖

~~~XML
<dependencies>
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt</artifactId>
        <version>0.9.1</version>
    </dependency>
</dependencies>
~~~

### 2.2 生成JWT令牌

~~~java
@Test
public void testGenerate(){
    String compact = Jwts.builder()
        .setId(UUID.randomUUID().toString())//设置唯一标识
        .setSubject("JRZS") //设置主题
        .claim("name", "nineclock") //自定义信息
        .claim("age", 88) //自定义信息
        .setExpiration(new Date()) //设置过期时间
        .setIssuedAt(new Date()) //令牌签发时间
        .signWith(SignatureAlgorithm.HS256, "itheima")//签名算法, 秘钥
        .compact();
    System.out.println(compact);
}
~~~

### 2.3 JWT令牌校验

~~~java
@Test
public void testVerify(){
    String jwt = "eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiI5MzljNjU4MC0yMTQyLTRlOWEtYjcxOC0yNzlmNzRhODVmNDMiLCJzdWIiOiJOSU5FQ0xPQ0siLCJuYW1lIjoibmluZWNsb2NrIiwiYWdlIjo4OCwiaWF0IjoxNjE3MDMxMjUxfQ.J-4kjEgyn-Gkh0ZuivUCevrzDXt0K9bAyF76rn1BfUs";
    Claims claims = Jwts.parser().setSigningKey("itheima").parseClaimsJws(jwt).getBody();
    System.out.println(claims);
}
~~~

当我们对令牌进行任何部分(header , payload , signature)任何部分进行篡改, 都会造成令牌解析失败 ;

## 3、 JWT在前端保存方案

后端基于JWT生成的Token信息在前端有如下保存方式：

- LocalStorage
- SessionStorage
- cookie
- 页面中
- 其它

以LocalStorage为例：

~~~html
<script>
	//保存信息
    localStorage.setItem("name", "ithiema");
    //获取信息
    alert(localStorage.getItem("name"))
    //删除信息
    localStorage.removeItem("name");
</script>
~~~



# 第三章 SpringSecurity专题

## 1、SpringSecurity快速入门

### 1.1 SpringSecurity简介

<img src="assets/image-20210117104427731.png" alt="image-20210117104427731" style="zoom:50%;" /> 

- Spring Security是基于Spring的安全框架,提供了包含认证和授权的落地方案；
- Spring Security底层充分利用了Spring IOC和AOP功能，为企业应用系统提供了声明式安全访问控制解决方案；
- SpringSecurity可在Web请求级别和方法调用级别处理身份认证和授权，为应用系统提供声明式的安全访问控制功能；

官网地址: https://spring.io/projects/spring-security

### 1.2 SpringSecurity工程搭建

导入基础工程：day10-分库分表项目集成和安全框架\资料\security资料\基础代码\security_demo

**A. 创建工程引入依赖**

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
    <relativePath/>
</parent>
<dependencies>
    <!-- web起步依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- springBoot整合Security -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
	<!-- lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
    </dependency>
</dependencies>
```

**B. 引导类**

```java
@SpringBootApplication
public class MySecurityApplication {
    public static void main(String[] args) {
        SpringApplication.run(MySecurityApplication.class,args);
    }
}
```

**C. Controller**

```java
@RestController
public class UserController {
    @GetMapping("/hello")
    public String hello(){
        return "hello security";
    }
    @GetMapping("/say")
    public String say(){
        return "say security";
    }
    @GetMapping("/register")
    public String register(){
        return "register security";
    }
}
```

**D. 测试**

访问: http://localhost:8080/hello

会自动拦截，并跳转到登录页面（SpringSecurity提供），登录之后才可以访问； 而登录的用户名和密码都是SpringSecurity中内置的默认的用户名密码， **用户名为user** ， 密码为控制台输出的一段随机数；

![1642688262464](assets/1642688262464.png)

效果：

![image-20210117114009840](assets/image-20210117114009840.png) 

登录成功之后，会自动跳转到之前访问的地址： 

![image-20210117114254502](assets/image-20210117114254502.png) 

注意：

~~~properties
# 我们也可在配置文件中配置用户名和密码，实际开发中密码不应明文配置
spring.security.user.name=user
spring.security.user.password=6666
~~~

## 2、SpringSecurity自定义认证配置

​	在上述入门程序中, 用户名、密码都是框架默认帮我们生成的, 方式不够友好，SpringSecurity也为我们提供了通过配置的形式声明合法的账户信息的方式。

**A.声明配置类，定义用户名密码信息** 

```java
@Configuration
@EnableWebSecurity//开启web安全设置生效
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    /**
     * 构建认证服务，并将对象注入spring IOC容器，用户登录时，会调用该服务进行用户合法信息认证
     * @return
     */
    @Bean
    public UserDetailsService userDetailsService(){
        //从内存获取用户认证信息的服务类（了解）后期用户的信息要从表中获取
        InMemoryUserDetailsManager inMemoryUserDetailsManager = new InMemoryUserDetailsManager();
        //构建用户,真实开发中用户信息要从数据库加载构建
        UserDetails u1 = User
                .withUsername("itcast")
                .password("{noop}123456")//{noop}:no operration--》表示登录时对避免不做任何操作，说白了就是明文比对
                .authorities("P5", "ROLE_ADMIN")//用户的权限信息
                .build();
        UserDetails u2 = User
                .withUsername("itheima")
                .password("{noop}123456")
                .authorities("P7", "ROLE_SELLER","ROLE_ADMIN")//如果角色也作为一种权限资源，则角色名称的前缀必须加ROLE_
                .build();
        inMemoryUserDetailsManager.createUser(u1);
        inMemoryUserDetailsManager.createUser(u2);
        return inMemoryUserDetailsManager;
    }
}
```

> 说明：
>
> 1.在userDetailsService()方法中 返回了一个UserDetailsService对象给spring容器管理，当用户发生登录认证行为时，Spring Security底层会自动调用UserDetailsService类型bean提供的用户信息进行合法比对，如果比对成功则资源放行，否则就认证失败；
>
> 2.当前暂时使用InMemoryUserDetailsManager实现类，后续我们也可手动实现UserDetailsService接口，做最大程度的自定义；

**B.测试配置账户信息**

​	通过测试，配置的账户和密码信息都是有效的。



## 4、SpringSecurity自定义授权配置

​	经过上一小结配置，我们发现用户认证通过后，资源是都可被访问的。如果我们想为不同的用户指定不同的访问资源，该如何实现呢？

接下来，我们通过配置为不同用户访问授权。

### 4.1  基于编码方式定义授权

```java
@Configuration
@EnableWebSecurity
//@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {
	
    @Bean
    @Override
    public UserDetailsService userDetailsService(){
        InMemoryUserDetailsManager inMemoryUserDetailsManager = new InMemoryUserDetailsManager();
        inMemoryUserDetailsManager.createUser(User.withUsername("itcast").password("{noop}123456").authorities("P1","ROLE_ADMIN").build());
        inMemoryUserDetailsManager.createUser(User.withUsername("itheima").password("{noop}123456").authorities("O1","ROLE_SELLER").build());
        return inMemoryUserDetailsManager;
    }
	
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()//开启默认form表单登录方式
                .and()
                .logout()//登出用默认的路径登出 /logout
                .permitAll()//允许所有的用户访问登录或者登出的路径
                .and()
                  .csrf().disable()//启用CSRF,防止CSRF攻击
                .authorizeRequests()//授权方法，该方法后有若干子方法进行不同的授权规则处理
                //允许所有账户都可访问（不登录即可访问）,同时可指定多个路径
                .antMatchers("/register").permitAll()
                //开发方式1：基于配置
//                .antMatchers("/a1","/a2").hasRole("seller")//拥有seller角色的用户可访问a1和a2资源
                //拥有指定的任意角色都可以访问对应资源
//                .antMatchers("/b1").hasAnyRole("manager1","manager2")
                //用户任意指定的aa bb都可以访问c1资源
//                .antMatchers("/c1").hasAnyAuthority("aa","bb")
//                .antMatchers("/d").denyAll()//拒绝任意用户访问
//                .antMatchers("/e").anonymous()//允许匿名访问
                //指定IP可以访问
//                .antMatchers("/f").hasIpAddress("localhost/82")
                .antMatchers("/hello").hasAuthority("P5") //具有P5权限才可以访问
                .antMatchers("/say").hasRole("ADMIN") //具有ROLE_ADMIN 角色才可以访问
                .anyRequest().authenticated(); //除了上边配置的请求资源，其它资源都必须授权才能访问
    }
}
```

> CSRF（Cross-site request forgery）跨站请求伪造，也被称为"One Click Attack"或者 Session Riding，通常缩写为 CSRF 或者 XSRF，是一种对网站的恶意利用。



### 4.2 基于注解方式定义授权

​	基于注解的方式维护权限更偏向于集中化配置管理，但是这种方式带来的问题是随着权限管理的资源的增多，会导致权限配置变得十分的臃肿，所以SpringSecurity为我们提供了基于注解的配置方式。

**A.在配置类开启security的前置注解**

~~~java
//开启SpringSecurity相关注解支持
@EnableGlobalMethodSecurity(prePostEnabled = true)
~~~

**B.调整资源配置类**

~~~java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()//定义认证时使用form表单的方式提交数据
                .and()
                .logout()//登出用默认的路径登出 /logout
                .permitAll()//允许所有的用户访问登录或者登出的路径,如果 .anyRequest().authenticated()注释掉，则必须添加permitAll()，否则就不能正常访问登录或者登出的路径
                .and()
                .csrf().disable()
                .authorizeRequests()//授权方法，该方法后有若干子方法进行不同的授权规则处理
                //允许所有账户都可访问（不登录即可访问）,同时可指定多个路径
//                .antMatchers("/register").permitAll()
                //开发方式1：基于配置
//                .antMatchers("/a1","/a2").hasRole("seller")//拥有seller角色的用户可访问a1和a2资源
                //拥有指定的任意角色都可以访问对应资源
//                .antMatchers("/b1").hasAnyRole("manager1","manager2")
                //用户任意指定的aa bb都可以访问c1资源
//                .antMatchers("/c1").hasAnyAuthority("aa","bb")
//                .antMatchers("/d").denyAll()//拒绝任意用户访问
//                .antMatchers("/e").anonymous()//允许匿名访问
                //指定IP可以访问
//                .antMatchers("/f").hasIpAddress("localhost/82")
//                .antMatchers("/hello").hasAuthority("P5") //具有P5权限才可以访问
//                .antMatchers("/say").hasRole("ADMIN") //具有ROLE_ADMIN 角色才可以访问
               .anyRequest().authenticated(); //与注解@PermitAll冲突，需要注掉该行
    }
~~~



**C.注解配置资源权限**

在控制方法/URL的权限时, 可以通过配置类中配置的方式进行控制, 也可以使用 注解 @PreAuthorize 来进行控制, ==推荐使用注解:== 

```java
    /**
     *  @PreAuthorize:指在注解作用的方法执行之前，做权限校验
     *  @PostAuthorize:指在注解作用的方法执行之后，做权限校验
     *  @return
     */
    @PreAuthorize("hasAuthority('P5')")
//    @PostAuthorize("hasAuthority('P4')")
    @GetMapping("/hello")
    public String hello(){
        return "hello security";
    }
    @PreAuthorize("hasRole('ADMIN')")
    @GetMapping("/say")
    public String say(){
        return "say security";
    }
    @PermitAll//等价于antMatchers("/register").permitAll()//任何用户都可访问
    //@PreAuthorize("isAnonymous()")
    @GetMapping("/register")
    public String register(){
        return "register security";
    }
```

> 说明：使用@PreAuthorize,需要开启全局方法授权开关,加上注解@EnableGlobalMethodSecurity(prePostEnabled=true)



## 5、密码加密方式

经过上述的入门案例的演示，我们对SpringSecurity的使用有了一定的了解，但是在入门程序中存在两个问题: 

- 用户的认证密码采用是明文处理的，不安全 ;
- 用户名的密码直接通过程序硬编码，不够灵活 ；

接下来，我么学习下密码加密的各种方式。

### 5.1 可逆加密算法

加密后, 密文可以反向解密得明文原文;

**1). 对称加密**

<img src="assets/image-20210117130346946.png" alt="image-20210117130346946" style="zoom:67%;" /> 

指加密和解密使用相同密钥的加密算法。

优点: 对称加密算法的优点是算法公开、计算量小、加密速度快、加密效率高。
缺点: 没有非对称加密安全。

常见的对称加密算法：DES、3DES、DESX、Blowfish、RC4、RC5、RC6和AES

==说白了加密和解密都使用同一个秘钥处理；==



**2). 非对称加密**

<img src="assets/image-20210117130528290.png" alt="image-20210117130528290" style="zoom:67%;" /> 

指加密和解密使用不同密钥的加密算法，也称为公私钥加密。假设两个用户要加密交换数据，双方交换公钥，使用时一方用对方的公钥加密，另一方即可用自己的私钥解密。

加密和解密:

- 私钥加密，持有私钥或公钥才可以解密
- 公钥加密，持有私钥才可解密

优点: 非对称加密与对称加密相比，其安全性更好；
缺点: 非对称加密的缺点是加密和解密花费时间长、速度慢，只**适合对少量数据进行加密**。

### 5.2 不可逆加密算法

一旦加密就不能反向解密得到密码原文 。通常用于密码数据加密。

常见的不可逆加密算法有：  MD5 、SHA、HMAC

**1).MD5**

​	MD5是比较常见的加密算法，广泛的应用于软件开发中的密码加密，通过MD5生成的密文，是无法解密得到明文密码的。但是现在在大数据背景下，很多的网站通过大数据可以将简单的MD5加密的密码破解。

![image-20210117135021764](assets/image-20210117135021764.png) 

网址： https://www.cmd5.com/

可以在用户注册时，限制用户输入密码的长度及复杂度，从而增加破解难度。

**2). Bcrypt**

​	用户表的密码通常使用 MD5 等不可逆算法加密后存储，为防止彩虹表破解，会先使用一个特定的字符串（如域名）加密，然后再使用一个随机的 salt（盐值）加密。 特定字符串是程序代码中固定的，salt 是每个密码单独随机，一般给用户表加一个字段单独存储，比较麻烦。 	

BCrypt 算法将 salt 随机并混入最终加密后的密码，验证时也无需单独提供之前的salt，从而无需单独处理 salt 问题。

在SecurityConfig配置类配置密码加密匹配器：

~~~java
@Bean
public BCryptPasswordEncoder bCryptPasswordEncoder(){
	return new BCryptPasswordEncoder();
}
~~~

加密密码: 

```java
@Autoware
private BCryptPasswordEncoder bCryptPasswordEncoder;
@Test
public void test01(){
  for (int i = 0; i < 5; i++) {
	System.out.println(bCryptPasswordEncoder.encode("123456"));
	}
}
```

得到结果: 

```
$2a$10$C6YynRFeJsSy7D/kg3d30OWnuwko7KQIEK5JrX0mWND.vuz2TqwpK
$2a$10$aSJfxH2oBtopFMbkMJ.PQ.sbSBXJH9g.9bv1mCyte/BtcU9VTs7lG
$2a$10$nVoB.eV5Uhc9FNUC36Pn0OosGh7aKlp7Sjfxaiml8NCSJ6PX1q6.m
$2a$10$2RM3mRNjz1LoZ5eeLdj.Hu15vlWIIj2zJC09vwTevBlIi5rjJStam
$2a$10$c2sZT/LtM1ExWfZjO0yIPeTGSqMSlX7oi.SvliMbeZpT9Y4qIBDue
```

验证密码: 

```java
boolean matches = 
   bCryptPasswordEncoder.matches("123456", "$2a$10$c2sZT/LtM1ExWfZjO0yIPeTGSqMSlX7oi.SvliMbeZpT9Y4qIBDue");
System.out.println(matches);//返回值为true, 则代表验证通过; 反之, 验证不通过
```

> 注意：此时重新启动security_demo测试工程，security底层会自动调用PasswordEncoder类型bean进行密码校验处理；
>
> 加密原理详见：资料\security资料\SpringSecurity加密+认证授权(扩展)\SpringSecurity加密+认证授权原理.md

## 6、动态加载用户权限实现

### 6.1 密码加密处理

在配置类 SecurityConfig中配置Bean: 

```java
//配置认证信息 , 密码使用BCryptPasswordEncoder加密 ;
@Bean
public UserDetailsService userDetailsService(){
	InMemoryUserDetailsManager inMemoryUserDetailsManager = new InMemoryUserDetailsManager();
	inMemoryUserDetailsManager.createUser(User.withUsername("itcast")
	               .password("$2a$10$qcKkkvsoClF9tO8c9wlR/ebgU8VM39GP5ZUdsts.XSPDmE40l.BP2").authorities("P1","ROLE_ADMIN").build());
    inMemoryUserDetailsManager.createUser(User.withUsername("itheima")
                   .password("$2a$10$qcKkkvsoClF9tO8c9wlR/ebgU8VM39GP5ZUdsts.XSPDmE40l.BP2").authorities("O1","ROLE_SELLER").build());
    return inMemoryUserDetailsManager;
}
```

### 6.2 动态查询用户

上述的案例中, 用户名密码都是在代码中写死的, 现在实际项目中, 是需要动态从数据库查询;简易的数据库表如下: 

```sql
create database security_demo default charset=utf8mb4;
use security_demo;

CREATE TABLE `tb_user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(100) DEFAULT NULL,
  `password` varchar(100) DEFAULT NULL,
  `roles` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

INSERT INTO `tb_user` VALUES (1, 'itcast', '$2a$10$f43iK9zKD9unmgLao1jqI.VluZ.Rr/XijizVEA73HeOu9xswaUBXC', 'ROLE_ADMIN,P5');
INSERT INTO `tb_user` VALUES (2, 'itheima', '$2a$10$f43iK9zKD9unmgLao1jqI.VluZ.Rr/XijizVEA73HeOu9xswaUBXC', 'ROLE_SELLER,P7,ROLE_ADMIN');
```

![image-20210117143544431](assets/image-20210117143544431.png) 



**A. pom.xml**

```xml
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>2.1.4</version>
</dependency>

<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
</dependency>
```



**B. application.yml**

```yaml
# 应用名称
spring.application.name=security_test
# 应用服务 WEB 访问端口
server.port=8080

# 配置用户名和密码
#spring.security.user.name=user
#spring.security.user.password=6666

#下面这些内容是为了让MyBatis映射
#指定Mybatis的Mapper文件
mybatis.mapper-locations=classpath:mapper/*xml
#指定Mybatis的实体目录
mybatis.type-aliases-package=com.itheima.security.pojo

# 数据库驱动：
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# 数据库连接地址
spring.datasource.url=jdbc:mysql://192.168.188.130:3306/security_demo?serverTimezone=UTC
# 数据库用户名&密码：
spring.datasource.username=root
spring.datasource.password=root
```



**C. 实体类**

```java

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class TbUser implements Serializable {

    private Integer id;

    private String username;

    private String password;

    private String roles;

    private static final long serialVersionUID = 1L;
}
```



**D. mapper**

```java
@Mapper
public interface TbUserMapper {
    //根据用户名称查询用户信息
    TbUser findByUserName(@Param("userName") String userName);
}
```

xml:

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.itheima.security.mapper.TbUserMapper">

    <resultMap id="BaseResultMap" type="com.itheima.security.pojo.TbUser">
            <id property="id" column="id" jdbcType="INTEGER"/>
            <result property="username" column="username" jdbcType="VARCHAR"/>
            <result property="password" column="password" jdbcType="VARCHAR"/>
            <result property="roles" column="roles" jdbcType="VARCHAR"/>
    </resultMap>

    <sql id="Base_Column_List">
        id,username,password,
        roles
    </sql>
    <!--根据用户名称查询用户信息-->
    <select id="findByUserName" resultMap="BaseResultMap">
        select <include refid="Base_Column_List"/> from tb_user where username=#{userName}
    </select>
</mapper>
~~~



**D. 自定义UserDetailsService**

```java
/**
 * @author by itheima
 * @Description
 */
@Component
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private TbUserMapper tbUserMapper;

    @Override
    public UserDetails loadUserByUsername(String userName) throws UsernameNotFoundException {
        TbUser user = tbUserMapper.findByUserName(userName);
        if (user==null) {
            throw new UsernameNotFoundException("用户不存在");
        }
        //构建认证明细对象
        //获取用户权限
        List<GrantedAuthority> list = AuthorityUtils.commaSeparatedStringToAuthorityList(user.getRoles());
        User user1 = new User(user.getUsername(),user.getPassword(),list);
        return user1;
    }
}
```

> 注意：
>
> 1.User是UserDetails接口的实现类,封装了用户权限相关的的数据及用户的权限数据, 不要导错包 ;
>
> 2.工程已经配置好了BCryptPasswordEncoder加密相关bean，底层会自动调用；



## 7、认证原理分析(了解)

- Spring Security所解决的问题就是安全访问控制，而安全访问控制功能其实就是对所有进入系统的请求进行拦截，校验每个请求是否能够访问它所期望的资源。根据前边知识的学习，可以通过Filter或AOP等技术来实现；
- Spring Security对Web资源的保护是基于Filter过滤器+AOP实现的，所以我们从Filter来入手，逐步深入Spring Security原理； 
- 当初始化Spring Security时，会创建一个名为 SpringSecurityFilterChain的Servlet过滤器，类型为org.springframework.security.web.FilterChainProxy，它实现了javax.servlet.Filter，因此外部的请求会经过该类；

下图是Spring Security过虑器链结构图：

![image-20210314174535436](assets/image-20210314174535436.png) 

​	FilterChainProxy是一个代理，真正起作用的是FilterChainProxy中SecurityFilterChain所包含的各个Filter，同时 这些Filter作为Bean被Spring管理，它们是Spring Security核心，各有各的职责，但他们并不直接处理用户的认 证，也不直接处理用户的授权，而是把它们交给了认证管理器
（AuthenticationManager）和决策管理器 （AccessDecisionManager）进行处理。

下面介绍过滤器链中主要的几个过滤器及其作用：

- SecurityContextPersistenceFilter 这个Filter是整个拦截过程的入口和出口（也就是第一个和最后一个拦截 器），会在请求开始时从配置好的 SecurityContextRepository 中获取SecurityContext，然后把它设置给 SecurityContextHolder。在请求完成后将SecurityContextHolder 持有的 SecurityContext 再保存到配置好 的SecurityContextRepository，同时清除 securityContextHolder 所持有的 SecurityContext；
- ==UsernamePasswordAuthenticationFilter==用于处理来自表单提交的认证。该表单必须提供对应的用户名和密 码，其内部还有登录成功或失败后进行处理的 AuthenticationSuccessHandler 和 AuthenticationFailureHandler，这些都可以根据需求做相关改变；
- FilterSecurityInterceptor 是用于保护web资源的，使用AccessDecisionManager对当前用户进行授权访问；
- ExceptionTranslationFilter 能够捕获来自 FilterChain 所有的异常，并进行处理。但是它只会处理两类异常： AuthenticationException 和 AccessDeniedException，其它的异常它会继续抛出。


核心代码说明：

~~~java
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
      	//请求必须是post请求方式，且不能是ajax流的方式发送数据
        if (this.postOnly && !request.getMethod().equals("POST")) {
            throw new AuthenticationServiceException("Authentication method not supported: " + request.getMethod());
        } else {
          	//从form表单中的username输入框获取
            String username = this.obtainUsername(request);//request.getParameter(this.usernameParameter);
          	//从form表单中的password输入框获取
            String password = this.obtainPassword(request);
            if (username == null) {
                username = "";
            }

            if (password == null) {
                password = "";
            }
            username = username.trim();
          	//将认证的用户名和密码信息封装到一个token对象下
            UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(username, password);
            this.setDetails(request, authRequest);
          	//通过认证管理器校验token对象，它的底层会通过UserDetailService的方法获取数据库的用户信息
            //然后与前端输入的用户信息做比对
            return this.getAuthenticationManager().authenticate(authRequest);
        }
    }
~~~

> 详见：资料\security资料\SpringSecurity加密+认证授权(扩展)\SpringSecurity加密+认证授权原理.md