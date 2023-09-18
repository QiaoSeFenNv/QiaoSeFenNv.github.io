---
title: "Shiro 👋"
description: ""
lead: ""
summary: ""
date: 2023-09-16T13:45:39+08:00
lastmod: 2023-09-16T13:45:39+08:00
draft: false
weight: 50
images: []
categories: []
tags: []
contributors: ["QiaoSe FenNv"]
pinned: false
homepage: false
---

## 前言：

**我真裂开**。公司使用到 *shiro* 框架 ，**要求代码层面不允许调用一个第三方jar包内的接口，由于接口存在第三方 jar 包内 简单使用下面方法无法满足要求，只要登陆还是可以调用到 **

```java
filterMap.put("xxx/xxx/test","authc");	#没有登录确实无法访问，但是没有满足要求
```

​	简单百度一下 *shiro* 都是**快速使用，搭建以及如何绕行**，怎么拦截却没有写的很清楚，快速看了官网和相关书籍，才有了更深的了解。 *shiro* 路径匹配采用 Ant 风格（不了解的建议 google 一下很快的，不耽误时间）

```java
    public ShiroFilterChainDefinition shiroFilterChainDefinition() {
        DefaultShiroFilterChainDefinition chainDefinition = new DefaultShiroFilterChainDefinition();
        chainDefinition.addPathDefinition("xxx/xxx/test","authc, roles[none]"); //表示只有登录，并且角色为 none 才可以访问
        //或者使用
        chainDefinition.addPathDefinition("xxx/xxx/test","authc, perms[none]");
        return chainDefinition;
    }

```

>`authc` 是 Shiro 中的一个过滤器，用于进行身份验证（Authentication）。
>
>`perms[test]` 是 Shiro 中的一个过滤器，用于进行授权（Authorization）。它的意思是要求用户必须具有 `test` 权限才能访问该接口。
>
>因此，`"authc, perms[test]"` 的意思是要求用户必须先进行身份验证，然后再检查用户是否具有 `test` 权限，才能访问 `/test` 接口。如果用户没有通过身份验证或者没有 `test` 权限，将无法访问该接口，会返回一个未授权（Unauthorized）的响应。
>
>`role[xxx]` 同理

​

​

