---
title: Java计算文件MD5
date: 2023-06-07 15:58:40
tags:
categories: 
- Java基础
---
# 计算文件的MD5

```java
public String md5(File file){
        try {
            MessageDigest md5 = MessageDigest.getInstance("MD5");
            FileInputStream fileInputStream = new FileInputStream(file);
            ByteArrayOutputStream stream = new ByteArrayOutputStream();
            byte [] bytes = new byte[1024];
            int read = 0;
            while ((read=(fileInputStream.read(bytes)))!=-1){
                stream.write(bytes,0,read);
            }
            fileInputStream.close();
            stream.close();
            byte[] byteArray = stream.toByteArray();
            md5.update(byteArray);
            byte[] digest = md5.digest();
            BigInteger bigInteger = new BigInteger(1,digest);
            //生成MD5的进制数
            return bigInteger.toString(16);
        } catch (NoSuchAlgorithmException | IOException e) {
            throw new ApplicationException(CodeType.SERVICE_ERROR, "MD5转换失败");
        }
    }
```

