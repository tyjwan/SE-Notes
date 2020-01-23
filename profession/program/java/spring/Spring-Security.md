# Spring Security
***
## Code
```java
package cn.nssas.eelantech.cross;

import org.json.JSONObject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.security.web.authentication.AuthenticationFailureHandler;
import org.springframework.security.web.authentication.AuthenticationSuccessHandler;
import org.springframework.security.web.authentication.logout.LogoutSuccessHandler;

import javax.servlet.http.HttpSession;

/**
 * @author https://blog.csdn.net/wtopps/article/details/78297197
 * @date 2019.4.19
 */
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                    .antMatchers("/", "/message/").permitAll()
                    .anyRequest().authenticated()
                .and()
                .formLogin()
                    .loginProcessingUrl("/api/login")
                    .usernameParameter("user")
                    .passwordParameter("password")
                    .successHandler(successHandler())
                    .failureHandler(failureHandler())
                    .permitAll()
                .and()
                    .exceptionHandling()
                        .accessDeniedHandler(accessDeniedHandler())
                        .authenticationEntryPoint(authenticationEntryPoint())
                .and()
                .logout()
                .logoutUrl("/api/logout")
                .logoutSuccessHandler(logoutSuccessHandler())
                .deleteCookies("JSESSIONID")
                .permitAll();
        http.csrf().disable();
    }

    /**
     * 注销处理函数
     * @return map
     */
    private LogoutSuccessHandler logoutSuccessHandler() {
        return (httpServletRequest, httpServletResponse, e) -> {
            JSONObject result = new JSONObject();
            result.put("ret_code", 0);
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(200);
        };
    }

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().withUser("user").password("password").roles("USER");
        auth.inMemoryAuthentication().withUser("user2").password("password2").roles("USER");
        //在内存中创建了一个用户，该用户的名称为user，密码为password，用户角色为USER
    }

    /**
     * 没有登录前，访问错误处理
     * @return map
     */
    private AuthenticationEntryPoint authenticationEntryPoint() {
        return (httpServletRequest, httpServletResponse, e) -> {
            JSONObject result = new JSONObject();
            result.put("ret_err", "Not authenticated");
            result.put("ret_code", -3);
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(401);
        };
    }

    /**
     * 访问错误处理
     * @return map
     */
    private AccessDeniedHandler accessDeniedHandler() {
        return (httpServletRequest, httpServletResponse, e) -> {
            JSONObject result = new JSONObject();
            result.put("ret_err", "Access denied");
            result.put("ret_code", -2);
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(200);
        };
    }

    /**
     * 登录成功处理
     * @return map
     */
    private AuthenticationSuccessHandler successHandler() {
        return (httpServletRequest, httpServletResponse, authentication) -> {
            HttpSession session = httpServletRequest.getSession(false);
            if (session != null) {
                session.setMaxInactiveInterval(60);
            }

            JSONObject result = new JSONObject();
            result.put("prefix", "mnu");
            result.put("unitName", "绵阳师范学院");
            result.put("ret_code", 0);

            // 设置编码格式，编码中文乱码
            httpServletResponse.setCharacterEncoding("UTF-8");
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(200);
        };
    }

    /**
     * 登录失败处理
     * @return map
     */
    private AuthenticationFailureHandler failureHandler() {
        return (httpServletRequest, httpServletResponse, e) -> {
            JSONObject result = new JSONObject();
            result.put("ret_err", "Authentication failure");
            result.put("ret_code", -1);
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(200);
        };
    }
}
```

