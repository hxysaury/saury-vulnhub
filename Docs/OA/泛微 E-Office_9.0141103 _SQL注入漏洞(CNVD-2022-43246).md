# 泛微 E-Office_9.0141103 _SQL注入漏洞

| 说明     | 内容                                                         |
| -------- | ------------------------------------------------------------ |
| 漏洞编号 | CNVD-2022-43246                                              |
| 漏洞名称 | 泛微 E-Office SQL注入漏洞                                    |
| 漏洞评级 | 高危 SQL注入                                                 |
| 影响范围 | E-Office v9.0 141103                                         |
| 漏洞描述 | 上海泛微网络科技股份有限公司的产品E-Office存在SQL注入漏洞，攻击者可以通过该漏洞获取到数据库敏感信息 |
| 修复方案 | 升级                                                         |



### 1.1、漏洞描述

上海泛微网络科技股份有限公司的产品E-Office存在SQL注入漏洞，攻击者可以通过该漏洞获取到数据库敏感信息

### 1.2、漏洞等级

高危

### 1.3、影响版本

E-Office v9.0 141103

### 1.4、漏洞复现

#### 1、基础环境

默认安装位置是 `d:\eoffice` 在虚拟机内安装没有 D 盘，所以安装位置是 `c:\eoffice`

安装完成后，服务默认在 `8082` 端口 通过`主机名 或 ip 地址`都可以访问到

![image-20230915190458443](./imgs/image-20230915190458443.png)

#### 2、漏洞扫描

#### 3、漏洞验证

```
/general/index/UploadFile.php?m=uploadPicture&uploadType=eoffice_logo&userId=
```



### 1.5、深度利用

#### 1、反弹Shell

#### 2、写入文件

