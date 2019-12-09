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

### 判断A子网是否属于B子网
```java
public class IpTest {
    public boolean isSubnetInSubnet(String subnet1, String subnet2) {
        String[] split1 = subnet1.split("/");
        String ip1 = split1[0];
        String mask1 = split1[1];

        String[] split2 = subnet2.split("/");
        String ip2 = split2[0];
        String mask2 = split2[1];

        if(mask1.compareTo(mask2) < 1) {
            return false;
        }

        String maskBit2 = getMaskByMaskBit(mask2);
        Long ipNumber1 = getIpFromString(ip1);
        Long ipNumber2 = getIpFromString(ip2);
        Long ipNumber3 = getIpFromString(maskBit2);
        if((ipNumber1 & ipNumber3) == (ipNumber2 & ipNumber3)) {
            return true;
        }
        return false;
    }

    /**
     * 把xx.xx.xx.xx类型的转为long类型的
     *
     * @param ip
     * @return
     */
    public Long getIpFromString(String ip)
    {
        Long ipLong = 0L;
        String ipTemp = ip;
        ipLong = ipLong * 256
                + Long.parseLong(ipTemp.substring(0, ipTemp.indexOf('.')));
        ipTemp = ipTemp.substring(ipTemp.indexOf('.') + 1, ipTemp.length());
        ipLong = ipLong * 256
                + Long.parseLong(ipTemp.substring(0, ipTemp.indexOf('.')));
        ipTemp = ipTemp.substring(ipTemp.indexOf(".") + 1, ipTemp.length());
        ipLong = ipLong * 256
                + Long.parseLong(ipTemp.substring(0, ipTemp.indexOf('.')));
        ipTemp = ipTemp.substring(ipTemp.indexOf('.') + 1, ipTemp.length());
        ipLong = ipLong * 256 + Long.parseLong(ipTemp);
        return ipLong;
    }

    /**
     * 根据掩码位获取掩码
     *
     * @param maskBit
     *            掩码位数，如"28"、"30"
     * @return
     */
    public String getMaskByMaskBit(String maskBit)
    {
        return "".equals(maskBit) ? "error, maskBit is null !" : getMaskMap(maskBit);
    }

    public String getMaskMap(String maskBit) {
        if ("1".equals(maskBit)) {
            return "128.0.0.0";
        }
        if ("2".equals(maskBit)) {
            return "192.0.0.0";
        }
        if ("3".equals(maskBit)) {
            return "224.0.0.0";
        }
        if ("4".equals(maskBit)) {
            return "240.0.0.0";
        }
        if ("5".equals(maskBit)) {
            return "248.0.0.0";
        }
        if ("6".equals(maskBit)) {
            return "252.0.0.0";
        }
        if ("7".equals(maskBit)) {
            return "254.0.0.0";
        }
        if ("8".equals(maskBit)) {
            return "255.0.0.0";
        }
        if ("9".equals(maskBit)) {
            return "255.128.0.0";
        }
        if ("10".equals(maskBit)) {
            return "255.192.0.0";
        }
        if ("11".equals(maskBit)) {
            return "255.224.0.0";
        }
        if ("12".equals(maskBit)) {
            return "255.240.0.0";
        }
        if ("13".equals(maskBit)) {
            return "255.248.0.0";
        }
        if ("14".equals(maskBit)) {
            return "255.252.0.0";
        }
        if ("15".equals(maskBit)) {
            return "255.254.0.0";
        }
        if ("16".equals(maskBit)) {
            return "255.255.0.0";
        }
        if ("17".equals(maskBit)) {
            return "255.255.128.0";
        }
        if ("18".equals(maskBit)) {
            return "255.255.192.0";
        }
        if ("19".equals(maskBit)) {
            return "255.255.224.0";
        }
        if ("20".equals(maskBit)) {
            return "255.255.240.0";
        }
        if ("21".equals(maskBit)) {
            return "255.255.248.0";
        }
        if ("22".equals(maskBit)) {
            return "255.255.252.0";
        }
        if ("23".equals(maskBit)) {
            return "255.255.254.0";
        }
        if ("24".equals(maskBit)) {
            return "255.255.255.0";
        }
        if ("25".equals(maskBit)) {
            return "255.255.255.128";
        }
        if ("26".equals(maskBit)) {
            return "255.255.255.192";
        }
        if ("27".equals(maskBit)) {
            return "255.255.255.224";
        }
        if ("28".equals(maskBit)) {
            return "255.255.255.240";
        }
        if ("29".equals(maskBit)) {
            return "255.255.255.248";
        }
        if ("30".equals(maskBit)) {
            return "255.255.255.252";
        }
        if ("31".equals(maskBit)) {
            return "255.255.255.254";
        }
        if ("32".equals(maskBit)) {
            return "255.255.255.255";
        }
        return "-1";
    }
}
```

## 参考链接
- [Java IP地址转换和数字相互转换算法](http://www.what21.com/sys/view/java_java-algorithm_1456895959436.html)
- [Java IPv4地址和int类型数字的互相转换](https://www.jianshu.com/p/70185244e46c)
- [java判断某个ip是否在一个网段内 ip/mask IP+掩码](https://blog.csdn.net/yinxianluo/article/details/51208323)
- [判断两个IP是否在同一网段](https://blog.csdn.net/dqchouyang/article/details/78276956)
- [java实现 IP/掩码位 转换 ip段范围](https://blog.csdn.net/whatday/article/details/79168699)
- [GeoLite2 Free Downloadable Databases](https://dev.maxmind.com/geoip/geoip2/geolite2/)
- [GeoIP2 Java API](https://maxmind.github.io/GeoIP2-java/)
