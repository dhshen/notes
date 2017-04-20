# Sequelize入门实践

> Sequelize is a promise-based ORM for Node.js and io.js. It supports the dialects PostgreSQL, MySQL, MariaDB, SQLite and MSSQL and features solid transaction support, relations, read replication and more.

[官方github](https://github.com/sequelize)

官方github有好几个项目值得关注，分别是：

* [sequelize](https://github.com/sequelize/sequelize)
* [cli](https://github.com/sequelize/cli)
* [express-example](https://github.com/sequelize/express-example)
* [sequelize-auto](https://github.com/sequelize/sequelize-auto)



## 安装Sequelize

```javascript
npm install --save sequelize mysql		//因为使用的是mysql数据库，因此也要安装mysql库
```



### 使用sequelize命令行初始化项目

安装命令行工具：

```
npm install -g sequelize-cli
```

在node项目根目录下执行如下命令：

```
sequelize init
```

此操作会在项目根目录下生成几个文件件

* config：此目录下有一个config.json文件，内容是数据库的配置
* migrations
* models：此目录是用来存放数据库表映射的，初始化之后会有一个index.js文件，在其它模块引用的就是这个模块，index.js中的逻辑是把本目录下的表映射模块全部加载进来。
* seeders：



### 使用`sequelize-auto`自动生成表的映射

```
sequelize-auto -o "./models" -d shunv -h 120.24.14.220 -u root -p 3306 -x hexuntest123 -e mysql
```





### 在数据库表映射中添加配置

```
module.exports = function(sequelize, DataTypes) {
  return sequelize.define('msuser', {
    id: {
      type: DataTypes.INTEGER(11).UNSIGNED,
      allowNull: false,
      primaryKey: true,
      autoIncrement: true
    },
    username: {
      type: DataTypes.STRING(32),
      allowNull: true
    },
    password: {
      type: DataTypes.STRING(256),
      allowNull: true
    },
    notes: {
      type: DataTypes.STRING(256),
      allowNull: true
    }
  }, {
    tableName: 'msuser',
    underscored: false,
    timestamps: false,
    freezeTableName: true

  });
};
```

上面的配置中：

```
underscored: false,
timestamps: false,
freezeTableName: true
```

这一段属于自己手动添加的，这几个配置的作用很重要，分别是：



#### 在查询中使用









