# Mybatis 错误记录
***
## 实体与数据库中数据对不上
### 相关错误数据
- nested exception is org.apache.ibatis.executor.result.ResultMapException: Error attempting to get column 'description' from result set.  Cause: java.lang.NumberFormatException: For input string: "description"
- org.mybatis.spring.MyBatisSystemException: nested exception is org.apache.ibatis.exceptions.PersistenceException: Cause: java.lang.IndexOutOfBoundsException: Index 2 out of bounds for length 2

### 错误说明
- java的类反射拿到的数据不完整

### 解决办法
不返回实体，返回map