# SuperSql
基于瞎注(xia_sql)插件使用burp最新API（montoya-api:2026.2）重写版本：SuperSql，添加[path]扫描模块致力于多sql注入点发现，插件通过对字符型参数插入'和''对比返回包状态人工检查是否存在注入点，通过对数值型参数插入','',-1,-0等payload对比返回包状态人工检查注入点，对于隐藏在URL中的注入点支持对path的扫描。
# 注意事项
1.插件使用jdk21版本编译

2.插件使用burp最新API编写，参考最新API文档https://github.com/PortSwigger/burp-extensions-montoya-api-examples/tree/main

3.由于gradle版本更新目前burp给出的build.gradle已经报错，build.gradle可参考如下代码

```
plugins {
    id 'java'
}

group 'net.portswigger.burp.extensions.example'
version '1.0.0'

repositories {
    mavenLocal()
    mavenCentral()
}

dependencies {
    compileOnly 'net.portswigger.burp.extensions:montoya-api:2026.2'
}

// 注意：Montoya API 2026.2 需要 Java 17 或更高版本
// 请安装 JDK 17 并设置 JAVA_HOME 环境变量
java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}

jar {
    from {
        // 修复：使用 forEach 替代 get()
        configurations.runtimeClasspath.collect {
            it.isDirectory() ? it : zipTree(it)
        }
    }

    // 可选：避免重复文件冲突
    duplicatesStrategy = DuplicatesStrategy.EXCLUDE
}
```
# 插件使用
- <font color="green">✔ 返回</font> 代表该条流量中最少有一条条流量引起异常，例如单引号和两个单引号返回长度不一致，表示该条流量中可能存在有注入点的参数。
- 出现具体数值长度差，代表单引号和两个单引号返回长度的差值，针对数值型参数代表-1数据包和-0数据包的差值，表明可能存在注入点
- <font color="red">==> 返回</font> 代表着 原始包的长度和两个单引号的长度相同且和一个单引号的长度不同，表明很可能是注入。
- <font color="blue">Error! 返回</font> 代表响应包中含有数据库报错信息。
- <font color="blue"></font> 支持json格式，V1.9以上版本已支持json多层嵌套。
- <font color="blue"></font>支持参数的值是纯数字则-1，-0。
- <font color="blue"></font>默认支持cookie测试
- <font color="blue"></font>监控Proxy流量。
- <font color="blue"></font>监控Repeater流量。
- <font color="blue"></font>同个数据包只扫描一次
- <font color="blue"></font>支持黑名单功能，每行一个域名或者一个路径
- <font color="blue"></font>静态文件过滤，自动跳过图片、CSS、JS 等静态资源
- <font color="blue"></font>支持URL中path的扫描，用于发现注入点隐藏在路径中
- 黑名单自动保存到burp目录吓命名为supersql_blacklist.txt
- 支持自定义报错信息，自动保存到burp目录下，命名为supersql_error_patterns.txt
- 支持自动从返回包以及请求包中提取url清奇路径以及参数
# 插件截图
<img width="1580" height="823" alt="image" src="https://github.com/user-attachments/assets/9e02959d-80e4-4d8c-be30-7a73f1645385" />

<img width="1582" height="830" alt="image" src="https://github.com/user-attachments/assets/2a2fb10b-941f-49fc-8bf2-54c2930c13c3" />

