Maven基本概念与核心配置

8/7/2018 10:51:15 PM 

## maven 安装与核心概念

### maven安装

- 1.官网下载 Maven （http://maven.apache.org/download.cgi）
- 2.解压指定目录
- 3.配置环境变量
- 4.检查安装是否成功 （mvn -version）

### Maven基本配置文件pom.xml

	<project xmlns="http://maven.apache.org/POM/4.0.0"
	  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
	                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
	  <modelVersion>4.0.0</modelVersion>
	 
	  <groupId>org.codehaus.mojo</groupId>
	  <artifactId>my-project</artifactId>
	  <version>1.0.SNAPSHOT</version>

	  <dependencies>
		<dependency>
		  <groupId>junit</groupId>
		  <artifactId>junit</artifactId>
		  <version>4.0</version>
		  <scope>test</scope>
		</dependency>
	  </dependencies>

	</project>

### Maven基本命令

	# 生成maven项目的骨架
	mvn archetype:generate

	# 编译
	mvn compile;

	# 打包
	mvn package
	
	# 在本地Repository中安装jar，该命令包含mvn compile，mvn package，然后上传到本地仓库
	mvn install

	# 清除产生的项目
	mvn clean

	# 执行测试代码
	mvn test

> 在没有引入JUnit等测试框架时，maven当中的测试类做了约定，约定在/src/test/java/中的测试类代码，必须是Test开头的类名与test开头的方法才会执行（执行mvn test时运行）。如：

	public class TestHello{
        public void testSayHelloTest(){
                System.out.println("run test .....");
        }
	}

### 默认远程仓库

默认远程仓库 maven central 其配置在  
maven-model-builder-3.2.1.jar\org\apache\maven\model\pom-4.0.0.xml 位置

### 本地仓库位置
本地仓库位置默认在 ~/.m2/respository 下
要修改 ${M2_HOME}/conf/settings.xml  来指定仓库目录

	<!-- 指定本地仓库目录-->
	<localRepository>G:\.m2\repository</localRepository>

**maven 核心功能总结：**  

- 1.maven 核心作用是编译、测试、打包。  
- 2.根目录下的pom.xml 文件设置分组ID与artifactId。  
- 3.maven 基于约定的方式从项目中获取源码与资源文件进行编译打包。  
- 4.对于项目所依懒的组件与会本地仓库引用，如果本地仓库不存在则会从中央仓库下载。

## maven核心配置

### 项目依懒
>项目依赖是指maven 通过依赖传播、依赖优先原则、可选依赖、排除依赖、依赖范围等特性来管理项目ClassPath。

**依赖传播特性**

>我们的项目通常需要依赖第三方组件，而第三方组件又会依赖其它组件遇到这种情况Maven会将依赖网络中的所有节点都会加入ClassPath当中，这就是Maven的依赖传播特性。

**依赖优先原则**  

>基于依赖传播特性，导致整个依赖网络会很复杂，难免会出现相同组件不同版本的情况。Maven此时会基于依赖优先原则选择其中一个版本。

- 第一原则：最短路径优先。
- 第二原则：相同路径下配置在前的优先。

**可选依赖**

>可选依赖表示这个依赖不是必须的。通过在 `<dependency>` 添  `<optional>true</optional>` 表示，默认是不可选的。可选依赖不会被传递。

**排除依赖**

> 即排除指定的间接依赖。通过配置 `<exclusions>` 配置排除指定组件。

	
	<dependency>
	    <groupId>corg.springframework</groupId>
	    <artifactId>spring-web</artifactId>
	    <version>4.0.2</version>
		<!-- 排除指定项目 -->
		<exclusions>
		    <exclusion>
		       <groupId>ommons-logging</groupId>
		       <artifactId>commons-logging</artifactId>
		    </exclusion>
		</exclusions>
	</dependency>

**依赖范围**  

> 像junit 这个组件 我们只有在运行测试用例的时候去要用到，这就没有必要在打包的时候把junit.jar 包过构建进去，可以通过Mave 的依赖范围配置`<scope>`来达到这种目的。  
> maven 总共支持以下四种依赖范围：


- **compile(默认):** 编译范围，编译和打包都会依赖。
- **provided：**提供范围，编译时依赖，但不会打包进去。如：servlet-api.jar
- **runtime：**运行时范围，打包时依赖，编译不会。如：mysql-connector-java.jar
- **test：**测试范围，编译运行测试用例依赖，不会打包进去。如：junit.jar
- **system：**表示由系统中CLASSPATH指定。编译时依赖，不会打包进去。配合<systemPath> 一起使用。示例：java.home下的tool.jar
	
system 除了可以用于引入系统classpath 中包，也可以用于引入系统非maven  收录的第三方Jar，做法是将第三方Jar放置在项目的lib目录下，然后配置相对路径，但因system不会打包进去所以需要配合maven-dependency-plugin插件配合使用。当然推荐大家还是通过将第三方Jar手动install到仓库。

以下这种方式在maven打成war包时不会将引入的jar一起打进去。

	<!-- system 的通常使用方式-->
	<dependency>
		<groupId>com.sun</groupId>
		<artifactId>tools</artifactId>
        <version>${java.version}</version>
        <scope>system</scope>
		<optional>true</optional>
		<systemPath>${java.home}/../lib/tools.jar</systemPath>
	</dependency>

