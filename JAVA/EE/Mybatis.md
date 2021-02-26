# Mybatis

### 全局配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
        <package name=""/>
    </typeAliases>
    <environments default="default">
        <environment id="default">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/database"/>
                <property name="username" value=""/>
                <property name="password" value=""/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource=""/>
    </mappers>
</configuration>
```

#### transactionManaager

设置mybatis中两种事物管理器类型

1. JDBC：使用JDBC的提交和回滚设置，依赖从数据源得到的链接来管理事务;
2. MANAGED：让容器来管理事务的整个生命周期，默认情况下它会关闭连接。然后一些容器并不希望这些，因此如果你需要从链接中停止，将需要将closeConnection的属性设置为false。

#### dataSource

使用JDBC数据源的时候配置JDBC 连接对象的资源

1. UNPOOLED：每次请求的时候简单的打开和关闭数据源;
2. POOLED：使用数据源链接池;
3. JNDI：使用容器，容器可以集中或者外部配置数据源。

