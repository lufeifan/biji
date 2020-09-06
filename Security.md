# 角色继承Roles

```java
@Bean
RoleHierarchy roleHierarchy() {
    RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
    String hierarchy = "ROLE_dba > ROLE_admin \n ROLE_admin > ROLE_user";
    roleHierarchy.setHierarchy(hierarchy);
    return roleHierarchy;
}
```

用法

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests().antMatchers("/admin/**")
            .hasRole("admin")
            .antMatchers("/db/**")
            .hasRole("dba")
            .antMatchers("/user/**")
            .hasRole("user")
            .and()
            .formLogin()
            .loginProcessingUrl("/doLogin")
            .permitAll()
            .and()
            .csrf().disable();
}
```

# 非法请求返回json

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
            .anyRequest().authenticated()
            .formLogin()
            .loginProcessingUrl("/doLogin")
            .loginPage("/login")
            //其他配置
            .permitAll()
            .and()
            .csrf().disable().exceptionHandling()
                .authenticationEntryPoint(new AuthenticationEntryPoint() {
            @Override
            public void commence(HttpServletRequest req, HttpServletResponse resp, AuthenticationException authException) throws IOException, ServletException {
                
                resp.setContentType("application/json;charset=utf-8");
                
                PrintWriter out = resp.getWriter();
                RespBean respBean = RespBean.error("访问失败!");
                if (authException instanceof InsufficientAuthenticationException) {
                    respBean.setMsg("请求失败，请联系管理员!");
                }
                out.write(new ObjectMapper().writeValueAsString(respBean));
                out.flush();
                out.close();
            }
        });
}
```