```java
package cn.nssas.eelantech.cross;

import cn.nssas.eelantech.common.Constant;
import cn.nssas.eelantech.service.MyUserDetailService;
import org.bson.Document;
import org.json.JSONObject;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.security.web.authentication.AuthenticationFailureHandler;
import org.springframework.security.web.authentication.AuthenticationSuccessHandler;
import org.springframework.security.web.authentication.logout.LogoutSuccessHandler;

import javax.servlet.http.HttpSession;
import javax.swing.plaf.synth.SynthUI;

/**
 * @author https://blog.csdn.net/wtopps/article/details/78297197
 * @date 2019.4.19
 */
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                    .anyRequest().authenticated()
                .and()
                .formLogin()
                    .loginProcessingUrl("/api/login")
                    .usernameParameter("user")
                    .passwordParameter("password")
                    .successHandler(successHandler())
                    .failureHandler(failureHandler())
                    .permitAll()
                .and()
                    .exceptionHandling()
                        .accessDeniedHandler(accessDeniedHandler())
                        .authenticationEntryPoint(authenticationEntryPoint())
                .and()
                .logout()
                .logoutUrl("/api/logout")
                .logoutSuccessHandler(logoutSuccessHandler())
                .deleteCookies("JSESSIONID")
                .permitAll();
        http.csrf().disable();
    }

    /**
     * 添加 UserDetailsService， 实现自定义登录校验
     */
    @Override
    protected void configure(AuthenticationManagerBuilder builder) throws Exception{
        builder.userDetailsService(new MyUserDetailService()).passwordEncoder(passwordEncoder());
    }

    /**
     * 密码加密
     */
    @Bean
    public BCryptPasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    /**
     * 注销处理函数
     * @return map
     */
    private LogoutSuccessHandler logoutSuccessHandler() {
        return (httpServletRequest, httpServletResponse, e) -> {
            JSONObject result = new JSONObject();
            result.put("ret_code", 0);
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(200);
        };
    }

    /**
     * 没有登录前，访问错误处理
     * @return map
     */
    private AuthenticationEntryPoint authenticationEntryPoint() {
        return (httpServletRequest, httpServletResponse, e) -> {
            JSONObject result = new JSONObject();
            result.put("ret_err", "Not authenticated");
            result.put("ret_code", -3);
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(401);
        };
    }

    /**
     * 访问错误处理
     * @return map
     */
    private AccessDeniedHandler accessDeniedHandler() {
        return (httpServletRequest, httpServletResponse, e) -> {
            JSONObject result = new JSONObject();
            result.put("ret_err", "Access denied");
            result.put("ret_code", -2);
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(200);
        };
    }

    /**
     * 登录成功处理
     * @return map
     */
    private AuthenticationSuccessHandler successHandler() {
        return (httpServletRequest, httpServletResponse, authentication) -> {
            HttpSession session = httpServletRequest.getSession(false);
            if (session != null) {
                session.setMaxInactiveInterval(60*60*24*30);
            }

            String user = authentication.getName();
            Document query = Constant.visualDatabase.queryOne("user", user, Constant.USER_MANAGEMENT_COLLECTION);
            System.out.println(query);

            JSONObject result = new JSONObject();
            result.put("prefix", query.get("prefix"));
            result.put("unitName", query.get("unit"));
            result.put("ret_code", 0);

            // 设置编码格式，编码中文乱码
            httpServletResponse.setCharacterEncoding("UTF-8");
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(200);
        };
    }

    /**
     * 登录失败处理
     * @return map
     */
    private AuthenticationFailureHandler failureHandler() {
        return (httpServletRequest, httpServletResponse, e) -> {
            JSONObject result = new JSONObject();
            result.put("ret_err", "Authentication failure");
            result.put("ret_code", -1);
            httpServletResponse.getWriter().append(result.toString());
            httpServletResponse.setStatus(200);
        };
    }
}
```

```java
package cn.nssas.eelantech.service;

import cn.nssas.eelantech.common.Constant;
import org.bson.Document;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;

import java.util.ArrayList;
import java.util.Collection;
import java.util.List;

/**
 * spring security:基于MongoDB的认证:https://blog.csdn.net/ruangong1203/article/details/51124220
 * @author liu wei
 */
public class MyUserDetailService implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String user) throws UsernameNotFoundException {
        Document query = Constant.visualDatabase.queryOne("user", user, Constant.USER_MANAGEMENT_COLLECTION);
        if(query == null) {
            throw new UsernameNotFoundException(user+"不存在");
        }
        UserDetails userDetails = new User(query.get("user").toString(), query.get("password").toString(), getAuthorities(query.getInteger("access")));
        return userDetails;
    }

    /** 根据用户级别，获得角色列表。比如用户级别为1，表示该用户是管理员，则返回ROLE_USER,ROLE_ADMIN
     * @param access 用户级别
     * @return 用户角色列表
     */
    public Collection<GrantedAuthority> getAuthorities(int access){
        List<GrantedAuthority> authList = new ArrayList<>(2);
        authList.add(new SimpleGrantedAuthority("ROLE_USER"));
        if(access==1){
            authList.add(new SimpleGrantedAuthority("ROLE_ADMIN"));
        }
        return authList;
    }
}
```

## 参考链接
- [spring security ajax登录及返回](https://segmentfault.com/a/1190000012140889)
- [Spring Boot Security Starter » 2.1.4.RELEASE](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-security/2.1.4.RELEASE)
- [Custom login form. Configure Spring security to get a JSON response](https://stackoverflow.com/questions/32498868/custom-login-form-configure-spring-security-to-get-a-json-response)
- [Spring Boot+SpringSecurity Session超时处理](https://blog.csdn.net/Dongguabai/article/details/81053660)
- [Spring security session timeouts](https://prasans.info/2016/09/expire_session_after_timeout_spring/)
- [使用Spring Security开发基于表单的认证（二）](https://www.jianshu.com/p/a5fe18214287)
- [Spring Security Form Login](https://www.baeldung.com/spring-security-login)
- [spring-security-demos/03 - 前后端分离（JSON交互）/](https://github.com/ChinaSilence/spring-security-demos/tree/master/03%20-%20%E5%89%8D%E5%90%8E%E7%AB%AF%E5%88%86%E7%A6%BB%EF%BC%88JSON%E4%BA%A4%E4%BA%92%EF%BC%89)
- [解决HttpServletResponse输出的中文乱码问题](https://blog.csdn.net/simon_1/article/details/9092747)
- [解决session共享问题方式调研](https://blog.csdn.net/wangdong5678999/article/details/79533912)
- [从零开始的Spring Session(三)](http://blog.didispace.com/spring-session-xjf-3/)
- [Spring Security 加密密码](https://qtdebug.com/spring-security-4-encrypt/)
- [spring security:基于MongoDB的认证](https://blog.csdn.net/ruangong1203/article/details/51124220)
- [Spring Security 入门：密码加密](https://www.jianshu.com/p/7b0e0d350d44)