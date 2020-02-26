
--------------------------------

> 2020-02-26
> Spring Bean 手动获取失败

+ 问题描述：
	+ 大前提：MyBatis 配置第三方 cache 时，使用注解标记 cache 的实现类。
	+ 子前提一：第三方 cache 实现时必须创建一个带 String 参数的构造函数，来接受 MyBatis 传入的缓存 ID ，cache 的实现类不能为 bean 。
	+ 子前提二：cache 实现类需要使用redis配置生成 RedisTemplate，只能通过bean获取，bean 工具类SpringUtil继承 ApplicationContextAware 且使用@Component 由 Spring 自动生成。
	+ 导致问题：cache 的实现类手动获取 bean 时 applicationContext 为空。

+ 环境版本：JAVA=10，SpringBoot=Hoxton.SR1
+ 解决方案：
	+ 原因分析：对 Spring 的 bean 生成源码 debug 发现，Mapper 生成时 SpringUtil 并未注入。
	+ 解决方案：两种。
		1. 将 SpringUtil 移动到 SpringApplication 同包，使其最先注入。
		2. 在@Mapper上添加 @DependsOn 注解，强制注入顺序。

--------------------------------

> 2019-04-22

> JAVA

+ 问题描述：**clz.getConstructor(Boolean.class)=>NoSuchMethodException**

+ 环境版本：JAVA=8

+ 解决方案：根据构造函数参数改为getConstructor(boolean.class),Boolean.class与boolean.class不能通用。

-----------------------------

> 2019-04-15

> Android .dex合并 ClassNotFoundException

+ 问题描述：**自身SDK的dex文件与三方SDK的dex文件进行合并之后接入应用，反射调用三方SDK中的类引起CNFE**

+ 环境版本：Android=8.0 JAVA_1=1.8 JAVA_2=1.6

+ 解决方案：
	+ 原因分析：经过代码排查，不存在使用了不相匹配的非法ClassLoader的情况。然后考虑dex文件问题，在**类初始化**的时候，ART并没有成功加载指定的类，遂怀疑是.dex文件编码存在问题。经确认，是两个dex文件生成时所依赖的**Java环境版本不一致**所导致。
	+ 解决方案：使用相同的Java环境以及Android Tool环境。

-----------------------------

> 2019-02-15

> MAVEN 仓库

+ 问题描述：**Maven本地仓库有相应的jar包，并且%MAVEN_HOME%/conf/settings.xml内配置了localRepository的相应地址，执行mvn install命令时仍然去中央仓库进行下载**

+ 环境版本：Maven=3.5.4 JAVA=1.8 OS=Windows7

+ 解决方案：
	+ 原因分析：本地仓库内的jar包是从三方仓库下载，猜测Maven的某种机制，在对本地jar包进行检测时，如果是从三方仓库下载的，存在一种情况，会再去中央仓库获取一遍，**同样的配置文件以及同样的仓库文件夹，在另一台电脑上未出现该问题。**。
	+ 解决方案：修改本地仓库jar的远程仓库为中央仓库。将出问题的本地jar包文件夹下的_remote.repositories内的naxus=修改为central=。

---------------------------

> 2019-01-31

> JAVA 动态加载class文件 JVM原理

+ 问题描述：**使用Class.forName(clzName)对从*外部文件*导入的class进行加载时，报ClassNotFountException异常**

+ 环境版本：JAVA=1.8 JVM=HotSpot_build_25.191-b12

+ 解决方案：
	+ JVM分析：
		+ **不同的ClassLoader导入的同一个class二进制文件被JVM看做是不同的类**
		+ Class.forName(clzName) = Class.forName(clzName,加载静态区块=true,调用forName方法的实例类的类加载器)。可见：如果不传入ClassLoader参数，将会使用调用者的类加载器来生成Class
	+ 原因分析：加载二进制文件时使用自定义的类加载器，生成对象实例时未传入相应的类加载器
	
---------------------------

> 2019-01-28

> tomcat idea

+ 问题描述：**Tomcat启动显示:unable to ping server at localhost 1099**

+ 环境版本：idea=2018.2，tomcat=8.0.0，OS=x86-win7

+ 解决方案：
	* tomcat需要使用对应的jdk版本
	* R<u>u</u>n\>Run/Debug configuration dialog\>Edit Configurations>设置JRE版本。tomcat8使用JDK7以上
	
---------------------------

> 2019-01-28

> SQL

+ 问题描述：**取出其他表内的某几列（去除重复行）数据插入指定表**

+ 环境版本：Oracle12g

+ 解决方案：
	* insert into select语句插入；group by去重
	* 具体语法：**insert into** TARGET_T **select** COLUMN_1 as TT_COLUMN_1,COLUMN_2 as TT_COLUMN_2 from ( select COLUMN_1,COLUMN_2 from ANOTHER_T **group by** COLUMN_1,COLUMN_2)

--------------------------------

> 2019-01-23

> springMVC 请求参数

+ 问题描述：**后台在请求中自动填充数组字段时，VO使用List<String\>类型进行参数接受时报错**

+ 环境版本：SpringMVC=4.0.5

+ 解决方案：
	
	* SpringMVC参数VO内绑定仅支持基本类型+String
	* 前端拼接成字符串，后端再解析。

--------------------------------

> 2019-01-21

> json-lib bean spring

+ 问题描述：**后台将数组的json字符串转换成beans时，返回java数组元素内的字段值全为null**

+ 环境版本：json-lib=1.9.1，java=1.8

+ 解决方案：

	* 阅读JSONObject源码可知，json-lib不支持对setter为builder格式的bean进行解析
	* 使用json-lib时不使用builder格式构建setters

--------------------------------

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
		
------------------

> 2019-01-10

> memcached tomcat

+ 问题描述：本地使用tomcat和memcached搭建开发环境时，每次登录之后都又跳转到了登录界面。

+ 环境版本：tomcat=8.0.0，memcached=1.4.5

+ 解决方案：
	
	+ tomcat未设置path路径，导致memcached在处理请求时无法正确获取相应的用户session，导致每次重定向至登录界面
	+ 修改tomcat的项目path即可解决
	
--------------------------------
