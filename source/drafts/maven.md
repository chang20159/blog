# Maven
Java项目构建工具，使用java开发。   
约定优于配置
这是 maven 最核心的设计理念，很多语言框架都将其思想发扬光大。遵循约定虽然损失了一定的灵活性，不能随意安排目录结构，不能随意进行函数命名，但是却能减少配置和代码编写。更重要的是，遵循约定可以帮助开发人员遵守构建标准。   
Maven特点：
 
›拥有约定，知道你的代码在哪里，放到哪里去  
›拥有一个生命周期，例如执行 mvn install 就可以自动执行编译，测试，打包等构建过程  
›只需要定义一个pom.xml,然后把源码放到默认的目录，Maven帮你处理其他事情  
›拥有依赖管理，仓库管理  
- 有效pom
可以使用以下的命令让Maven打印出当前的有效pom  

    mvn help:effective-pom 

## maven编译打包，忽略测试用例  

- 跳过测试阶段   
    mvn package -DskipTests  
- 忽略失败的测试用例  
    mvn package -Dmaven.test.failure.ignore = true   
- 控制maven-compiler-plugin和maven-surefire-plugin两个插件的行为，即跳过编译，又跳过测试。  
    mvn package -Dmaven.test.skip=true  maven.test.skip 
- 指定测试类
    mvn test -Dtest=UserServiceTest  
    mvn test -Dtest=User*Test 以User开头Test结尾的测试类  
    mvn test -Dtest=UserServiceTest, ArticleServiceTest  用逗号分隔，指定多个测试用例
    

  