```xml
<!--   文件上传     -->
        <!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.4</version>
        </dependency>
```

![image-20200918085638270](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200918085638270.png)







ServletFileUpload ->  FileUpload ->  FileUploadBase 





DiskFileItemFactory -> FileItemFactory

![image-20200918090323526](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200918090323526.png)

![image-20200918090339475](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200918090339475.png)

配置

```
spring.servlet.multipart.enabled=false
```



```java
package com.myblog.demo.controller;
import com.myblog.demo.entity.UserEntity;
import com.myblog.demo.service.BannerService;
import org.apache.commons.fileupload.FileItem;
import org.apache.commons.fileupload.disk.DiskFileItemFactory;
import org.apache.commons.fileupload.servlet.ServletFileUpload;
import org.apache.commons.fileupload.servlet.ServletRequestContext;
import org.apache.commons.io.FileUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.util.List;

@RestController
public class TestController {

    @RequestMapping("/testfile")
    public String testFIle(HttpServletRequest request, HttpServletResponse response) throws Exception {
        DiskFileItemFactory diskFileItemFactory = new DiskFileItemFactory();
        ServletFileUpload fileUpload = new ServletFileUpload(diskFileItemFactory);
        fileUpload.setHeaderEncoding("utf-8");
        List<FileItem> fileItems = fileUpload.parseRequest(new ServletRequestContext(request));

        System.out.println(fileItems);
        for (FileItem item:fileItems){
            System.out.println(item.getName());
            System.out.println(item.getSize());
            System.out.println(item.getFieldName());

            InputStream inputStream = item.getInputStream();
//            保存文件
            File file = new File("com.myblog.demo.file", item.getName());
            FileUtils.copyInputStreamToFile(inputStream,file);
        }
        fileItems.clear();
        return "fileItems";
    }

    @RequestMapping(value="/fileUploadController")
    public String fileUpload(@RequestParam("file") MultipartFile file) {
        if (file.isEmpty()) {
            return "上传失败，请选择文件";
        }

        String fileName = file.getOriginalFilename();
        System.out.println(fileName);
        return fileName;
    }
    
     /**
     * 文件下载
     * @return
     */
    @RequestMapping(value="/filedown")
    public void filedown(HttpServletRequest request, HttpServletResponse response) throws IOException {
        File file = new File("com.myblog.demo.file\\灯泡.png");
        InputStream in = new FileInputStream(file);
        response.setContentType("text/html; charset=utf-8");
        ServletOutputStream out = response.getOutputStream();
        IOUtils.copy(in,out);
        in.close();
        out.close();
    }
}

```



html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>test html</h1>
<form method="post" action="/testfile" enctype="multipart/form-data">
    <input type="file" name="file"><br>
    <input type="submit" value="提交">
</form>

</body>
</html>
```

