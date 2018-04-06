# gradle-study
学习gradle的demo

# 前言
Gradle本身的领域对象主要有Project和Task。Project为Task提供了执行上下文，所有的Plugin要么向Project中添加用于配置的Property，要么向Project中添加不同的Task。

#配置
```
GRADLE_HOME=D:\ADT\gradlehome\gradle-4.5
PATH=%PATH%;%GRADLE_HOME%\bin
```

# hello world
## simple
使用的是groovy语法
```
// “<<”表示向helloWorld中加入执行代码
task helloworld << {
  println "hello world"
}
```
执行命令  `gradle helloworld`

```
> Task :helloworld
hello world

BUILD SUCCESSFUL in 1s
```

## 带属性的task
```
task helloworld << {
  // name是内置的默认属性, 你也可以定义一个局部变量
  // def name = "ddd"
  
  // 内置的description属性是可写的.
  description = "this is helloworld helloworld" 
  
  def var1 = "xiaofengqing"
  
  println  " name " + name + ", hello world :" + var1;
  println  " description: " + description
}
```

# project

----

# task
## 创建task
### 直接使用task + << groovy代码
```
task helloworld << {
  println "hello world"
}

task('helloworld') << {

}
```

### task + doLast / doFirst
```
task hello2{
  doFirst{
    println "task doFirst"
  }
  
  doLast{
    println "task doLast"
  }
}
```

### tasks.create 创建
```
tasks.create(name: 'hello4') << {
   println 'hello4'
}
```

## 自定义task - 文件内定义task
```
class HelloWorldTask extends DefaultTask {
    @Optional
    String message = 'I am davenkin'

    @TaskAction
    def hello(){
        println "hello world $message"
    }
}

task hello(type:HelloWorldTask)


task hello1(type:HelloWorldTask){
   message ="I am a programmer"
}
```

运行 `gradle.bat  -b .\customizetask.gradle hello`

## 自定义task - 工程内源代码定义task
源代码放在工程目录下的  `buildSrc\src\main\groovy\包名\类型.groovy` 即可.

## 自定义task - 独立工程内源代码定义task
见 `\customize-task\create-task-in-standalone-project\`

----

# 属性
先配置在运行, 配置代码可以放后面
| gradle -b .\properties-demo.gradle helloworld

## 闭包的方式来配置

```
task helloworld << {
  // 需要使用def关键字
  def var1 = "xiaofengqing"
  
  println  " var1: " + var1;
  //println  " var2: " + var2
  println  " description: " + description
}

// 配置代码可以放到后面
helloworld{
  // 使用def定义变量, 不报错, 但task内无法访问, 可以理解为临时变量
  def var2 = "var22222"
  
  // 内置的description属性是可写的.
  description = "this is helloworld helloworld" 
}
```

## 调用Task的configure()方法完成Property的设置
```
// 调用Task的configure()方法完成Property的设置
helloworld.configure {
   version = "this is hello9"
}
```

## 直接访问设置
```
helloworld.version = "xxxxxx"
```

还有这种
 
```
class GroovyBeanExample {
   private String name
}

def bean = new GroovyBeanExample()
bean.name = 'this is name'
println bean.name
```

## 

# 任务依赖
## 创建的时候声明 dependsOn
```
// taskA依赖于task helloworld
task taskA(dependsOn: helloworld) {
   //do something
}
```

## taskname.dependsOn 增加依赖
```
hello4.dependsOn helloworld
hello4.dependsOn hello2
```

输出

```
> Task :hello2
task doFirst
task doLast

> Task :helloworld
hello world

> Task :hello4
hello4
```



# 文件拷贝的Task
```
// 将src文件夹中的所有内容拷贝到dest文件夹中
task copy(type: Copy, group: "Custom", description: "Copies sources to the dest directory") {
    from "src"
    into "dest"
}
```



# 查看所有任务
`gradle tasks --all`

输出

```
> Task :tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'gradle-study'.
components - Displays the components produced by root project 'gradle-study'. [incubating]
dependencies - Displays all dependencies declared in root project 'gradle-study'.
dependencyInsight - Displays the insight into a specific dependency in root project 'gradle-study'.
dependentComponents - Displays the dependent components of components in root project 'gradle-study'. [incubating]
help - Displays a help message.
model - Displays the configuration model of root project 'gradle-study'. [incubating]
projects - Displays the sub-projects of root project 'gradle-study'.
properties - Displays the properties of root project 'gradle-study'.
tasks - Displays the tasks runnable from root project 'gradle-study'.

Other tasks
-----------
copyFile
helloworld
taskA
```

可以看出有很多默认的task

# 查看属性
`gradle.bat properties`

# doFirst / doLast