以下这种方式在maven打成war包时会将引入的jar一起打进去。

	<!-- system 另外使用方式 ,将工程内的jar直接引入 -->
	<dependency>
	    <groupId>jsr</groupId>
	    <artifactId>jsr</artifactId>
	    <version>3.5</version>
	    <scope>system</scope>
	    <optional>true</optional>
	    <systemPath>${basedir}/lib/jsr305.jar</systemPath>
	</dependency>

	<!-- 通过插件 将system 的jar 打包进去。 -->
	<plugin>
	    <groupId>org.apache.maven.plugins</groupId>
	    <artifactId>maven-dependency-plugin</artifactId>
	    <version>2.10</version>
	    <executions>
	        <execution>
	            <id>copy-dependencies</id>
	            <phase>compile</phase>
	            <goals>
	                <goal>copy-dependencies</goal>
	            </goals>
	            <configuration>
					<outputDirectory>${project.build.directory}/${project.build.finalName}/WEB-INF/lib</outputDirectory>
					<!-- 指定需要打包进去的scope -->
	                <includeScope>system</includeScope>  
	                <excludeGroupIds>com.sun</excludeGroupIds>
	            </configuration>
	        </execution>
	    </executions>
	</plugin>


当然还可以手动将jar包加入本地仓库中，这样就可以直接选择可打包的scope。

	mvn install:install-file -Dfile=abc_client_v1.20.jar -DgroupId=tuling  -DartifactId=tuling-client -Dversion=1.20 -Dpackaging=jar

### 项目聚合与继承

**聚合**

>是指将多个模块整合在一起，统一构建，避免一个一个的构建。聚合需要个父工程，然后使用 `<modules>` 进行配置其中对应的是子工程的相对路径。

	<modules>
	    <module>tuling-client</module>
	    <module>tuling-server</module>
	</modules>

**继承**  

> 继承是指子工程直接继承父工程当中的属性、依赖、插件等配置，避免重复配置。

- 1.属性继承：
- 2.依赖继承：
- 3.插件继承：

上面的三个配置子工程都可以进行重写，重写之后以子工程的为准。

**依赖管理**

>通过继承的特性，子工程是可以间接依赖父工程的依赖，但多个子工程依赖有时并不一至，这时就可以在父工程中加入 `<dependencyManagement>` 声明该功程需要的JAR包，然后在子工程中引入。

父工程的pom.xml：

	<！-- 父工程中声明 junit 4.12 -->
	<dependencyManagement>
	    <dependencies>
	        <dependency>
	            <groupId>junit</groupId>
	            <artifactId>junit</artifactId>
	            <version>4.12</version>
	        </dependency>
	    </dependencies>
	</dependencyManagement>

子工程的pom.xml：

	<!-- 子工程中引入 -->
	<dependency>
	    <groupId>junit</groupId>
	    <artifactId>junit</artifactId>
	</dependency>

**项目属性**

> 通过` <properties>` 配置 属性参数，可以简化配置。

	<!-- 配置proName属性 -->
	<properties>
	    <proName>ddd</proName>
	</properties>

	<!-- 引用方式 -->
	${proName}

maven 默认的属性:

- **${basedir}** 项目根目录  
- **${version}**表示项目版本;  
- **${project.basedir}**同${basedir};  
- **${project.version}**表示项目版本,与${version}相同;  
- **${project.build.directory}** 构建目录，缺省为target  
- **${project.build.sourceEncoding}**表示主源码的编码格式;  
- **${project.build.sourceDirectory}**表示主源码路径;  
- **${project.build.finalName}**表示输出文件名称;  
- **${project.build.outputDirectory}** 构建过程输出目录，缺省为target/classes 

### 项目构建配置

**构建资源配置**

	<!-- 基本配置示例-->
	<defaultGoal>package</defaultGoal>
	<directory>${basedir}/target2</directory>
	<finalName>${artifactId}-${version}</finalName>

说明：  
defaultGoal，执行构建时默认的goal或phase，如jar:jar或者package等  
directory，构建的结果所在的路径，默认为${basedir}/target目录  
finalName，构建的最终结果的名字，该名字可能在其他plugin中被改变  

配置示例：

	<resources>
	   <resource>
	      <directory>src/main/java</directory>
	      <includes>
	         <include>**/*.MF</include>
	         <include>**/*.XML</include>
	      </includes>
	      <filtering>true</filtering>
	   </resource>
	   <resource>
	      <directory>src/main/resources</directory>
	      <includes>
	         <include>**/*</include>
	         <include>*</include>
	      </includes>
	      <filtering>true</filtering>
	   </resource>
	</resources>

说明：resources，build过程中涉及的资源文件

- targetPath，资源文件的目标路径
- directory，资源文件的路径，默认位于${basedir}/src/main/resources/目录下
- includes，一组文件名的匹配模式，被匹配的资源文件将被构建过程处理
- excludes，一组文件名的匹配模式，被匹配的资源文件将被构建过程忽略。同时被includes和excludes匹配的资源文件，将被忽略。
- filtering： 默认false ，true 表示通过参数对资源文件中的${key}在编译时进行动态变更。替换源可紧-Dkey和pom中的`<properties>` 值或 `<filters>` 中指定的properties 文件。

