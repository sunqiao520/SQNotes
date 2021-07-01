# 1. 写操作
1. pom中引入xml相关依赖
```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/com.alibaba/easyexcel -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>easyexcel</artifactId>
        <version>2.1.1</version>
    </dependency>
</dependencies>
```

2. 创建实体类
```java
import com.alibaba.excel.annotation.ExcelProperty;
import lombok.Data;

@Data
public class DemoData {
    //设置excel表头名称
    @ExcelProperty(value = "学生编号",index = 0)
    private Integer sno;
    @ExcelProperty(value = "学生姓名",index = 1)
    private String sname;
}
```

3. 创建方法设置要添加到Excel 的数据,以 list集合返回
```java
private static List<DemoData> getData() {
    List<DemoData> list = new ArrayList<>();
    for (int i = 0; i < 10; i++) {
        DemoData data = new DemoData();
        data.setSno(i);
        data.setSname("lucy"+i);
        list.add(data);
    }
    return list;
}
```

4. 实现excel写的操作
```java
//        1 设置写入文件夹地址和excel文件名称
    String filename = "E:\\write.xlsx";
//        2 调用easyexcel里面的方法实现写操作
//        write方法两个参数：第一个参数文件路径名称，第二个参数实体类class
   EasyExcel.write(filename,DemoData.class).sheet("学生列表").doWrite(getData());
```

5. 结果

