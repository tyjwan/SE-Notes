# Java String convert to InuputStream
***
## Code
```java
package com.mkyong;

import java.io.BufferedReader;
import java.io.ByteArrayInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;

public class StringToInputStreamExample {
    public static void main(String[] args) throws IOException {
	String str = "This is a String ~ GoGoGo";

	// convert String into InputStream
	InputStream is = new ByteArrayInputStream(str.getBytes());

	// read it with BufferedReader
	BufferedReader br = new BufferedReader(new InputStreamReader(is));

	String line;
	while ((line = br.readLine()) != null) {
		System.out.println(line);
	}

	br.close();
   }
}
```

## 参考链接
- [How to convert String to InputStream in Java](https://www.mkyong.com/java/how-to-convert-string-to-inputstream-in-java/)