​	跟着 [shiro.apache.org ](https://shiro.apache.org/) 官网学习了有一整子了，但是感觉官网对如何使用讲的不是很详细，更具体的内容还是需要看书或者自己去看代码挖掘。我觉得官网的好处是给我讲清楚了 shiro 的核心，对于他的使用并没有讲的很深，而且有点 GitHub 上面的模板有些许年头了。不过，如何是看了解 shiro 理论还是会推荐去看官网。其实都是看自己的理解能力。这篇文章记录一下学习 shiro 内容。



## 介绍：

​	Simple. Java. Security. 在我看来，如果你想在项目嵌入**安全框架**倒是可以考虑一下 `Shiro Apache` 。真的很轻便，并且自定义实现一些功能也是很方便的。



>​	Apache Shiro is a powerful and easy-to-use Java security framework that performs authentication, authorization, cryptography, and session management. With Shiro’s easy-to-understand API, you can quickly and easily secure any application – from the smallest mobile applications to the largest web and enterprise applications.
>
>​	Apache Shiro 是一个功能强大且易于使用的 Java 安全框架，可执行身份验证、授权、加密和会话管理。借助 Shiro 易于理解的 API，您可以快速轻松地保护任何应用程序——从最小的移动应用程序到最大的 Web 和企业应用程序





## 实战代码案例

[代码仓库](https://github.com/QiaoSeFenNv/ShiroTemplate)

### POM文件

除了基础springboot框架之外：

```pom
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring-boot-web-starter</artifactId>
            <version>1.4.0-RC2</version>
        </dependency>
```



### 数据库 sql 语句：

```sql
CREATE TABLE user (
                      id BIGINT PRIMARY KEY AUTO_INCREMENT,
                      username VARCHAR(50) NOT NULL,
                      password VARCHAR(50) NOT NULL,
                      email VARCHAR(50) NOT NULL,
                      status INT DEFAULT 0
);

CREATE TABLE role (
                      id BIGINT PRIMARY KEY AUTO_INCREMENT,
                      name VARCHAR(50) NOT NULL,
                      description VARCHAR(100)
);

CREATE TABLE permission (
                            id BIGINT PRIMARY KEY AUTO_INCREMENT,
                            name VARCHAR(50) NOT NULL,
                            description VARCHAR(100)
);

CREATE TABLE user_role (
                           user_id BIGINT NOT NULL,
                           role_id BIGINT NOT NULL,
                           PRIMARY KEY (user_id, role_id)
);

CREATE TABLE role_permission (
                                 role_id BIGINT NOT NULL,
                                 permission_id BIGINT NOT NULL,
                                 PRIMARY KEY (role_id, permission_id)
);


INSERT INTO permission (name,description) VALUES ("/test/**","测试接口");
INSERT INTO permission (name,description) VALUES ("/role/**","角色接口");
INSERT INTO permission (name,description) VALUES ("/user/**","用户接口");
INSERT INTO permission (name,description) VALUES ("/permission/**","权限接口");

INSERT INTO role (name,description) VALUES("admin","管理员");
INSERT INTO role (name,description) VALUES("test","测试人员");
INSERT INTO role (name,description) VALUES("other","其他");
INSERT INTO role (name,description) VALUES("permission","认证角色");

INSERT INTO role_permission (role_id,permission_id) VALUES (2,1);
INSERT INTO role_permission (role_id,permission_id) VALUES (1,1);
INSERT INTO role_permission (role_id,permission_id) VALUES (1,2);
INSERT INTO role_permission (role_id,permission_id) VALUES (1,3);
INSERT INTO role_permission (role_id,permission_id) VALUES (1,4);
INSERT INTO role_permission (role_id,permission_id) VALUES (3,3);
INSERT INTO role_permission (role_id,permission_id) VALUES (4,4);

INSERT INTO `user` (username,`password`,email,`status`) VALUES ("admin","123456","123456789@ui.com",0);
INSERT INTO `user` (username,`password`,email,`status`) VALUES ("zhangsan","123456","123456789@ui.com",0);
INSERT INTO `user` (username,`password`,email,`status`) VALUES ("lisi","123456","123456789@ui.com",0);
INSERT INTO `user` (username,`password`,email,`status`) VALUES ("test","123456","123456789@ui.com",0);
INSERT INTO `user` (username,`password`,email,`status`) VALUES ("other","123456","123456789@ui.com",0);

INSERT INTO user_role (user_id,role_id) VALUES (1,1);
INSERT INTO user_role (user_id,role_id) VALUES (4,2);
INSERT INTO user_role (user_id,role_id) VALUES (2,3);
INSERT INTO user_role (user_id,role_id) VALUES (3,4);
INSERT INTO user_role (user_id,role_id) VALUES (5,3);
```



### ShiroConfig

```java
package com.qiaose.configuration;


import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.toolkit.support.SFunction;
import com.qiaose.entity.shiro.RolePermission;
import com.qiaose.entity.shiro.User;
import com.qiaose.entity.shiro.UserRole;
import com.qiaose.shiro.service.RolePermissionService;
import com.qiaose.shiro.service.UserRoleService;
import com.qiaose.shiro.service.UserService;
import lombok.extern.slf4j.Slf4j;
import org.apache.shiro.authc.*;

import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.List;
import java.util.Objects;
import java.util.stream.Collectors;


@Slf4j
public class MyRealm extends AuthorizingRealm {

    @Autowired
    UserService userService;
    @Autowired
    RolePermissionService rolePermissionService;

    @Autowired
    UserRoleService userRoleService;



    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {

        log.info("账户信息：{}",token.getPrincipal());
        log.info("密码信息：{}",token.getCredentials());

        String principal = (String)token.getPrincipal();
        String credentials = String.valueOf((char[])token.getCredentials());

        log.info("账户信息：{}",principal);
        log.info("密码信息：{}",credentials);


        User user = userService.getBaseMapper().selectOne(
                new LambdaQueryWrapper<User>() .eq(User::getUsername,principal)

        );

        log.info("sql 账号：{}",user.getUsername());
        log.info("sql 密码：{}",user.getPassword());

        if (user == null){
            throw new AccountException("账号错误");
        }

        if (!credentials.equals(user.getPassword())) {
            throw new AccountException("密码错误");
        }

        return new SimpleAuthenticationInfo(user,credentials,getName());
    }

    /**
     * 这里我简化的查询出来权限的名称：将 ”admin“ 变为 自身对应 ”1“（id）
     * @param principalCollection
     * @return
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();

        User primaryPrincipal = (User) principalCollection.getPrimaryPrincipal();

        log.info("个人信息：{}",primaryPrincipal);

        //设置权限角色
        List<UserRole> userRoles = userRoleService.getBaseMapper().selectList(
                new LambdaQueryWrapper<UserRole>().eq(UserRole::getUserId, primaryPrincipal.getId())
        );
        List<String> roleIds = userRoles.stream().map(UserRole::getRoleId).map(Object::toString).collect(Collectors.toList());
        simpleAuthorizationInfo.addRoles(roleIds);

        //设置接口权限
        List<RolePermission> rolePermissions = rolePermissionService.getBaseMapper().selectList(
                new LambdaQueryWrapper<RolePermission>().in(RolePermission::getRoleId, roleIds)
        );
        List<String> permissionIds = rolePermissions.stream().map(RolePermission::getPermissionId).map(Objects::toString).collect(Collectors.toList());
        simpleAuthorizationInfo.addStringPermissions(permissionIds);

        log.info("个人信息权限信息：{},{}",roleIds,permissionIds);

        return  simpleAuthorizationInfo;
    }


}

```



### Realm

```java
package com.qiaose.configuration;



import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authz.Authorizer;
import org.apache.shiro.authz.ModularRealmAuthorizer;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.spring.security.interceptor.AopAllianceAnnotationsAuthorizingMethodInterceptor;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;

import org.apache.shiro.spring.web.config.DefaultShiroFilterChainDefinition;

import org.apache.shiro.web.filter.mgt.DefaultFilterChainManager;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.LinkedHashMap;
import java.util.Map;


@Configuration
public class ShiroConfig  {


    /**
     * 引入 Realm 对象。在后续 SecurityManage 进行注入
     * @return
     */
    @Bean
    public MyRealm userRealm() {
        return new MyRealm();
    }

    /**
     * 创建默认的 SecurityManager ,将引入的 Realm 进行委托
     * @return
     */
    @Bean
    public DefaultWebSecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(userRealm());
        return securityManager;
    }

    /**
     * Shiro 权限注解 需要 SpringbootAOP 支持
     * @return
     */
    @Bean
    public DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator(){
        DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        defaultAdvisorAutoProxyCreator.setProxyTargetClass(true);
        return defaultAdvisorAutoProxyCreator;
    }

    /**
     * 将 SecurityManager 对象注入到 切面中才可以从中获取到对应权限信息
     * @return
     */
    @Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor() {
        AuthorizationAttributeSourceAdvisor advisor = new AuthorizationAttributeSourceAdvisor();
        advisor.setSecurityManager(securityManager());
        return advisor;
    }

    /**
     * 过滤链，可以通过 Realm 进行配置或者这边静态配置，推荐动态配置权限
     * @return
     */
    @Bean
    public DefaultShiroFilterChainDefinition shiroFilterChainDefinition(){
        DefaultShiroFilterChainDefinition chain = new DefaultShiroFilterChainDefinition();
        // 设置哪些请求可以匿名访问
        chain.addPathDefinition("/common/login","anon");
        chain.addPathDefinition("/user/insert","anon");

        // 由于使用Swagger调试，因此设置所有Swagger相关的请求可以匿名访问
        chain.addPathDefinition("/swagger-ui/index.html", "anon");
        chain.addPathDefinition("/swagger-resources", "anon");
        chain.addPathDefinition("/swagger-resources/configuration/security", "anon");
        chain.addPathDefinition("/swagger-resources/configuration/ui", "anon");
        chain.addPathDefinition("/v2/api-docs", "anon");
        chain.addPathDefinition("/webjars/springfox-swagger-ui/**", "anon");

        //除了以上的请求外，其它请求都需要登录
        chain.addPathDefinition("/**", "authc");


        return chain;
    }


    /**
     * 核心组件，旧版本之前使用的 @Bean("ShiroFilter") 高版本使用 ”shiroFilterFactoryBean“ 需要注意
     * @return
     */
    @Bean(value = "shiroFilterFactoryBean")
    public ShiroFilterFactoryBean shiroFilter() {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager());
        shiroFilterFactoryBean.setFilterChainDefinitionMap( shiroFilterChainDefinition().getFilterChainMap());

        return shiroFilterFactoryBean;
    }

}

```



### 其他代码：

#### 登录：

```java
package com.qiaose.controller.shiro;


import com.qiaose.entity.Result;
import com.qiaose.entity.shiro.User;
import io.swagger.annotations.Api;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.subject.Subject;
import org.springframework.web.bind.annotation.*;


@RequestMapping("/common")
@Api(tags = "公共接口")
@RestController
public class CommonController {


    /**
     * 简单的登录接口，后续有需要可以配置添加盐，或者其他密码加密工具进行整合
     * @param user
     * @return
     */
    @PostMapping("login")
    public Result<?> login(@RequestBody User user){

        UsernamePasswordToken usernamePasswordToken = new UsernamePasswordToken(user.getUsername(), user.getPassword());
        Subject subject = SecurityUtils.getSubject();
        subject.login(usernamePasswordToken);
        return Result.success();

    }

}

```

#### 权限接口

```java
package com.qiaose.controller.shiro;


import com.qiaose.entity.Result;
import com.qiaose.entity.shiro.Permission;

import com.qiaose.shiro.service.PermissionService;
import io.swagger.annotations.Api;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;


@RestController
@RequestMapping("/permission")
@Api(tags = "权限接口")
@Validated
public class PermissionController {


    @Resource
    PermissionService permissionService;

    @GetMapping("select/{id}")
    public Result<?> getUser(@PathVariable String id){
        Permission role = permissionService.selectByPrimaryKey(Long.parseLong(id));
        return Result.success(role);
    }

    @PostMapping("insert")
    public Result<?> insertUser(@RequestBody Permission permission){

        boolean insert = permissionService.save(permission);
        if (Boolean.FALSE.equals(insert)) {
            return Result.error();
        }
        return   Result.success();
    }

    @PostMapping("update")
    public Result<?> updateUser(@RequestBody Permission permission){

        boolean update = permissionService.updateById(permission);
        return  Boolean.FALSE.equals(update)?  Result.error() : Result.success();
    }
}

```

#### 角色接口

```java
package com.qiaose.controller.shiro;


import com.qiaose.entity.Result;
import com.qiaose.entity.shiro.Role;
import com.qiaose.shiro.service.RoleService;
import io.swagger.annotations.Api;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authz.annotation.RequiresRoles;
import org.apache.shiro.subject.Subject;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;


@RestController
@RequestMapping("/role")
@Api(tags = "角色接口")
@Validated
public class RoleController {


    @Resource
    RoleService roleService;

    /**
     * 这里我简化的查询出来权限的名称：将 ”other“ 变为 自身对应 ”3“（id）
     * @param id
     * @return
     */
    @GetMapping("select/{id}")
    public Result<?> getUser(@PathVariable String id){
        Subject subject = SecurityUtils.getSubject();
        if (subject.hasRole("3")) {
            Role role = roleService.selectByPrimaryKey(Long.parseLong(id));
            return Result.success(role);
        }
        return Result.error();
    }


    @PostMapping("insert")
    public Result<?> insertUser(@RequestBody Role role){

        boolean insert = roleService.save(role);
        if (Boolean.FALSE.equals(insert)) {
            return Result.error();
        }
        return   Result.success();
    }

    @PostMapping("update")
    public Result<?> updateUser(@RequestBody Role role){

        boolean update = roleService.updateById(role);
        return  Boolean.FALSE.equals(update)?  Result.error() : Result.success();
    }

}

```



#### 用户接口

```java
package com.qiaose.controller.shiro;


import com.qiaose.entity.Result;
import com.qiaose.entity.shiro.User;
import com.qiaose.entity.shiro.UserRole;
import com.qiaose.shiro.service.UserRoleService;
import com.qiaose.shiro.service.UserService;
import io.swagger.annotations.Api;
import org.apache.shiro.authz.annotation.RequiresRoles;
import org.springframework.util.StringUtils;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;


@RestController
@RequestMapping("/user")
@Api(tags = "用户接口")
@Validated
public class UserController {


    @Resource
    private UserService userService ;


    @Resource
    UserRoleService userRoleService;


    /**
     * 这里我简化的查询出来权限的名称：将 ”other“ 变为 自身对应 ”3“（id）
     * @param id
     * @return
     */
    @RequiresRoles("3")
    @GetMapping("select/{id}")
    public Result<?> getUser(@PathVariable String id){
        User user = userService.selectByPrimaryKey(Long.parseLong(id));
        return Result.success(user);
    }

    /**
     * 普通注册（添加）用户，可以通过传递 roleId 进行 角色赋予，默认为 3 other 角色
     * @param user
     * @param roleId
     * @return
     */
    @PostMapping("insert/{roleId}")
    public Result<?> insertUser(@RequestBody User user,@PathVariable String roleId){

        boolean insert = userService.save(user);
        if (Boolean.FALSE.equals(insert)) {
            return Result.error();
        }

        // mybatis-plus 添加成功后会返回实体，并且会对于返回 id 值 默认是 other 角色
        UserRole userRole = UserRole.builder().userId(user.getId()).userId(
                StringUtils.isEmpty(roleId) ? 3 : Long.parseLong(roleId)
        ).build();

        boolean save = userRoleService.save(userRole);
        if (Boolean.FALSE.equals(insert)) {
            return Result.error();
        }
        return   Result.success();
    }


    @PostMapping("update")
    public Result<?> updateUser(@RequestBody User user){

        boolean update = userService.updateById(user);
        return  Boolean.FALSE.equals(update)?  Result.error() : Result.success();
    }



}

```



>[!Info]
>
>实现基础的权限功能，但是存在一些功能没有实现，例如 CacheManager 没有使用上（不过感兴趣的可以看官网，使用 redis 或者 Encache 都是很方便的），Session 功能管理也没有实现，由于没有页面、前端进行处理，所以就略去了（bushi 我懒 ，一些功能。可以去官网查询或者文章后面有提及，感兴趣可以看看。







## 核心

### Subject

在 Shiro 中，`Subject` 是一个核心的概念，表示当前操作的主体，可以是一个用户、一个程序或者其他类型的实体。`Subject` 封装了当前用户的安全操作状态，包括身份认证、授权信息、会话状态等。

在 Shiro 中，要创建一个 `Subject` 实例，需要使用 `SecurityUtils.getSubject()` 方法。这个方法会返回一个 `Subject` 对象，用于表示当前用户。

可以使用 `Subject` 对象执行以下操作：

- 登录：使用 `Subject.login(AuthenticationToken token)` 方法进行登录操作。其中，`AuthenticationToken` 表示登录信息，可以是用户名/密码、证书、API Key 等。
- 登出：使用 `Subject.logout()` 方法进行登出操作。
- 权限检查：使用 `Subject.isPermitted(String permission)` 或 `Subject.isPermitted(Permission permission)` 方法检查当前用户是否具有指定的权限。
- 角色检查：使用 `Subject.hasRole(String role)` 或 `Subject.hasRole(Role role)` 方法检查当前用户是否具有指定的角色。
- 身份认证信息获取：使用 `Subject.getPrincipal()` 方法获取当前用户的身份信息。



#### 示例代码：

```java
Subject currentUser = SecurityUtils.getSubject();

// 构造登录令牌
UsernamePasswordToken token = new UsernamePasswordToken("username", "password");

try {
    // 登录
    currentUser.login(token);
} catch (UnknownAccountException uae) {
    // 用户名不存在
} catch (IncorrectCredentialsException ice) {
    // 密码不正确
} catch (LockedAccountException lae) {
    // 账号被锁定
} catch (AuthenticationException ae) {
    // 其他认证异常
}

if (currentUser.isAuthenticated()) {
    // 已经登录
} else {
    // 未登录
}

// 权限检查
if (currentUser.isPermitted("user:create")) {
    // 具有创建用户的权限
} else {
    // 没有创建用户的权限
}

// 角色检查
if (currentUser.hasRole("admin")) {
    // 具有 admin 角色
} else {
    // 没有 admin 角色
}
```



### SecuirtyManager

`SecurityManager`：Shiro 安全管理器，负责管理所有的安全相关组件，例如用户身份验证、权限校验、会话管理等

使用 `SecurityManager` 的步骤一般如下：

- 创建 `SecurityManager` 实例：可以通过 `DefaultSecurityManager` 或者 `IniSecurityManagerFactory` 等工厂类创建 `SecurityManager` 实例。
- 配置 `Realm`：需要配置好相应的 `Realm` 实现，以便 Shiro 可以从数据源中获取用户的身份信息和权限信息。
- 将 `SecurityManager` 注入到 `Subject` 中：可以通过 `SecurityUtils.setSecurityManager(SecurityManager)` 方法将 `SecurityManager` 注入到 `Subject` 中。



#### 示例代码：

```java
@Configuration
public class ShiroConfig {

    @Bean
    public DefaultWebSecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(myRealm());
        return securityManager;
    }

    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean() {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager());
        shiroFilterFactoryBean.setLoginUrl("/login");
        shiroFilterFactoryBean.setUnauthorizedUrl("/unauthorized");
        shiroFilterFactoryBean.setFilterChainDefinitionMap(new LinkedHashMap<String, String>() {{
            put("/login", "anon");
            put("/logout", "logout");
            put("/**", "authc");
        }});
        return shiroFilterFactoryBean;
    }

    @Bean
    public MyRealm myRealm() {
        return new MyRealm();
    }

}

```

```java
public class MyRealm extends AuthorizingRealm {

    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        // TODO: 实现授权逻辑
        return null;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        // TODO: 实现认证逻辑
        return null;
    }

}
```



>[!Info]
>
>ShiroFilterFactoryBean 是一个用于创建 Shiro 过滤器链的工厂类。它是 Shiro 和 Spring Boot 集成的核心组件之一，用于定义 Shiro 应用程序的安全过滤器链。
>
>ShiroFilterFactoryBean 中最重要的方法是 setFilterChainDefinitionMap，它用于设置 URL 模式和过滤器的映射关系。通常情况下，我们会按照从上到下的顺序将 URL 模式和对应的过滤器写在一个 LinkedHashMap 中，并将这个 Map 传给 setFilterChainDefinitionMap 方法。过滤器的名称通常是由 Shiro 提供的，比如 authc 表示需要认证才能访问，anon 表示不需要认证就可以访问。
>
>除了 setFilterChainDefinitionMap 方法，ShiroFilterFactoryBean 还提供了其他的一些方法，如 setSecurityManager 方法用于设置 SecurityManager 对象、setLoginUrl 方法用于设置登录 URL、setSuccessUrl 方法用于设置登录成功后跳转的 URL、setUnauthorizedUrl 方法用于设置未授权时跳转的 URL 等。这些方法可以根据实际需要进行配置。
>
>**需要注意的是，ShiroFilterFactoryBean 只能用于 Spring 应用程序中。如果您的应用程序不是基于 Spring 的，则需要手动创建 FilterChainResolver 对象来定义过滤器链。**





### Realm

在 Shiro 中，Realm 是一个安全实体，它封装了访问数据的方法，可以用来验证用户、授权访问和存储安全相关的数据，如用户信息、角色信息和权限信息等。它是 Shiro 的一个核心组件，负责处理用户身份认证和授权的逻辑，对于应用程序的安全性有着至关重要的作用。

Realm 的实现类通常需要根据实际情况去实现这些方法，以便在用户登录、授权和访问受限资源时进行调用。

其中，

​	doGetAuthenticationInfo 方法用于用户认证，

​	doGetAuthorizationInfo 方法用于用户授权。

**在使用 Realm 的时候，我们需要将其注入到 SecurityManager 中，这样在用户身份认证和授权的过程中，SecurityManager 就会使用注入的 Realm 来查询用户信息和权限信息。**



#### 示例代码：

创建一个名为`user`的用户表，包含以下字段：

| 字段名   | 类型        | 描述     |
| -------- | ----------- | -------- |
| id       | bigint(20)  | 用户ID   |
| username | varchar(50) | 用户名   |
| password | varchar(50) | 密码     |
| role     | varchar(50) | 角色名称 |

创建一个名为`permission`的权限表，包含以下字段：

| 字段名 | 类型         | 描述        |
| ------ | ------------ | ----------- |
| id     | bigint(20)   | 权限ID      |
| name   | varchar(50)  | 权限名称    |
| url    | varchar(200) | 资源URL地址 |

创建一个名为`role_permission`的角色权限关联表，包含以下字段：

| 字段名        | 类型        | 描述     |
| ------------- | ----------- | -------- |
| id            | bigint(20)  | 关联ID   |
| role_name     | varchar(50) | 角色名称 |
| permission_id | bigint(20)  | 权限ID   |

```java
public class MybatisRealm extends AuthorizingRealm {

    @Autowired
    private UserMapper userMapper;

    @Autowired
    private RoleMapper roleMapper;

    @Autowired
    private PermissionMapper permissionMapper;


    @Autowired
    private UserMapper userMapper;
    @Autowired
    private PermissionMapper permissionMapper;
    @Autowired
    private RolePermissionMapper rolePermissionMapper;

    // 授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();
        String username = (String) principals.getPrimaryPrincipal();

        // 获取用户角色
        User user = userMapper.selectByUsername(username);
        authorizationInfo.addRole(user.getRole());

        // 获取用户权限
        List<Permission> permissionList = permissionMapper.selectByRole(user.getRole());
        for (Permission permission : permissionList) {
            authorizationInfo.addStringPermission(permission.getName());
        }

        return authorizationInfo;
    }
    /**
     * 用户身份认证
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        // 获取用户名和密码
        String username = (String) token.getPrincipal();
        String password = new String((char[]) token.getCredentials());

        // 根据用户名查询用户信息
        User user = userMapper.selectByUsername(username);
        if (user == null) {
            throw new UnknownAccountException("用户名或密码不正确");
        }

        // 验证密码是否正确
        if (!password.equals(user.getPassword())) {
            throw new IncorrectCredentialsException("用户名或密码不正确");
        }

        // 返回认证信息
        return new SimpleAuthenticationInfo(user, password, getName());
    }


}

```





### Cache

Shiro提供了缓存机制来提高应用程序的性能和响应速度。Shiro可以自动缓存数据，例如身份验证、角色和权限等，可以使用缓存来避免频繁地查询数据库。

Shiro缓存主要有两种类型：认证缓存和授权缓存。认证缓存缓存了身份验证信息，授权缓存缓存了授权信息，例如用户的角色和权限等。

在Shiro中，缓存的使用分为两种方式：

- 全局缓存
  - 全局缓存是Shiro提供的一个抽象缓存接口，所有的缓存都必须实现这个接口。全局缓存通常用于缓存长时间不变的数据，例如用户的角色和权限等。

- 局部缓存
  - 局部缓存是指Shiro在特定情况下缓存一些数据，例如缓存一个用户的身份验证信息。局部缓存通常是有时限的，当缓存数据过期或者不再需要时，Shiro会自动删除这些缓存数据。



在 Spring Boot 中，我们可以使用 `CacheManager` 接口的实现类来配置 Shiro 缓存，Shiro 默认提供了以下实现类：

- `MemoryConstrainedCacheManager`：基于内存的缓存管理器，用于单机环境；
- `EhCacheManager`：基于 Ehcache 的缓存管理器，用于分布式环境；
- `RedisCacheManager`：基于 Redis 的缓存管理器，用于分布式环境。

#### 示例代码：

```java
@Configuration
public class ShiroCacheConfig {

    @Bean
    public EhCacheManager ehCacheManager() {
        EhCacheManager ehCacheManager = new EhCacheManager();
        ehCacheManager.setCacheManagerConfigFile("classpath:ehcache.xml"); // Ehcache 配置文件路径
        return ehCacheManager;
    }
}

```

在 Shiro Realm 中需要使用缓存的方法上添加 `@RequiresCaching` 注解，标记该方法需要使用缓存，这样 Shiro 才会将该方法返回的结果缓存起来。

同时，在 ShiroFilterFactoryBean 中配置缓存策略：

```java
@Bean
public ShiroFilterFactoryBean shiroFilter(DefaultWebSecurityManager securityManager) {
    // 省略其他配置

    Map<String, Filter> filterMap = new LinkedHashMap<>();
    filterMap.put("authc", new FormAuthenticationFilter());	//FormAuthenticationFilter是Shiro框架自带的一个过滤器，用于处理基于表单的身份验证。当用户提交包含用户名和密码的表单时，FormAuthenticationFilter将自动从请求中提取这些值，并使用它们来验证用户的身份。如果身份验证成功，则用户将被授予访问所请求资源的权限

    shiroFilterFactoryBean.setFilters(filterMap);

    // 配置缓存策略
    shiroFilterFactoryBean.setCacheManager(ehCacheManager());
    shiroFilterFactoryBean.setFilterChainResolver(shiroFilterChainResolver());

    return shiroFilterFactoryBean;
}

```

配置缓存xml文件

```xml
<cache name="myCache"
       maxEntriesLocalHeap="10000"
       timeToIdleSeconds="600"
       timeToLiveSeconds="1800"
       eternal="false"
       memoryStoreEvictionPolicy="LRU" />

```





### Sesion

Shiro的Session是一个会话对象，用于存储用户在应用程序中的会话信息。在Web应用程序中，Shiro使用HTTP会话来存储会话信息。Shiro的Session可以使用多种方式存储，包括内存、文件、数据库和缓存等。

Shiro的Session提供了以下常见的功能：

1. 可以存储和检索用户的身份信息。
2. 可以存储和检索用户的权限信息。
3. 可以设置和检索会话的过期时间。
4. 可以存储和检索会话中的任意数据。

Shiro的Session可以通过以下两种方式使用：

1. 直接获取当前用户的Session对象：可以通过SecurityUtils.getSubject().getSession()方法获取当前用户的Session对象。
2. 通过SessionManager获取Session对象：可以通过SessionManager的getSession方法获取Session对象。SessionManager可以通过SecurityManager进行配置。

在使用Shiro的Session时，需要注意以下几点：

1. 需要配置Shiro的Session管理器，以便使用指定的会话存储方式。可以使用默认的会话管理器，也可以自定义会话管理器。
2. 需要在Shiro的配置文件中配置Session的相关属性，如超时时间、Cookie名称等。
3. 需要在使用Session的代码中注意对Session的异常处理，如Session过期、Session失效等。



#### 示例代码：

```Java
@Controller
public class MyController {

    @GetMapping("/test")
    @ResponseBody
    public String test(HttpSession httpSession, HttpServletRequest request) {
        // 获取 Shiro Session
        Subject currentUser = SecurityUtils.getSubject();
        Session shiroSession = currentUser.getSession();

        // 设置 Session 属性
        shiroSession.setAttribute("key", "value");

        // 获取 Session 属性
        String value = (String) shiroSession.getAttribute("key");

        // 销毁 Session
        shiroSession.stop();

        return "Session Test";
    }
}

```



>[!Info]
>
>在上面的示例中，我们获取了当前用户的 Shiro Session，设置了一个属性，获取了该属性的值，然后销毁了 Session。在实际应用中，我们可以使用 Shiro Session 存储一些用户信息，如用户 ID、用户名、角色、权限等，以便在后续的操作中使用。同时，Shiro 也提供了一些 Session 相关的配置，如 Session 超时时间、Session ID 生成方式等，可以根据实际需求进行配置。





## 其他组件:

### AuthenticationToken

AuthenticationToken 是 Shiro 用来封装用户身份信息的接口，它提供了 getPrincipal() 和 getCredentials() 两个方法，用来获取用户的身份信息和凭证信息。



通常会创建一个 AuthenticationToken 对象，将用户输入的用户名和密码等身份信息封装进去，然后将该对象交给 SecurityManager 进行认证。SecurityManager 会根据 AuthenticationToken 中的信息进行用户身份认证，并将认证结果封装到 AuthenticationInfo 对象中返回给调用者。



**除了 UsernamePasswordToken 之外，Shiro 还提供了其他的 AuthenticationToken 实现类，如 JwtToken、SamlToken、CaptchaToken 等，可以根据实际需要进行选择和使用。**



JwtToken会包含以下信息：

- 用户身份信息，如用户ID、用户名等
- 有效期，用于判断JwtToken是否过期
- 签名信息，用于校验JwtToken是否被篡改

JwtToken的方法通常包括：

- 构造方法：用于创建JwtToken对象，并设置相应的属性值
- 获取用户身份信息的方法：用于获取JwtToken中的用户身份信息
- 判断是否过期的方法：用于判断JwtToken是否已过期
- 获取签名信息的方法：用于获取JwtToken的签名信息



#### 示例代码：

```java
public class JwtToken implements AuthenticationToken {

    private String token;

    public JwtToken(String token) {
        this.token = token;
    }

    @Override
    public Object getPrincipal() {
        return token;
    }

    @Override
    public Object getCredentials() {
        return token;
    }
}
```



```java
public class JwtRealm extends AuthorizingRealm {

    @Override
    public boolean supports(AuthenticationToken token) {
        return token instanceof JwtToken;
    }

    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        // 授权逻辑
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        JwtToken jwtToken = (JwtToken) token;
        String jwt = jwtToken.getJwt();
        if (JwtUtil.verifyJwt(jwt)) {
            String userId = JwtUtil.getUserId(jwt);
            User user = userService.getUserById(userId);
            if (user != null) {
                return new SimpleAuthenticationInfo(jwtToken, jwtToken.getCredentials(), getName());
            }
        }
        throw new AuthenticationException("JwtToken认证失败");
    }
}

```

>传统的用户名/密码认证流程是先通过用户名查询数据库获取用户信息，然后将用户输入的密码与数据库中的密码进行比对，从而确定用户是否认证成功。而 JWT 认证则是直接将 JWT 字符串作为令牌进行认证，不需要再查询数据库或其他数据源获取用户信息，因为 JWT 中已经包含了用户的信息。因此，在 JWT 认证中，Shiro Realm 的作用是根据 JWT 中包含的信息来确定用户是否存在和用户的角色、权限等





### ShiroFilterChainDefinition

可以在其中编写过滤那些接口和权限定义



#### 示例代码：

```java
    @Bean
    public DefaultShiroFilterChainDefinition shiroFilterChainDefinition(){
        DefaultShiroFilterChainDefinition chain = new DefaultShiroFilterChainDefinition();
        // 设置哪些请求可以匿名访问
        chain.addPathDefinition("/login/**", "anon");

        // 由于使用Swagger调试，因此设置所有Swagger相关的请求可以匿名访问
        chain.addPathDefinition("/swagger-ui/index.html", "authc");
        chain.addPathDefinition("/swagger-resources", "authc");
        chain.addPathDefinition("/swagger-resources/configuration/security", "authc");
        chain.addPathDefinition("/swagger-resources/configuration/ui", "authc");
        chain.addPathDefinition("/v2/api-docs", "authc");
        chain.addPathDefinition("/webjars/springfox-swagger-ui/**", "authc");

        //除了以上的请求外，其它请求都需要登录
        chain.addPathDefinition("/**", "authc");
        return chain;
    }

	//通过该方法将 shiroFilterChainDefinition 内容填写进去
	shiroFilterFactoryBean.setFilterChainDefinitionMap( shiroFilterChainDefinition().getFilterChainMap());
```





### shiroFilter

- `anon`（AnonymousFilter）：允许匿名访问，不需要进行身份验证和授权。
- `authc`（FormAuthenticationFilter）：要求用户必须进行身份验证后才能访问。
- `authcBasic`（BasicHttpAuthenticationFilter）：要求用户必须进行基本身份验证（HTTP Basic Authentication）后才能访问。 这种身份验证方式需要客户端将用户名和密码进行 Base64 编码后放在 HTTP 请求头中的 Authorization 字段中发送给服务端。
- `logout`（LogoutFilter）：用于退出当前用户的身份验证状态。
- `roles`（RolesAuthorizationFilter）：要求用户必须具有指定的角色才能访问。
- `perms`（PermissionsAuthorizationFilter）：要求用户必须具有指定的权限才能访问。



>[!Info]
>
>1. `RolesAuthorizationFilter`：用于基于角色授权的过滤器。
>2. `PermissionsAuthorizationFilter`：用于基于权限授权的过滤器。
>3. `LogoutFilter`：用于处理登出的过滤器。
>4. `AuthenticatingFilter`：用于在请求处理之前进行身份验证的过滤器，其它的过滤器都是在该过滤器验证通过后才会执行的。
>5. `AnonymousFilter`：用于处理匿名访问的过滤器，即不需要登录就可以访问的资源。
>6. `UserFilter`：用于判断用户是否已经登录的过滤器。
>7. `RolesOrFilter`：用于实现多角色授权的过滤器。
>8. `PermsOrFilter`：用于实现多权限授权的过滤器。
>9. `SslFilter`：用于处理 HTTPS 请求的过滤器。

#### 示例代码1：

```java
filterChainDefinitionMap.put("/admin/**", "authc, roles[admin]");  // /admin/** 路径需要用户登录且具有 admin 角色才能访问
filterChainDefinitionMap.put("/api/**", "authc, perms[api:read]"); // /api/** 路径需要用户登录且具有 api:read 权限才能访问
filterChainDefinitionMap.put("/logout", "logout"); // /logout 路径用于退出当前用户的身份验证状态
filterChainDefinitionMap.put("/**", "anon"); // 所有其他路径都可以匿名访问
filterChainDefinitionMap.put("/api/**", "authcBasic");
```



- `ssl`（SslFilter）：要求用户必须使用 HTTPS 安全连接才能访问。  可以在 Web 容器中配置 HTTPS 监听端口并启用 SSL/TLS，然后在 Shiro 配置文件中开启 `forceSSL` 选项，该选项会将所有 HTTP 请求强制重定向到 HTTPS 请求

- `port`（PortFilter）：要求用户必须使用指定的端口号才能访问。



#### 示例代码2：

```java
@Bean
public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager) {
    // ...省略其他配置...
    shiroFilterFactoryBean.setForceSsl(true);
    shiroFilterFactoryBean.setPort(8443);
    return shiroFilterFactoryBean;
}
```





### 权限注解：

需要先开启 AOP 代理支持，以便在运行时能够拦截被注解的方法。



在 Spring Boot 的配置类中，需要声明一个 `DefaultAdvisorAutoProxyCreator` bean，用于自动创建 AOP 代理。例如：

```java
@Configuration
public class ShiroConfig {

    @Bean
    public DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator() {
        DefaultAdvisorAutoProxyCreator creator = new DefaultAdvisorAutoProxyCreator();
        creator.setProxyTargetClass(true);
        return creator;
    }

}


    @Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor() {
        AuthorizationAttributeSourceAdvisor advisor = new AuthorizationAttributeSourceAdvisor();
        advisor.setSecurityManager(securityManager());
        return advisor;
    }
```

这里使用了默认的 `DefaultAdvisorAutoProxyCreator` 实现，并将 `proxyTargetClass` 属性设置为 `true`，表示使用 CGLIB 动态代理实现 AOP 代理。如果你的类是一个接口，则可以将其设置为 `false`，使用 JDK 动态代理实现 AOP 代理。





1. `@RequiresAuthentication`：表示当前用户需要进行身份验证才能访问被注解的方法或类。
2. `@RequiresGuest`：表示当前用户需要是“访客”才能访问被注解的方法或类。
3. `@RequiresPermissions`：表示当前用户需要具备指定的权限才能访问被注解的方法或类。可以同时指定多个权限，多个权限之间使用逗号分隔，表示需要满足其中任意一个权限即可。
4. `@RequiresRoles`：表示当前用户需要具备指定的角色才能访问被注解的方法或类。可以同时指定多个角色，多个角色之间使用逗号分隔，表示需要满足其中任意一个角色即可。

这些注解可以用在方法上或类上，如果用在类上，则表示该类中的所有方法都需要满足注解中指定的条件才能被访问。这些注解还支持逻辑运算符，可以通过 `logical` 属性来指定逻辑运算符，其取值可以是 `AND` 或 `OR`，表示需要满足所有条件或满足任意一个条件即可。



#### 示例代码：

```java
@RequiresRoles("admin")
@RequiresPermissions("user:view")
public User getUser(Long userId) {
    // ...
}

```





### 自定义Flter：

自定义 Filter 需要实现 org.apache.shiro.web.filter.AccessControlFilter 接口，该接口继承了 org.apache.shiro.web.filter.authc.AuthenticationFilter 接口，其中 AuthenticationFilter 是用于身份认证的过滤器。因此，如果我们只需要自定义授权过滤器，可以直接继承 AccessControlFilter 接口。



#### 示例代码：

```java
public class CustomAuthorizationFilter extends AccessControlFilter {

    @Override
    protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object mappedValue) throws Exception {
        // 这里可以编写自定义的授权逻辑
        return true; // 默认允许访问
    }

    @Override
    protected boolean onAccessDenied(ServletRequest request, ServletResponse response) throws Exception {
        // 如果没有权限，则重定向到指定的 URL
        WebUtils.issueRedirect(request, response, "/unauthorized");
        return false;
    }
}

```

```java
@Bean
public ShiroFilterFactoryBean shiroFilter(SecurityManager securityManager) {
    ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
    shiroFilterFactoryBean.setSecurityManager(securityManager);

    // 注入自定义 Filter
    Map<String, Filter> filters = new HashMap<>();
    filters.put("customAuthorization", new CustomAuthorizationFilter());
    shiroFilterFactoryBean.setFilters(filters);

    // 配置 FilterChainDefinitionMap
    Map<String, String> filterChainDefinitionMap = new LinkedHashMap<>();
    filterChainDefinitionMap.put("/test/**", "customAuthorization");
    filterChainDefinitionMap.put("/admin/**", "authc");
    shiroFilterFactoryBean.setFilterChainDefinitionMap(filterChainDefinitionMap);

    return shiroFilterFactoryBean;
}

```

>如果同一个 URL 匹配了多个过滤器链，Shiro 将按照配置的顺序依次使用这些过滤器链。因此，如果需要自定义 URL 的保护策略，需要根据具体的业务需求编写相应的过滤器





### 归纳总结：

shiro 将获取到用户/密码或者 Token 等等，将其封装到 AuthenticationToken 中，通过 Subject.login 方法中( Subject 实例，通常是一个（或子类）通过调用 DelegatingSubject 委托给应用程序)。 Shiro 会将内容收集起来（ SecurityManager 作为基本“伞形”组件的 接收令牌并 Authenticator 通过调用 简单地委托给其内部实例authenticator.authenticate(token)）。

在 Realm 进行校验，判断是否符合预期的设计逻辑。如何符合则放行，反正不处理






