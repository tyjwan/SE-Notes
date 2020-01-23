# File 与 MultipartFile 互转
***
```java
@Test
public void convert() throws Exception {
	File unitCSV = new File("docs/units.csv");
	FileInputStream inputStream = new FileInputStream(unitCSV);
	MultipartFile multipartFile = new MockMultipartFile(unitCSV.getName(), inputStream);
}
```

```java
<dependency>
     <groupId>org.mockito</groupId>
     <artifactId>mockito-core</artifactId>
     <version>2.5.5</version>
     <scope>test</scope>
</dependency>
```

## 参考链接
- [Junit测试之File转MultipartFile和MultipartFile转File](https://blog.csdn.net/ljh_learn_from_base/article/details/84578009)
- [测试接收MultipartFile的方法](https://blog.csdn.net/yshuoo/article/details/82848896)
- [springboot 文件上传大小配置](https://blog.csdn.net/shi0299/article/details/69525848)
