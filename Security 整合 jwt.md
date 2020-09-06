# 依赖

```xml
<!--        jwt-->
<!-- https://mvnrepository.com/artifact/com.auth0/java-jwt -->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.10.3</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<!--mybatis plus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.3.2</version>
</dependency>
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.3.2</version>
</dependency>
<!--        模板引擎需要-->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.0</version>
</dependency>
<!--swagger-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
     <groupId>org.projectlombok</groupId>
     <artifactId>lombok</artifactId>
     <optional>true</optional>
</dependency>
```

# application.yml

```yml
server:
  port: 8081

spring:
  datasource:
    name: test
    url: jdbc:mysql://127.0.0.1:3307/shiro?useUnicode=true&useSSL=false&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    username: root
    password: root

# mybatis config
mybatis-plus:
  global-config:
    db-config:
      id-type: auto
```

# security配置

```java
package com.securityjwt.demo.config;

import com.securityjwt.demo.filter.JwtFilter;
import com.securityjwt.demo.filter.JwtLoginFilter;
//import com.securityjwt.demo.jwt.JwtAuthenticationTokenFilter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpMethod;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.config.BeanIds;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.annotation.web.configurers.ExpressionUrlAuthorizationConfigurer;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import org.springframework.security.web.authentication.logout.LogoutFilter;
import org.springframework.web.cors.CorsUtils;

@Configuration
@EnableWebSecurity
class SecurityConfig extends WebSecurityConfigurerAdapter {

//    @Bean
//    @Override
//    public UserDetailsService userDetailsServiceBean() throws Exception {
//        return super.userDetailsServiceBean();
//    }
//
//    @Bean
//    @Override
//    public AuthenticationManager authenticationManagerBean() throws Exception {
//        return super.authenticationManagerBean();
//    }

    //授权
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("admin").password(new BCryptPasswordEncoder().encode("admin")).roles("admin")
                .and()
                .withUser("lllll").password(new BCryptPasswordEncoder().encode("admin")).roles("root");
//                .withUser("root").password(new BCryptPasswordEncoder().encode("root")).roles("vip2");
    }

//    认证
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/hello").hasRole("admin")
                .antMatchers("/admin").hasRole("root")
                .antMatchers("/jwt").permitAll()
                .antMatchers("/login").permitAll()
                .anyRequest().authenticated()
                .and()
                .addFilterBefore(new JwtLoginFilter("/login",authenticationManager()),UsernamePasswordAuthenticationFilter.class)
                .addFilterBefore(new JwtFilter(),UsernamePasswordAuthenticationFilter.class)
                .csrf().disable();
    }
//不需要授权
    @Override
    public void configure(WebSecurity web) throws Exception {
//        web.ignoring().antMatchers("/tt");
//        .ignoring().antMatchers("out");
    }
}
```

# 继承UserDetails

> 用户详情

```java
package com.securityjwt.demo.filter;

import lombok.Data;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.Collection;
import java.util.List;

@Data
public class JwtUser implements UserDetails {

    private String username;
    private String password;
//    权限
    private List<GrantedAuthority> authorities;
    
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    @Override
    public String getPassword() {
        return password;
    }

    @Override
    public String getUsername() {
        return username;
    }

//    是否过期
    @Override
    public boolean isAccountNonExpired() {
        return true;
    }
//    是否锁定
    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }
// 是否启用
    @Override
    public boolean isEnabled() {
        return true;
    }
}
```



# 登录过滤器

```java
package com.securityjwt.demo.filter;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.securityjwt.demo.util.JwtUtil;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.web.authentication.AbstractAuthenticationProcessingFilter;
import org.springframework.security.web.util.matcher.AntPathRequestMatcher;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Collection;
import java.util.HashMap;
import java.util.Map;

public class JwtLoginFilter extends AbstractAuthenticationProcessingFilter {

    public JwtLoginFilter(String defaultFilterProcessesUrl, AuthenticationManager authenticationManager) {
        super(new AntPathRequestMatcher(defaultFilterProcessesUrl));
        setAuthenticationManager(authenticationManager);
    }

    @Override
    public Authentication attemptAuthentication(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws AuthenticationException, IOException, ServletException {
        JwtUser user = new ObjectMapper().readValue(httpServletRequest.getInputStream(),JwtUser.class);
        return getAuthenticationManager().authenticate(new UsernamePasswordAuthenticationToken(user.getUsername(),user.getPassword()));
    }


    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse resp, FilterChain chain, Authentication authResult) throws IOException, ServletException {
        Collection<? extends GrantedAuthority> authorities =  authResult.getAuthorities();

        StringBuffer stringBuffer = new StringBuffer();
        for (GrantedAuthority authority : authorities) {
            stringBuffer.append(authority.getAuthority()).append(",");
        }
        String roles = stringBuffer.toString();
        System.out.println(roles);

        String jwt = new JwtUtil().createToken(authResult.getName(),roles);
        System.out.println(jwt);
        Map<String, String> map = new HashMap<>();
        map.put("token",jwt);
        map.put("msg","登录成功");

        resp.setContentType("application/json;charset=utf-8");
        resp.setHeader("authorization",jwt);

        PrintWriter out = resp.getWriter();
//      登录成功返回jwt
        out.write(new ObjectMapper().writeValueAsString(map));
        out.flush();
        out.close();
    }

    @Override
    protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse resp, AuthenticationException failed) throws IOException, ServletException {
        Map<String, String> map = new HashMap<>();
        map.put("msg","登录失败");
        resp.setContentType("application/json;charset=utf-8");
        PrintWriter out = resp.getWriter();
        out.write(new ObjectMapper().writeValueAsString(map));
        out.flush();
        out.close();
    }


}
```

