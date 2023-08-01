# ClazzSearcher
一款使用Yaml定义搜索规则来搜索Class的工具
### TODOList
- 还很多没做，还在开发ing
### Quick Start(快速开始)
下面是yml的规则搜索模版(如果不需要引入某条rule则不写)
```yaml
name: "targetName" # target的类名，支持正则，例如".*ServiceImpl$"
isInterface: false     # target是不是接口，true:是
extendsList:           # target继承的父类，有多个，因为接口可以有多个父接口
  - java.lang.Object
implementsList:        # target实现的接口，例如"java/io/Serializable"
  - interface1
  - interface2
annotations:           # target上存在的注解，例如"Lorg/springframework/web/bind/annotation/RestController;"
  - annotation1
  - annotation2
fields:                # target所有的field属性
  - {
    "name": "request", # field的name
    "access": 2,       # field的访问access
    "type": "type1"    # field的class类型，例如"org/springframework/http/server/reactive/ServerHttpRequest"
  }
  - {
    "name": "field2",
    "access": 1,
    "type": "type2"
  }
methods:               # target所拥有的method方法
  - {
    "name": "m1",      # method的name，例如"excludeMBeanIfNecessary"
    "desc": "desc1",   # method的描述符，例如"(Ljava/lang/Object;)V"
    "access": "public",# method的访问修饰符
    "isStatic": false, # method是否是静态方法
    "calls": [         # method中调用了哪些方法
      {
        "classRef": "org/springframework/jmx/export/MBeanExporter", # 被调用方法的类
        "name": "addExcludedBean",                                  # 被调用方法的名
        "desc": "(Ljava/lang/String;)V"                             # 被调用方法的方法描述
      }
    ]
  }
  - {
    "name": "m2",
    "desc": "(Ljava/io/Reader;)Ljava/lang/Object;",
    "access": "private",
    "call" : [
      { },{ },...
    ]
  }

```
### Use Case(使用案例)
例如下面一段规则
```yaml
implementsList:
  - org/springframework/beans/PropertyValues
  - java/io/Serializable
extendsList:
  - org/springframework/beans/MutablePropertyValues
```
运行`ClazzSearchApplication`加上参数`--f example.yml --boot d3forest-1.0-SNAPSHOT.jar`
```
[main] INFO org.vidar.discovery.ClazzDiscovery - 加载所有方法信息完毕
[main] INFO org.vidar.discovery.ClazzDiscovery - 加载所有类信息完毕
[main] INFO org.vidar.discovery.ClazzDiscovery - 加载所有父子类、超类、实现类关系
[main] INFO org.vidar.discovery.ClazzDiscovery - 加载方法调用信息完毕
[main] INFO org.vidar.discovery.ClazzDiscovery - 开始寻找目标类...
[main] INFO org.vidar.discovery.ClazzDiscovery - 你所寻找的class实现的接口有：
[main] INFO org.vidar.discovery.ClazzDiscovery - org/springframework/beans/PropertyValues
[main] INFO org.vidar.discovery.ClazzDiscovery - java/io/Serializable
[main] INFO org.vidar.discovery.ClazzDiscovery - 你所寻找的class继承的类有：
[main] INFO org.vidar.discovery.ClazzDiscovery - org/springframework/beans/MutablePropertyValues
找到一个类：ClassReference.Handle(name=org/springframework/web/bind/ServletRequestParameterPropertyValues)
找到一个类：ClassReference.Handle(name=org/springframework/web/filter/GenericFilterBean$FilterConfigPropertyValues)
找到一个类：ClassReference.Handle(name=org/springframework/web/servlet/HttpServletBean$ServletConfigPropertyValues)
```

如有下面这段规则
```yaml
methods:
  - {
    "name": "excludeMBeanIfNecessary",
    "desc": "(Ljava/lang/Object;Ljava/lang/String;Lorg/springframework/context/ApplicationContext;)V",
    "access": "private",
    "isStatic": false,
    "calls": [
      {
        "classRef": "org/springframework/jmx/export/MBeanExporter",
        "name": "addExcludedBean",
        "desc": "(Ljava/lang/String;)V"
      }
    ]
  }
```
运行`ClazzSearchApplication`加上参数`--f example.yml --boot d3forest-1.0-SNAPSHOT.jar`
```
[main] INFO org.vidar.discovery.ClazzDiscovery - 加载所有方法信息完毕
[main] INFO org.vidar.discovery.ClazzDiscovery - 加载所有类信息完毕
[main] INFO org.vidar.discovery.ClazzDiscovery - 加载所有父子类、超类、实现类关系
[main] INFO org.vidar.discovery.ClazzDiscovery - 加载方法调用信息完毕
[main] INFO org.vidar.discovery.ClazzDiscovery - 开始寻找目标类...
[main] INFO org.vidar.discovery.ClazzDiscovery - 你希望target中存在：ClazzRule.Method(clazz=null, name=excludeMBeanIfNecessary, desc=(Ljava/lang/Object;Ljava/lang/String;Lorg/springframework/context/ApplicationContext;)V, access=private, isStatic=false, calls=[ClazzRule.Call(classRef=org/springframework/jmx/export/MBeanExporter, name=addExcludedBean, desc=(Ljava/lang/String;)V)])方法
找到一个类：ClassReference.Handle(name=org/springframework/boot/autoconfigure/jdbc/JndiDataSourceAutoConfiguration)
```
### 参数说明
- --resume 是否使用已建立的数据缓存
- --f 指定配置yml
- --onlyJDK 是否只在jdk中筛选
- --boot 是否是springboot的jar
