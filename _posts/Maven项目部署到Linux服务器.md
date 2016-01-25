title: Maven项目部署到Linux服务器
date: 2016-01-25 17:11:01
tags: linux
---
上一次用linux服务器还是半年前，那次是非maven的项目。记得大概流程是把项目所需要的jar包，还有项目生成的target下的class文件上传到服务器，然后用一个sh文件运行。

``` bash
APP_HOME=/root/autocrawler
for i in "$APP_HOME"/lib/*.jar
do
 CLASSPATH="$CLASSPATH":"$i"
done
export CLASSPATH=.:$CLASSPATH
nohup java -Xms50m -Xmx250m spider.test.WebCrawlerFinalTest &
```

现在换成了Maven的工程因为项目所需要的jar包被maven管理，所以一时还手忙脚乱不知道怎么做好。好不容易成功了，现在总结下免得下次再用的时候忘记了。

<!--more-->

这次用到的东西。

首先在pom文件中加入build标签
``` bash
<build>
		<sourceDirectory>src/main/java</sourceDirectory>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.1</version>
				<configuration>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-resources-plugin</artifactId>
				<version>2.6</version>
				<configuration>
					<encoding>UTF-8</encoding>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<version>2.9</version>
				<configuration>
					<skipTests>true</skipTests>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-jar-plugin</artifactId>
				<version>2.4</version>
				<configuration>
					<archive>
						<manifest>
							<addClasspath>true</addClasspath>
							<classpathPrefix>lib/</classpathPrefix>
							<mainClass>com.hou.guanggu.Infosource.checkWebsite.AnalyseLogScheduler</mainClass>
							这里写主函数的位置..
						</manifest>
					</archive>
				</configuration>
			</plugin>
		</plugins>
		<resources>
			<resource>
				<directory>src/main/resources</directory>
				<filtering>false</filtering>
			</resource>
		</resources>
	</build>
```

最好加个
``` bash
<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

还有远程服务器的位置
``` bash
	<repositories>
		<repository>
			<id>nexus</id>
			<name>Team Nexus Repository</name>
			<url>XXX</url>
			<releases>
				<enabled>true</enabled>
			</releases>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
	</repositories>
```

然后就是Maven的打包了。
用到mvn clean package，把生成的jar包拖到服务器上面去。然后用下面的命令输出所需的依赖，再把依赖传到服务器上面。

``` bash
mvn dependency:copy-dependencies -DoutputDirectory=lib/
```

写一个简单的sh文件。大功告成。

``` bash
nohup java -jar Infosource-0.0.1-SNAPSHOT.jar &
```

路漫漫其修远兮..

2016年1月25日17:21:44