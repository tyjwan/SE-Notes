# IPV4地址与数字的互转
***
```java
package cn.nssas.eelantech.utils;

public class IPV4Util {

    /**
     * @param ipAddress
     * @return
     */
    public static long ipToLong(String ipAddress) {
        long result = 0;
        String[] ipAddressInArray = ipAddress.split("\\.");
        for (int i = 3; i >= 0; i--) {
            long ip = Long.parseLong(ipAddressInArray[3 - i]);
            // left shifting 24,16,8,0 and bitwise OR
            // 1. 192 << 24
            // 1. 168 << 16
            // 1. 1 << 8
            // 1. 2 << 0
            result |= ip << (i * 8);
        }
        return result;
    }

    /**
     * @param ip
     * @return
     */
    public static String longToIp(long ip) {
        StringBuilder result = new StringBuilder(15);
        for (int i = 0; i < 4; i++) {
            result.insert(0, Long.toString(ip & 0xff));
            if (i < 3) {
                result.insert(0, '.');
            }
            ip = ip >> 8;
        }
        return result.toString();
    }

    /**
     * @param ip
     * @return
     */
    public static String longToIp2(long ip) {
        return ((ip >> 24) & 0xFF) + "." + ((ip >> 16) & 0xFF) + "."
                + ((ip >> 8) & 0xFF) + "." + (ip & 0xFF);
    }
}
```

## 参考链接
- [Java IP地址转换和数字相互转换算法](http://www.what21.com/sys/view/java_java-algorithm_1456895959436.html)
- [Java IPv4地址和int类型数字的互相转换](https://www.jianshu.com/p/70185244e46c)