# 效验token过滤器

```java
package com.securityjwt.demo.filter;

import com.securityjwt.demo.util.JwtUtil;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.web.filter.GenericFilterBean;
import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;
import java.util.List;

public class JwtFilter extends GenericFilterBean {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest req = (HttpServletRequest) servletRequest;
        String jwtToken = req.getHeader("authorization");

        System.out.println(jwtToken);
        System.out.println(jwtToken.replace("Bearer",""));
        String username = new JwtUtil().getUsername(jwtToken.replace("Bearer",""));
        /**
         * 获取roles
         */
//        List<GrantedAuthority> authorities = AuthorityUtils.commaSeparatedStringToAuthorityList((String) new JwtUtil().getAuthorities(jwtToken.replace("Bearer","")));
        List<SimpleGrantedAuthority> authorities = new JwtUtil().getUserRolesByToken(jwtToken.replace("Bearer",""));
        System.out.println(authorities);
        UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken(authorities, username, authorities);
        SecurityContextHolder.getContext().setAuthentication(token);

        filterChain.doFilter(servletRequest, servletResponse);
    }
}
```

# jwt工具类

```java
package com.securityjwt.demo.util;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.exceptions.JWTDecodeException;
import com.auth0.jwt.interfaces.DecodedJWT;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.stereotype.Component;

import java.util.*;
import java.util.stream.Collectors;

/**
 * @author huwen
 */
@Component
public class JwtUtil {
    /**
     * 设置过期时间24小时
     */
    private static final long EXPIRE_TIME = 1000*60;
    /**
     * 设置密钥
     */
    private static final String SECRET = "shiro+jwt";

    /**
     * 根据用户名创建一个token
     * @param username 用户名
     * @return 返回的token字符串
     */
    public String createToken(String username, String roles){
        try {
            //将当前时间的毫秒数和设置的过期时间相加生成一个新的时间
            Date date = new Date(System.currentTimeMillis()+EXPIRE_TIME);
            //由密钥创建一个指定的算法
            return JWT.create()
                    //附带username信息
                    .withClaim("username",username)
                    .withClaim("authorities",String.join(",", roles))
//                    .withClaim("authorities", Arrays.asList(authorities))
//                    .withArrayClaim("authorities",authorities)
                    //附带过期时间
                    .withExpiresAt(date)
                    //使用指定的算法进行标记
                    .sign(Algorithm.HMAC256(SECRET));
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 验证token是否正确
     * @param token 前端传过来的token
     * @param username 用户名
     * @return 返回boolean
     */
    public boolean verify(String token,String username){
        try {
            //获取算法
            Algorithm algorithm = Algorithm.HMAC256(SECRET);
            //生成JWTVerifier
            JWTVerifier verifier = JWT.require(algorithm)
                    .withClaim("username",username)
                    .build();
            //验证token
            verifier.verify(token);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 从token中获得username，无需secret
     * @param token token
     * @return username
     */
    public String getUsername(String token){
        try {
            DecodedJWT jwt = JWT.decode(token);
            return jwt.getClaim("username").asString();
        } catch (JWTDecodeException e) {
            return null;
        }
    }

    /**
     * 获取roles
     * @param token
     * @return
     */
    public String getAuthorities(String token){
        try {
            DecodedJWT jwt = JWT.decode(token);
            System.out.println(jwt.getClaim("authorities").asString());
            return jwt.getClaim("authorities").asString();
        } catch (JWTDecodeException e) {
            return null;
        }
    }

    /**
     *获取roles
     * @param token
     * @return
     */
    public List<SimpleGrantedAuthority> getUserRolesByToken(String token) {
        DecodedJWT jwt = JWT.decode(token);
        String role = jwt.getClaim("authorities").asString();
        return Arrays.stream(role.split(","))
                .map(SimpleGrantedAuthority::new)
                .collect(Collectors.toList());
    }
}
```

# 参考网站：

[http://docs.javaboy.org/springboot/security-jwt.html#_2-2-jwt-%E8%BF%87%E6%BB%A4%E5%99%A8%E9%85%8D%E7%BD%AE](http://docs.javaboy.org/springboot/security-jwt.html#_2-2-jwt-过滤器配置)

# 刷新token:

https://www.sundayfine.com/shiro-jwt/