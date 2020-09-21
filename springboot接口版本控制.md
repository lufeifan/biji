## 定义注解

```java
package com.apiversion.demo.hander;

import org.springframework.web.bind.annotation.Mapping;

import java.lang.annotation.*;

/**
 * 版本控制
 * @author Zac
 */
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface ApiVersion {
    /**
     * 标识版本号
     * @return
     */
    double value();
}
```



## 版本号筛选器ApiVersionCondition

```java
package com.apiversion.demo.hander;

import org.springframework.web.servlet.mvc.condition.RequestCondition;

import javax.servlet.http.HttpServletRequest;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

/**
 * 版本号匹配筛选器
 * @author Zac
 */
public class ApiVersionCondition implements RequestCondition<ApiVersionCondition> {

    /**
     * 路径中版本的正则表达式匹配， 这里用 /1.0的形式
     */
    private static final Pattern VERSION_PREFIX_PATTERN = Pattern.compile("^\\S+/([1-9][.][0-9])$");

    private double apiVersion;

    public ApiVersionCondition(double apiVersion) {
        this.apiVersion = apiVersion;
    }

    @Override
    public ApiVersionCondition combine(ApiVersionCondition other) {
        // 采用最后定义优先原则，则方法上的定义覆盖类上面的定义
        return new ApiVersionCondition(other.getApiVersion());
    }

    @Override public ApiVersionCondition getMatchingCondition(HttpServletRequest request) {

        Matcher m = VERSION_PREFIX_PATTERN.matcher(request.getRequestURI());
        if (m.find()) {
            Double version = Double.valueOf(m.group(1));
            System.out.println(version);
            // 如果请求的版本号大于配置版本号， 则满足
            if (version >= this.apiVersion) {
                return this;
            }
        }
        return null;
    }

    @Override
    public int compareTo(ApiVersionCondition other, HttpServletRequest request) {
        // 优先匹配最新的版本号
        return Double.compare(other.getApiVersion(), this.apiVersion);
    }

    public double getApiVersion() {
        return apiVersion;

    }
}
```



# 版本号匹配拦截器

```java
package com.apiversion.demo.hander;

import org.springframework.core.annotation.AnnotationUtils;
import org.springframework.web.servlet.mvc.condition.RequestCondition;
import org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping;
 
import java.lang.reflect.Method;
 
/**
 * 版本号匹配拦截器
 * @author Zac
 */
public class CustomRequestMappingHandlerMapping extends RequestMappingHandlerMapping {
 
    @Override protected RequestCondition<ApiVersionCondition> getCustomTypeCondition(Class<?> handlerType) {
        /**
         * 通过注解获取 apiVersion 的 value 值
         */
        ApiVersion apiVersion = AnnotationUtils.findAnnotation(handlerType, ApiVersion.class);
        System.out.println(apiVersion);
        return createCondition(apiVersion);
    }
 
    @Override protected RequestCondition<ApiVersionCondition> getCustomMethodCondition(Method method) {
        ApiVersion apiVersion = AnnotationUtils.findAnnotation(method, ApiVersion.class);
        return createCondition(apiVersion);
    }
 
    private RequestCondition<ApiVersionCondition> createCondition(ApiVersion apiVersion) {
        return apiVersion == null ? null : new ApiVersionCondition(apiVersion.value());
    }
}
```



## 配置

```java
package com.apiversion.demo.config;

import com.apiversion.demo.hander.CustomRequestMappingHandlerMapping;

import org.springframework.context.annotation.Configuration;
import org.springframework.format.support.FormattingConversionService;
import org.springframework.web.accept.ContentNegotiationManager;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;
import org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping;
import org.springframework.web.servlet.resource.ResourceUrlProvider;

@Configuration
public class WebMvcConfig extends WebMvcConfigurationSupport {

    @Override
    public RequestMappingHandlerMapping requestMappingHandlerMapping(ContentNegotiationManager contentNegotiationManager, FormattingConversionService conversionService, ResourceUrlProvider resourceUrlProvider) {
        return new CustomRequestMappingHandlerMapping();
    }
}

```

## 控制器

```java
package com.apiversion.demo.controller;

import com.apiversion.demo.hander.ApiVersion;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    /**
     *
     * @return
     */
    @RequestMapping("/demo1/{version}")
    @ApiVersion(1.0)
    public String demo1(){
        return "demo1";
    }

    @RequestMapping("/demo1/{version}")
    @ApiVersion(2.0)
    public String demo2(){
        return "demo2";
    }

    @RequestMapping("/demo1/{version}")
    @ApiVersion(3.0)
    public String demo3(){
        return "demo3";
    }
}

```