![image.png](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625105797448-3fb286b6-790a-4cfd-b566-db393ce5b9da.png#align=left&display=inline&height=169&margin=%5Bobject%20Object%5D&name=image.png&originHeight=338&originWidth=219&size=12472&status=done&style=none&width=109.5)
# 2. 读操作
在以上基础上创建读取操作的监听器
```java
public class ExcelListener extends AnalysisEventListener<DemoData> {
    //一行一行读取excel内容
    @Override
    public void invoke(DemoData data, AnalysisContext analysisContext) {
        System.out.println("****"+data);
    }
    //读取表头内容
    @Override
    public void invokeHeadMap(Map<Integer, String> headMap, AnalysisContext context) {
        System.out.println("表头："+headMap);
    }
    //读取完成之后
    @Override
    public void doAfterAllAnalysed(AnalysisContext analysisContext) { }
}
```
实现读操作
```java
        String filename = "E:\\write.xlsx";
        EasyExcel.read(filename,DemoData.class,new ExcelListener()).sheet().doRead();
```
结果
```java
表头：{0=学生编号, 1=学生姓名}
****DemoData(sno=0, sname=lucy0)
****DemoData(sno=1, sname=lucy1)
****DemoData(sno=2, sname=lucy2)
****DemoData(sno=3, sname=lucy3)
****DemoData(sno=4, sname=lucy4)
****DemoData(sno=5, sname=lucy5)
****DemoData(sno=6, sname=lucy6)
****DemoData(sno=7, sname=lucy7)
****DemoData(sno=8, sname=lucy8)
****DemoData(sno=9, sname=lucy9)
```
# 3. 项目中使用
## 3.1 需求
读取多级分类存储到数据库中
如:
后端开发:
Java
C++
前端开发:
vue
js
## 3.2 Excel 中的数据


![image.png](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625106926574-58fce93b-3d24-4b4e-bfa8-b6bafdafd7ee.png#align=left&display=inline&height=128&margin=%5Bobject%20Object%5D&name=image.png&originHeight=256&originWidth=197&size=9714&status=done&style=none&width=98.5)
## 3.3 数据库表设计
![image.png](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625107079541-44c39c4b-0f76-41df-bc1b-c7aff0f2680b.png#align=left&display=inline&height=96&margin=%5Bobject%20Object%5D&name=image.png&originHeight=191&originWidth=1026&size=132528&status=done&style=none&width=513)
## 3.4 项目中引入依赖
```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/com.alibaba/easyexcel -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>easyexcel</artifactId>
        <version>2.1.1</version>
    </dependency>
</dependencies>
```
## 3.5 创建和Excel对应的实体类
```java
import com.alibaba.excel.annotation.ExcelProperty;
import lombok.Data;
@Data
public class SubjectData {

    @ExcelProperty(index = 0)
    private String oneSubjectName;

    @ExcelProperty(index = 1)
    private String twoSubjectName;
}

```
## 3.6 读取Excel需要创建监听类
存储到数据库的逻辑在监听类中实现
```java
public class SubjectExcelListener extends AnalysisEventListener<SubjectData> {

    //因为SubjectExcelListener不交给spring进行管理，需要自己new，不能注入其他对象
    //不能实现数据库操作
    public EduSubjectService subjectService;
    public SubjectExcelListener() {}
    public SubjectExcelListener(EduSubjectService subjectService) {
        this.subjectService = subjectService;
    }

    //读取excel内容，一行一行进行读取
    @Override
    public void invoke(SubjectData subjectData, AnalysisContext analysisContext) {
        if(subjectData == null) {
            throw new GuliException(20001,"文件数据为空");
        }

        //一行一行读取，每次读取有两个值，第一个值一级分类，第二个值二级分类
        //判断一级分类是否重复
        EduSubject existOneSubject = this.existOneSubject(subjectService, subjectData.getOneSubjectName());
        if(existOneSubject == null) { //没有相同一级分类，进行添加
            existOneSubject = new EduSubject();
            existOneSubject.setParentId("0");
            existOneSubject.setTitle(subjectData.getOneSubjectName());//一级分类名称
            subjectService.save(existOneSubject);
        }

        //获取一级分类id值
        String pid = existOneSubject.getId();

        //添加二级分类
        //判断二级分类是否重复
        EduSubject existTwoSubject = this.existTwoSubject(subjectService, subjectData.getTwoSubjectName(), pid);
        if(existTwoSubject == null) {
            existTwoSubject = new EduSubject();
            existTwoSubject.setParentId(pid);
            existTwoSubject.setTitle(subjectData.getTwoSubjectName());//二级分类名称
            subjectService.save(existTwoSubject);
        }
    }

    //判断一级分类不能重复添加
    private EduSubject existOneSubject(EduSubjectService subjectService, String name) {
        QueryWrapper<EduSubject> wrapper = new QueryWrapper<>();
        wrapper.eq("title",name);
        wrapper.eq("parent_id","0");
        EduSubject oneSubject = subjectService.getOne(wrapper);
        return oneSubject;
    }

    //判断二级分类不能重复添加
    private EduSubject existTwoSubject(EduSubjectService subjectService,String name,String pid) {
        QueryWrapper<EduSubject> wrapper = new QueryWrapper<>();
        wrapper.eq("title",name);
        wrapper.eq("parent_id",pid);
        EduSubject twoSubject = subjectService.getOne(wrapper);
        return twoSubject;
    }

    @Override
    public void doAfterAllAnalysed(AnalysisContext analysisContext) {

    }
}
```
## 3.7 创建添加文件的接口
```java
 //添加课程分类
    //获取到上传过来的文件，把文件内容读取出来
    @PostMapping("addSubject")
    public R addSubject(MultipartFile file) {
        //上传过来excel文件
        subjectService.saveSubject(file,subjectService);
        return R.ok();
    }
```
## 3.8 在Service 中实现上传的方法
```java
//添加课程分类
    @Override
    public void saveSubject(MultipartFile file,EduSubjectService subjectService) {
        try {
            InputStream in = file.getInputStream();
            EasyExcel.read(in, SubjectData.class,new SubjectExcelListener(subjectService)).sheet().doRead();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```
## 3.9 swagger 测试
![image.png](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625107862555-f1c92d67-aa3e-46f6-9e90-622afa4ca2a6.png#align=left&display=inline&height=183&margin=%5Bobject%20Object%5D&name=image.png&originHeight=366&originWidth=649&size=18837&status=done&style=none&width=324.5)
数据库中查看，添加成功！！
![image.png](https://cdn.nlark.com/yuque/0/2021/png/21986860/1625108002083-1af58902-bf67-4278-980e-9f5f2c7d4164.png#align=left&display=inline&height=116&margin=%5Bobject%20Object%5D&name=image.png&originHeight=231&originWidth=1044&size=154012&status=done&style=none&width=522)
