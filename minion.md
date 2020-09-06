# 依赖

```xml
<dependency>
    <groupId>io.minio</groupId>
    <artifactId>minio</artifactId>
    <version>7.1.0</version>
</dependency>
```

# 配置

```java
import java.io.IOException;
import java.security.NoSuchAlgorithmException;
import java.security.InvalidKeyException;

import org.xmlpull.v1.XmlPullParserException;

import io.minio.MinioClient;
import io.minio.errors.MinioException;

public class FileUploader {
  public static void main(String[] args) throws NoSuchAlgorithmException, IOException, InvalidKeyException, XmlPullParserException {
    try {
      // Create a minioClient with the MinIO Server name, Port, Access key and Secret key.
      MinioClient minioClient = new MinioClient("https://play.min.io", "Q3AM3UQ867SPQQA43P2F", "zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG");

      // Check if the bucket already exists.
      boolean isExist = 
        minioClient.bucketExists(BucketExistsArgs.builder().bucket("asiatrip").build());
      if(isExist) {
        System.out.println("Bucket already exists.");
      } else {
        // Make a new bucket called asiatrip to hold a zip file of photos.
        minioClient.makeBucket(MakeBucketArgs.builder().bucket("asiatrip").build());
      }

      // Upload the zip file to the bucket with putObject
      minioClient.putObject("asiatrip","asiaphotos.zip", "/home/user/Photos/asiaphotos.zip", null);
      System.out.println("/home/user/Photos/asiaphotos.zip is successfully uploaded as asiaphotos.zip to `asiatrip` bucket.");
    } catch(MinioException e) {
      System.out.println("Error occurred: " + e);
    }
  }
}

```

![image-20200824054734923](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200824054734923.png)



# windows下载

> 官网

https://docs.min.io/cn/minio-quickstart-guide.html

下载地址

> https://dl.min.io/server/minio/release/windows-amd64/minio.exe

# 启动

> minio.exe server E:\sousuo\minio



![image-20200824054950201](C:\Users\17140\AppData\Roaming\Typora\typora-user-images\image-20200824054950201.png)

# 测试

https://mp.weixin.qq.com/s/kvLZRqgm1lEITm1j6rzJ0A