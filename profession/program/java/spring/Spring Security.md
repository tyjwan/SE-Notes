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