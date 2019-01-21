> 2019-01-17

> idea 日志输出 tomcat
    	
+ 问题描述：**idea前端返回报500错误，IntelliJ Idea控制台没有报错信息。**

+ 环境版本：idea=2018.2，tomcat=8.0.0，OS=x86-win7

+ 解决方案：
	+ 直接去用户目录下查看idea生成的日志：${USER_HOME}/.IntelliJIdea${version}/system/tomcat/${project}/logs/localhost-${date}.log
	
	+ 修改tomcat日志配置
	
		1. 打开${CATALINA_HOME}/conf/logging.properties
		2. 修改配置项org.apache.catalina.core.ContainerBase.[Catalina].[localhost].handlers = *.*.**.*
		3. 在原配置项后新加一个类，两者之间用英文逗号隔开：java.util.logging.ConsoleHandler


> 2019-01-01

> json-lib bean spring

+ 问题描述：**后台将数组的json字符串转换成beans时，返回java数组元素内的字段值全为null**

+ 环境版本：json-lib=1.9.11

+ 问题定位：阅读JSONObject源码可知，json-lib不支持对setter方法为builder格式的bean进行解析

+ 解决方案：使用json-lib时不使用builder格式构建setters
