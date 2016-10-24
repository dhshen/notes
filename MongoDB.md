#MongoDB#
## 安装 ##
在安装目录下加data目录，下辖db和logs两个目录

cmd下进入bin目录下执行：`mongod --dbpath=D:\mongodb\data\db`

localhost:27017看到内容则表示安装好了

新cmd进入bin目录，mongo默认连接到test

## 基本操作 ##
    db.help()                    help on db methods
    show dbs                     查询所有数据库
    show collections             show collections in current database
    use <db_name>                set current database
    exit                         quit the mongo shell

### 基本数据库操作 ###
	use 数据库名			//切换/创建数据库
	db.dropDatabase()		//删除当前使用的数据库
	db或db.getName()			//查看当前使用的数据库
	db.getMongo()			//查看当前db的链接机器地址
	db.version()			//当前db版本

### insert ###
	dp.person.insert({"name":"xiaoming","age":23})

### find (query) ###
	db.person.find(查找的条件)

### update ###
update方法的第一个参数为“查找的条件”，第二个参数为“更新的值”

	db.person.update(查找的条件,更新的值)
	db.person.update({"name":"xiaoming"},{"name":"abld","age":23})

	局部更新：
		$inc:增加字段
		$set:设置字段
	db.person.update({"name":"abld"},{$inc:{"age":2}})
	db.person.update({"name":"abld"},{$set:{"age":5}})

update之第三个参数：当第三个参数为true时如果一个参数查询条件没有找到时，就在数据库里新增一条

<批量更新>：在mongodb中若匹配多条，则默认的情况只更新第一条，若需批量更新，则将update的第4个参数设为true

### delete (remove) ###
remove中如果不带参数将删除所有数据，在mongodb中是一个不可撤回的操作.

	db.person.remove(删除的条件)
	db.person.remove({"name":"abld"})
	db.person.remove({});	//删除所有记录

### 其它语法 ###
	>   $gt
	>=  $gte
	<   $lt
	<=  $lte
	!=  $ne
	=   =或$eq  
	例：db.person.find({"age":{$gt:23}})	

	and	  
	or         $or
	in		   $in
	not in	   $nin
	例： 
		db.person.find({$or:[{"name":"xiaoming"},{"age":5}]})
	    db.person.find({"name":{$in:["xiaoming","abld"]}})
### 正则表达式 ###
	db.person.find({"name":/^test/})

### $where ###
	db.person.find({$where:function(){
		return this.name == "abld";
	}})

### 聚合 ###
	count:
		db.person.count()			//返回记录数
		db.person.count({"age":5})	//条件

	distinct：
		db.person.distinct("age");

### 索引 ###
	db.person.ensureIndex({"name":1})
	"1"表示按照name进行升序，"-1"表示降序

### 唯一索引 ###
重复键值不能插入

	db.person.ensureIndex({"name":1},{"unique":true})





















