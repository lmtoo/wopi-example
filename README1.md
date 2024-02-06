# 简介

## **Office文件处理：转换为PDF**

> 功能特性：
> 
> 在文件存储之后，会对office文件生成一个PDF的预览文件，
> 
> 暴露office预览地址到preview超链接
> 
> 对转换结果进行缓存，防止多次转换（缓存介质：本地目录、mongodb）


- office转PDF方案：

1. 利用document4j转换（windows）  
2. 利用jodconverter转换（linux）
3. 利用docx4j转换（linux、windows）效果不佳


# 总体配置  


### 配置  

> 是否启用展示模块    
```properties
document.renders.enabled=true
```

> 是否缓存转换结果  
```properties
document.renders.cache.enabled=true
```
> 转换结果缓存本地目录  
```properties
document.renders.cache.dir=file:/usr/local/conversion
```

> 转换结果缓存mongo桶名称  
```properties
document.renders.cache.bucket=cache
```

> 预览超时时间  
```properties
document.renders.timeout=300s
```

> 是否转换excel格式  
```properties
document.renders.excel=true
```


# 利用[document4j](https://github.com/documents4j/documents4j)


### 配置  

> 是否启用远程转换
```properties
document.renders.remote.enabled=true
```

>文档转换服务
```properties
document.renders.remote.uri=http://192.168.20.143:9998
```

### 部署office转换服务  

> 确保windows机器安装了word，如果要启用excel转换，也要安装excel

>下载转换服务部署包

[下载documents4j-server-standalone-1.1.5-shaded.jar](https://repo1.maven.org/maven2/com/documents4j/documents4j-server-standalone/1.1.5/documents4j-server-standalone-1.1.5-shaded.jar)

> 查询启动参数

```cmd
java -Xmx1G -Xms1G -jar documents4j-server-standalone-1.1.5-shaded.jar http://192.168.20.143:9998/ -?
```

> 启动转换服务（要跟配置文件中的地址相同）

```cmd
java -Xmx1G -Xms1G -jar documents4j-server-standalone-1.1.5-shaded.jar http://192.168.20.143:9998/ -F "F:\\conversions\\" -R 300000 -V warn -S 5 -B 5
```


# 利用[libreoffice/online](https://github.com/sbraconnier/jodconverter/wiki/LibreOffice-Online)

>https://www.collaboraoffice.com/collabora-online-editor-api-reference/


### 配置  

>启用文档转换服务
```properties
jodconverter.remote.enabled=true
```

>文档转换服务远程地址
```properties
jodconverter.remote.url=http://192.168.2.215:9980
```

### 部署office转换服务

#### 部署指南：

> https://www.collaboraoffice.com/code/docker/  
> https://github.com/CollaboraOnline/Docker-CODE/issues/49

>启动服务

- 内网  

```cmd
docker run -t -d -p 9980:9980 -e "domain=" -e "username=root" -e "password=root" -e "extra_params=--o:ssl.enable=false" collabora/code
```

- 外网  

```cmd
docker run --privileged -t -d -p 9980:9980 -e "domain=" -e "username=code" -e "password=root" -e "extra_params=--o:ssl.enable=false --o:ssl.termination=true --o:net.post_allow.host[0]=.+ --o:storage.wopi.host[0]=.+" collabora/code
```

> 测试服务是否正常

>curl http://192.168.2.215:9980

# 利用[docx4j](https://www.docx4java.org/trac/docx4j)

### 配置

>启用文档转换服务
```properties
document.renders.docx4j.enabled=true
```























