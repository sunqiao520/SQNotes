## 1、在阿里云上注册账号并开通OSS服务


![](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625103141909-df732abe-35a0-44e5-8808-634264d57b14.png#align=left&display=inline&height=489&margin=%5Bobject%20Object%5D&originHeight=489&originWidth=1215&size=0&status=done&style=none&width=1215)
开通后进入控制台。


## 2、创建Bucket


![](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625103141911-8aada2fa-fb6f-44a2-8286-7f366b33b9c5.png#align=left&display=inline&height=822&margin=%5Bobject%20Object%5D&originHeight=822&originWidth=998&size=0&status=done&style=none&width=998)
根据需求创建即可。设置读写权限为公共读。
![](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625103141877-6c0d6f6f-77ac-40d7-9608-dd2079655803.png#align=left&display=inline&height=81&margin=%5Bobject%20Object%5D&originHeight=81&originWidth=314&size=0&status=done&style=none&width=314)


## 3、记录重要数据


1. Endpoint 地域结点
![](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625103141999-da709b99-4a38-4b30-a90b-e10a3e9eb6d6.png#align=left&display=inline&height=567&margin=%5Bobject%20Object%5D&originHeight=567&originWidth=1191&size=0&status=done&style=none&width=1191)
1. AccessKey 的创建
![](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625103142034-194f0703-c06c-4ad3-bea9-a0f57c1f980a.png#align=left&display=inline&height=535&margin=%5Bobject%20Object%5D&originHeight=535&originWidth=435&size=0&status=done&style=none&width=435)
记录创建成功的AccessKeyId 和 AccessSecret



3、上面创建Bucket时所取得名字 BucketName


## 4、SpringBoot 整合


文件目录如下：
![](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625103141892-6f590ef4-7a96-457b-adc7-ffa6174fa431.png#align=left&display=inline&height=352&margin=%5Bobject%20Object%5D&originHeight=352&originWidth=364&size=0&status=done&style=none&width=364)


4.1、创建一个上传模块 在pom文件中引入依赖


```xml
        <!-- 阿里云oss依赖 -->
        <dependency>
            <groupId>com.aliyun.oss</groupId>
            <artifactId>aliyun-sdk-oss</artifactId>
            <version>3.1.0</version>
        </dependency>

        <!-- 日期工具栏依赖 -->
        <dependency>
            <groupId>joda-time</groupId>
            <artifactId>joda-time</artifactId>
            <version>2.10.1</version>
        </dependency>
```


4.2、配置application.properties


```yaml
#阿里云 OSS
#不同的服务器，地址不同
aliyun.oss.file.endpoint=your endpoint
aliyun.oss.file.keyid=your accessKeyId
aliyun.oss.file.keysecret=your accessKeySecret
aliyun.oss.file.bucketname=your bucketname
```


4.3、通过工具类从配置文件中获取配置


```java
@Component
public class ConstantPropertiesUtils implements InitializingBean {

    //读取配置文件内容
    @Value("${aliyun.oss.file.endpoint}")
    private String endpoint;

    @Value("${aliyun.oss.file.keyid}")
    private String keyId;

    @Value("${aliyun.oss.file.keysecret}")
    private String keySecret;

    @Value("${aliyun.oss.file.bucketname}")
    private String bucketName;

    //定义公开静态常量
    public static String END_POIND;
    public static String ACCESS_KEY_ID;
    public static String ACCESS_KEY_SECRET;
    public static String BUCKET_NAME;

    @Override
    public void afterPropertiesSet() throws Exception {
        END_POIND = endpoint;
        ACCESS_KEY_ID = keyId;
        ACCESS_KEY_SECRET = keySecret;
        BUCKET_NAME = bucketName;
    }
}
```


4.4、创建文件上传接口


```java
public interface OssService {
    //上传头像到oss
    String uploadFileAvatar(MultipartFile file);
}
```


4.5、实现上传功能


```java
@Service
public class OssServiceImpl implements OssService {
    //上传头像到oss
    @Override
    public String uploadFileAvatar(MultipartFile file) {
        // 工具类获取值
        String endpoint = ConstantPropertiesUtils.END_POIND;
        String accessKeyId = ConstantPropertiesUtils.ACCESS_KEY_ID;
        String accessKeySecret = ConstantPropertiesUtils.ACCESS_KEY_SECRET;
        String bucketName = ConstantPropertiesUtils.BUCKET_NAME;

        try {
            // 创建OSS实例。
            OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

            //获取上传文件输入流
            InputStream inputStream = file.getInputStream();
            //获取文件名称
            String fileName = file.getOriginalFilename();

            //1 在文件名称里面添加随机唯一的值
            String uuid = UUID.randomUUID().toString().replaceAll("-","");
            // yuy76t5rew01.jpg
            fileName = uuid+fileName;

            //2 把文件按照日期进行分类
            //获取当前日期
            String datePath = new DateTime().toString("yyyy/MM/dd");
            //拼接
            fileName = datePath+"/"+fileName;

            //调用oss方法实现上传
            //第一个参数  Bucket名称
            //第二个参数  上传到oss文件路径和文件名称   aa/bb/1.jpg
            //第三个参数  上传文件输入流
            ossClient.putObject(bucketName,fileName , inputStream);

            // 关闭OSSClient。
            ossClient.shutdown();

            //把上传之后文件路径返回
            //需要把上传到阿里云oss路径手动拼接出来
            String url = "https://"+bucketName+"."+endpoint+"/"+fileName;
            return url;
        }catch(Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}
```


4.6、最后在Controller 中调用获得图片的url


```java
public class OssController {


    @Autowired
    private OssService ossService;
    //上传头像的方法
    @PostMapping
    public R uploadOssFile(MultipartFile file) {
        //获取上传文件  MultipartFile
        //返回上传到oss的路径
        String url = ossService.uploadFileAvatar(file);
        return R.ok().data("url",url);
    }
}
```


## 5、Swagger测试


![](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625103141933-d46f7e9c-92c6-4fbc-989f-62bf85162ba3.png#align=left&display=inline&height=860&margin=%5Bobject%20Object%5D&originHeight=860&originWidth=699&size=0&status=done&style=none&width=699)
上传成功
![](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625103141928-fc066cb0-c4d0-4069-8377-beb02732da37.png#align=left&display=inline&height=274&margin=%5Bobject%20Object%5D&originHeight=274&originWidth=207&size=0&status=done&style=none&width=207)
在自己的阿里云OSS文件管理中可以查看图片。


详细学习可参考阿里云官网的技术文档
[https://help.aliyun.com/product/31815.html?spm=a2c4g.11186623.6.540.796c5338hrsRHP](https://help.aliyun.com/product/31815.html?spm=a2c4g.11186623.6.540.796c5338hrsRHP)
