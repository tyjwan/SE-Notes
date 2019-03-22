# IP相关
***
### IPV4地址与数字的互转
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

### java判断某个ip是否在一个网段内 ip/mask IP+掩码
```java
package com.ip;  
  
public class IpTest {  
    public static void main(String[] args) {  
        System.out.println(isInRange("192.168.1.127", "192.168.1.64/26"));  
        System.out.println(isInRange("192.168.1.2", "192.168.0.0/23"));  
        System.out.println(isInRange("192.168.0.1", "192.168.0.0/24"));  
        System.out.println(isInRange("192.168.0.0", "192.168.0.0/32"));  
    }  
    public static boolean isInRange(String ip, String cidr) {  
        String[] ips = ip.split("\\.");  
        int ipAddr = (Integer.parseInt(ips[0]) << 24)  
                | (Integer.parseInt(ips[1]) << 16)  
                | (Integer.parseInt(ips[2]) << 8) | Integer.parseInt(ips[3]);  
        int type = Integer.parseInt(cidr.replaceAll(".*/", ""));  
        int mask = 0xFFFFFFFF << (32 - type);  
        String cidrIp = cidr.replaceAll("/.*", "");  
        String[] cidrIps = cidrIp.split("\\.");  
        int cidrIpAddr = (Integer.parseInt(cidrIps[0]) << 24)  
                | (Integer.parseInt(cidrIps[1]) << 16)  
                | (Integer.parseInt(cidrIps[2]) << 8)  
                | Integer.parseInt(cidrIps[3]);  
  
        return (ipAddr & mask) == (cidrIpAddr & mask);  
    }  
}  
```

## 参考链接
- [Java IP地址转换和数字相互转换算法](http://www.what21.com/sys/view/java_java-algorithm_1456895959436.html)
- [Java IPv4地址和int类型数字的互相转换](https://www.jianshu.com/p/70185244e46c)
- [java判断某个ip是否在一个网段内 ip/mask IP+掩码](https://blog.csdn.net/yinxianluo/article/details/51208323)