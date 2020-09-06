```java
package com.shirojwt.demo.jwt;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.exceptions.JWTDecodeException;
import com.auth0.jwt.interfaces.DecodedJWT;
import org.springframework.stereotype.Component;

import java.util.Date;

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
    public String createToken(String username){
        try {
            //将当前时间的毫秒数和设置的过期时间相加生成一个新的时间
            Date date = new Date(System.currentTimeMillis()+EXPIRE_TIME);
            //由密钥创建一个指定的算法
            return JWT.create()
                    //附带username信息
                    .withClaim("username",username)
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
}
```

