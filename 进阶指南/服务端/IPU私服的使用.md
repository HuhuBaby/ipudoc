# IPU私服的使用

# maven项目使用私服配置方法
#一、 maven项目的pom.xml不必配置repositories节点。
原因：repositories节点配置就是告知maven项目下载jar包的地址来源，为了避免每个项目都是配置相同的仓库地址，顾将该配置搬迁到maven的settings.xml配置里面，即可全局生效。
#二、 settings.xml文件详情配置。
## 2.1 localRepository节点配置
必要配置，本地仓库地址。
## 2.2 servers节点配置
servers的配置有username和password节点，作用就是授权。
举例1，maven项目通过deploy命令发布jar包到私服，需要发布权限，配置发布的用户名和密码；
举例2，maven项目生成war包，通过tomcat插件自动复制到tomcat的webapps目录，需要tomcat的用户名和密码授权。
不用此功能的，可不用配置server。
## 2.3 mirrors节点配置
镜像节点不用配置，我们通过下面的profiles和activeProfiles也可以达到目的。
## 2.4 profiles和activeProfiles节点配置
profiles配置了多个profile，每个profile有个id，但只有配置在activeProfiles中的id，才能激活该仓库，可以使用。

注：http://114.215.100.48:9090/nexus/content/groups/public/ 是ipu的私服仓库组地址，里面包含了中央仓库地址、ipu仓库地址、3rd仓库地址。
以下是settings.xml文件中私服的配置样例。
```xml
  <profiles>
	<profile>
      <id>ipuProfile</id>
		<repositories>
			<repository>
				<id>ipuGroup</id>
				<name>ipu group Repository</name>
				<url>http://114.215.100.48:9090/nexus/content/groups/public/</url>
			</repository>
		</repositories>
    </profile>
  </profiles>
  
  <activeProfiles>
	<!-- 只有激活之后才生效 -->
    <activeProfile>ipuProfile</activeProfile>  
  </activeProfiles>
```

#三、 如果项目有特殊jar包，而ipuGroup仓库组不包含，怎么办？
## 3.1 假如jar包在第三方仓库地址，那就需要添加仓库地址。
解决方法一：
在pom.xml文件添加地址。
解决方法二：
在settings.xml文件添加仓库地址，增加profile节点，profile节点内部通过repository节点配置仓库地址，并激活该profile的id。
解决方法三：
在现有的激活的profile的id里面，添加repository节点，配置地址。
## 3.2 假如jar包未在任何一个仓库地址上面，比如oracle的jar包。
可以告知ipu管理组成员黄波、程文斌，代为上传到ipu私服的3rd仓库地址中。
## 3.3 假如jar包是自己团队的jar包。
解决方法一：
可以通过GAV坐标，添加scope节点，并显示指定jar包的位置即可

解决方法二：
通过命令手动安装到本地仓库，即可使用，参考资料：http://www.cnblogs.com/zaixiuxing/p/5970549.html
