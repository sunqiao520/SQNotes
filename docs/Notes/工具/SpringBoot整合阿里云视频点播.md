## 1、开通阿里云视频点播

![image-20210701154943521](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210701154943521.png)

## 2、使用SDK快速开发

官方文档

![image-20210701155427205](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210701155427205.png)

### 2.1、创建环境

前提条件：阿里云实名认证与服务开通

服务端环境：Java 1.8 版本

引入依赖

```xml
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-core</artifactId>
    <version>4.5.1</version>
</dependency>
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.10.2</version>
</dependency>
 <dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-java-sdk-vod</artifactId>
    <version>2.15.11</version>
</dependency>
<!-- 此依赖未开源，需要自己下载再引入 -->
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>aliyun-sdk-vod-upload</artifactId>
    <version>1.4.11</version>
</dependency>

<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.28</version>
</dependency>
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20170516</version>
</dependency>
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.2</version>
</dependency>
```

在本地Maven仓库中安装jar包

下载视频上传SDK，解压，命令行进入lib 目录，执行以下代码：

```cmd
mvn install:install-file -DgroupId=com.aliyun -DartifactId=aliyun-sdk-vod-upload -Dversion=1.4.11 -Dpackaging=jar -Dfile=aliyun-java-vod-upload-1.4.11.jar
```

### 2.2、测试

1、根据视频 id 获取视频播放地址

<img src="https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210701160809179.png" alt="image-20210701160809179" style="zoom: 67%;" />

**代码实现：**

初始化client

```java
public class InitObject {
    public static DefaultAcsClient initVodClient(String accessKeyId, String accessKeySecret) throws ClientException {
        String regionId = "cn-shanghai";  // 点播服务接入区域
        DefaultProfile profile = DefaultProfile.getProfile(regionId, accessKeyId, accessKeySecret);
        DefaultAcsClient client = new DefaultAcsClient(profile);
        return client;
    }
}
```

```java
public static void getPlayUrl() throws Exception{
        //创建初始化对象
        DefaultAcsClient client = InitObject.initVodClient("<your accessKeyId>", "<your accessKeySecret>");

        //创建获取视频地址request和response
        GetPlayInfoRequest request = new GetPlayInfoRequest();
        GetPlayInfoResponse response = new GetPlayInfoResponse();

        //向request对象里面设置视频id
        request.setVideoId("29b44cb339374a309e4b4567aa25b7f5");

        //调用初始化对象里面的方法，传递request，获取数据
        response = client.getAcsResponse(request);

        List<GetPlayInfoResponse.PlayInfo> playInfoList = response.getPlayInfoList();
        //播放地址
        for (GetPlayInfoResponse.PlayInfo playInfo : playInfoList) {
            System.out.print("PlayInfo.PlayURL = " + playInfo.getPlayURL() + "\n");
        }
        //Base信息
        System.out.print("VideoBase.Title = " + response.getVideoBase().getTitle() + "\n");
    }
```

![image-20210701161806647](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210701161806647.png)

控制台输出地址。且在浏览器中可以直接观看。

![image-20210701161834114](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210701161834114.png)



2、根据视频id获取播放凭证

```java
 public static void getPlayAuth() throws Exception{

        DefaultAcsClient client = InitObject.initVodClient("<your accessKeyId>", "<your accessKeySecret>");

        GetVideoPlayAuthRequest request = new GetVideoPlayAuthRequest();
        GetVideoPlayAuthResponse response = new GetVideoPlayAuthResponse();

        request.setVideoId("29b44cb339374a309e4b4567aa25b7f5");

        response = client.getAcsResponse(request);
        System.out.println("playAuth:"+response.getPlayAuth());
    }
```

![image-20210701162147425](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210701162147425.png)

3、上传视频

```java
public class TestVod {
    public static void main(String[] args) throws Exception{

        //上传视频
        String accessKeyId = "<your accessKeyId>";
        String accessKeySecret = "<your accessKeySecret>";

        String title = "6 - What If I Want to Move Faster - upload by sdk";   //上传之后文件名称
        String fileName = "/path/文件名.mp4";  //本地文件路径和名称
        //上传视频的方法
        UploadVideoRequest request = new UploadVideoRequest(accessKeyId, accessKeySecret, title, fileName);
        /* 可指定分片上传时每个分片的大小，默认为2M字节 */
        request.setPartSize(2 * 1024 * 1024L);
        /* 可指定分片上传时的并发线程数，默认为1，(注：该配置会占用服务器CPU资源，需根据服务器情况指定）*/
        request.setTaskNum(1);

        UploadVideoImpl uploader = new UploadVideoImpl();
        UploadVideoResponse response = uploader.uploadVideo(request);

        if (response.isSuccess()) {
            System.out.print("VideoId=" + response.getVideoId() + "\n");
        } else {
            /* 如果设置回调URL无效，不影响视频上传，可以返回VideoId同时会返回错误码。其他情况上传失败时，VideoId为空，此时需要根据返回错误码分析具体错误原因 */
            System.out.print("VideoId=" + response.getVideoId() + "\n");
            System.out.print("ErrorCode=" + response.getCode() + "\n");
            System.out.print("ErrorMessage=" + response.getMessage() + "\n");
        }

//        getPlayUrl();
//        getPlayAuth();
    }
```

控制台打印出上传后的视频id

![image-20210701162557855](https://gitee.com/sun-qiao321/picture/raw/master/images/image-20210701162557855.png)

## 3、概念介绍

整体流程

![img](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/4572226061/p182454.png)

1. 用户获取上传授权。
2. VOD下发上传地址和凭证及VideoId。
3. 用户上传视频并保存视频ID（VideoId）。
4. 用户服务端获取播放授权。
5. 用户客户端请求播放地址与凭证，VOD下发播放地址与带时效的播放凭证。
6. 用户服务端将播放凭证下发给客户端完成视频播放。

