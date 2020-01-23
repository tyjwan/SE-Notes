# Java CSV
***
## Maven
```java
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.6</version>
</dependency>
```

## Code
```java
/***
     * 读取相应的配置文件
     * @param fileName
     * @throws IOException
     */
    private static void init(String fileName) throws IOException {
        File csvData = new File(fileName);
        CSVParser parser = CSVParser.parse(csvData, Charset.defaultCharset(),CSVFormat.EXCEL);
        for (CSVRecord csvRecord : parser) {
            System.out.println(csvRecord);
            try {
                int sid = Integer.valueOf(csvRecord.get(0));
                String isxchg = csvRecord.get(1);
                String nasName = csvRecord.get(2);
                if(!nasName.equals("-")) nas.put(sid, nasName);
                if(isxchg.equals("xchg")) exchangeSrc2Dst.add(sid);
            }
            catch (Exception e) {
                continue;
            }
        }
        initConfig = true;
        System.out.println(nas.toString());
        System.out.println(exchangeSrc2Dst.toString());
    }
```

## 参考链接
- [Using Apache Commons CSV](https://commons.apache.org/proper/commons-csv/)
- [Class CSVParser](https://commons.apache.org/proper/commons-csv/archives/1.6/apidocs/index.html)
- [Java从.CSV文件中读取数据和写入](https://www.cnblogs.com/leihupqrst/p/3508410.html)