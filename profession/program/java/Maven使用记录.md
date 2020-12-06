# Maven使用记录
***
- [https://maven.aliyun.com/mvn/guide](https://maven.aliyun.com/mvn/guide)


- pom.xml


```java
    <repositories>
		<repository>
			<id>spring</id>
			<url>https://maven.aliyun.com/repository/spring</url>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
	</repositories> 
```

- setting.xml
- 
```xml
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>*</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

## 仓库地址
- [maven](https://repo.maven.apache.org/maven2/org/apache/shardingsphere/)
- [aliyun](https://maven.aliyun.com/mvn/search)