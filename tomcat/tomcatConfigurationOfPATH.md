**问题还原**：今天换用了JDK1.8版本以后 tomcat服务器竟然跑不动了（启动之后会接着卡掉，具体情况如下），发现其中的JRE_HOME路径有出入，遂开始整理配置文件：


```
# service tomcat start
	  
	Starting tomcat
	Using CATALINA_BASE:   /usr/local/tomcat
	Using CATALINA_HOME:   /usr/local/tomcat
	Using CATALINA_TMPDIR: /usr/local/tomcat/temp
	Using JRE_HOME:        /usr/java/jdk1.7.0_80
	Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
	Tomcat started.
	Tomcat is running with pid: 28720

# service tomcat status

	Tomcat is not running
```



```
# vi /etc/profile

	//追加如下行所示，其中default是我ln指向的JDK1.8
	export JRE_HOME=/usr/java/default/jre
```
使profile及时生效：
```
# source /etc/profile
```
查看jre版本：
```
# javac -version

	javac 1.8.0_121
```
说明以上工作生效了。（但是似乎还是不可以）

然后打开**tomcat的配置文件**，追加这样的说明：

```
# vi /usr/local/tomcat/bin/catalina.sh

	export JAVA_HOME=/usr/java/default
	export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
	export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin
	export CATALINA_HOME=/usr/local/tomcat
```

执行
```
# /usr/local/tomcat/bin/catalina.sh start

	Using CATALINA_BASE:   /usr/local/tomcat
	Using CATALINA_HOME:   /usr/local/tomcat
	Using CATALINA_TMPDIR: /usr/local/tomcat/temp
	Using JRE_HOME:        /usr/java/default/jre
	Using CLASSPATH:       /usr/local/tomcat/bin/bootstrap.jar:/usr/local/tomcat/bin/tomcat-juli.jar
	Tomcat started.
```



查看状态
```
# service tomcat status

	Tomcat is running with pid: 29631
```

**配置完成**，其实catalina.sh会去调用setclasspath.sh来加载PATH,所以在setclasspath.sh中追加也可以。

最后，可以**设置自动启动**：
```

# vi /etc/rc.d/rc.local
	
	//追加
	/usr/local/tomcat/bin/startup.sh
```
完成。


