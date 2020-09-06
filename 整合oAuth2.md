# 依赖

```yml
<!-- https://mvnrepository.com/artifact/me.zhyd.oauth/JustAuth -->
<dependency>
    <groupId>me.zhyd.oauth</groupId>
    <artifactId>JustAuth</artifactId>
    <version>1.15.6</version>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

# Github登录平台

## 第一种方法：

```java
package com.oauth2.demo.controller;

import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.client.RestTemplate;

import java.util.HashMap;
import java.util.Map;

@Controller
public class TsetHtml {

    @GetMapping("/github")
    public String test2() {
        return "github";
    }

    /**
     * 当我们点击 GitHub 图标，完成授权操作之后，会自动跳转到我们在准备工作填的回调地址中，
     * 并且携带一个 code 参数，拿着这个 code 参数我们就可以去获取 access_token 了，
     * 有了 access_token 我们就可以获取到用户信息了。
     * @param code
     * @return
     */
    @GetMapping("/github_code")
    public String authorization_code(String code) {
        Map<String, String> map = new HashMap<>();
        map.put("client_id", "7f6178a30d5726427163");
        map.put("client_secret", "951fee61bed7cca5de68d682d59529745263002b");
        map.put("state", "javaboy");
        map.put("code", code);
        map.put("redirect_uri", "http://localhost:8081/github_code");
        System.out.println( Map.class);
        Map<String,String> resp =new RestTemplate().postForObject("https://github.com/login/oauth/access_token", map, Map.class);

        HttpHeaders httpheaders = new HttpHeaders();
        httpheaders.add("Authorization", "token " + resp.get("access_token"));
        HttpEntity<?> httpEntity = new HttpEntity<>(httpheaders);
        ResponseEntity<Map> exchange = new RestTemplate().exchange("https://api.github.com/user", HttpMethod.GET, httpEntity, Map.class);
        System.out.println("exchange.getBody() = " + exchange.getBody());

        /**
         * https://api.github.com/user?access_token=defae1dc85b77dd79936feb1df1a547e0e4fd511
         * 登录成功个人信息地址
         */
//        登录成功跳转到github页面
        return "forward:/github";
    }
}
```

## 第二种方式（使用jar包）

### https://justauth.wiki/#/

```java
package com.oauth2.demo.controller;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import me.zhyd.oauth.config.AuthConfig;
import me.zhyd.oauth.request.AuthGiteeRequest;
import me.zhyd.oauth.model.AuthCallback;
import me.zhyd.oauth.request.AuthGithubRequest;
import me.zhyd.oauth.request.AuthOschinaRequest;
import me.zhyd.oauth.request.AuthRequest;
import me.zhyd.oauth.utils.AuthStateUtils;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.HashMap;
import java.util.Map;


@Controller
public class RestAuthController {

    /**
     * github
     * @param response
     * @throws IOException
     */
// 请求授权页面
    @RequestMapping("/github_render")
    public void renderAuth(HttpServletResponse response) throws IOException {
        AuthRequest authRequest = getAuthRequest();
        response.sendRedirect(authRequest.authorize(AuthStateUtils.createState()));
    }
//返回code 
    @RequestMapping("/github_code")
    public Object github_code(String code) {
        /**
         * 授权码
         */
        System.out.println(code);
        return "github";
    }

    private AuthRequest getAuthRequest() {
        return new AuthGithubRequest(AuthConfig.builder()
                .clientId("7f6178a30d5726427163")
                .clientSecret("951fee61bed7cca5de68d682d59529745263002b")
                .redirectUri("http://localhost:8081/github_code")
                .build());
    }
}
```

![image-20200822182644220](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200822182644220.png)

# Gitee

```java
package com.oauth2.demo.controller;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import me.zhyd.oauth.config.AuthConfig;
import me.zhyd.oauth.request.AuthGiteeRequest;
import me.zhyd.oauth.model.AuthCallback;
import me.zhyd.oauth.request.AuthGithubRequest;
import me.zhyd.oauth.request.AuthOschinaRequest;
import me.zhyd.oauth.request.AuthRequest;
import me.zhyd.oauth.utils.AuthStateUtils;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.HashMap;
import java.util.Map;


@Controller
public class RestAuthController {
    /**
     * gitee
     * @return
     */
    @GetMapping("/gitee")
    public String gitee() {
        return "gitee";
    }

//    授权页面
    @RequestMapping("/render/{source}")
    public void renderAuth(HttpServletResponse response) throws IOException {
        AuthRequest authRequest = getAuthRequest();
        response.sendRedirect(authRequest.authorize(AuthStateUtils.createState()));
    }

    @GetMapping("/callback/{source}")
    public void login(String code,HttpServletResponse response) throws IOException {
        Map<String, String> map = new HashMap<>();
        map.put("msg","登录成功");
        map.put("data",code);
        response.setContentType("application/json;charset=utf-8");
        PrintWriter out = response.getWriter();
        out.write(new ObjectMapper().writeValueAsString(map));
        out.flush();
        out.close();
    }

    private AuthRequest getAuthRequest() {
        return new AuthGiteeRequest(AuthConfig.builder()
                .clientId("3cee9e11851333e5cc728faddd1d4a01de1a583cec4bf4cce111cf4bab7875e5")
                .clientSecret("d78a155704aa2a2fb9fa53fbd015f1ba848204ad71b930159d0d2a1c5a710b6d")
                .redirectUri("http://localhost:8081/callback/gitee")
                .build());
    }

    /**
     * oschina 开源中国
     */

//    @RequestMapping("/oschina_render")
//    public void renderAuth(HttpServletResponse response) throws IOException {
//        AuthRequest authRequest = getAuthRequest();
//        response.sendRedirect(authRequest.authorize(AuthStateUtils.createState()));
//    }
//
//    @RequestMapping("/oschina_code")
//    public Object login(String code) {
//        System.out.println(code);
//        return "github";
//    }
//
//    private AuthRequest getAuthRequest() {
//        return new AuthOschinaRequest(AuthConfig.builder()
//                .clientId("nddPw6h1Moq3B6yiM2lK")
//                .clientSecret("8TPFxCXyOip62l2yxTHsBVLCYgYQ6zj0")
//                .redirectUri("http://localhost:8081/oschina_code")
//                .build());
//    }
}
```

![image-20200822182546268](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200822182546268.png)

# 其他平台

跟上面的差不多



# 思考怎么跟自己的jwt整合使用