```java
package com.myblog.demo.handle;

import com.baomidou.mybatisplus.extension.exceptions.ApiException;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.myblog.demo.comment.JsonReturn;
import org.springframework.core.MethodParameter;
import org.springframework.http.MediaType;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.http.server.ServerHttpRequest;
import org.springframework.http.server.ServerHttpResponse;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import org.springframework.web.servlet.mvc.method.annotation.ResponseBodyAdvice;

@RestControllerAdvice
public class MyControllerAdvice implements ResponseBodyAdvice<Object> {
    @Override
    public boolean supports(MethodParameter methodParameter, Class<? extends HttpMessageConverter<?>> aClass) {
        return !methodParameter.getGenericParameterType().equals(JsonReturn.class);
    }

    @Override
    public Object beforeBodyWrite(Object o, MethodParameter methodParameter, MediaType mediaType, Class<? extends HttpMessageConverter<?>> aClass,
                                  ServerHttpRequest serverHttpRequest, ServerHttpResponse serverHttpResponse) {
        /**
         * String 类型进行特殊处理
         */
        if (methodParameter.getGenericParameterType().equals(String.class)){
            ObjectMapper objectMapper = new ObjectMapper();
            try{
                return objectMapper.writeValueAsString(new JsonReturn().buildSuccess(o));
            } catch (JsonProcessingException e) {
                throw new ApiException("返回String 类型错误");
            }
        }
        return new JsonReturn().buildSuccess(o);
    }
}

```

