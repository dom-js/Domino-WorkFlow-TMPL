#XMLCollection#

XML Collection 是一个用于对复杂数据结构进行处理的集合，接受一个xml字符串作为输入，转换后按照输入指定的Action对文档集合进行操作，总体类似SOAP消息的集合。

## XML文档API ##

		<collection>
			<doc name='abc' unid='unid'>
			<head> 
				<action  name='savetodoc'>
					<params name='abc' value=''/> 
				</action> 
			</head>
			<body>
				<fields>
						<field name='' type=''>
						</field>
				</fields> 
				<entries  fields="" idproperty="">
					<entry>
						<field name='' type=''>
							<value>
							</value>
						</field>
					</entry>
				</entries>
			</body>
			</doc> 
		</collection> 
### 标签说明 ###

- **collection** XML顶层标签
	- **doc** 待办一个文档
		- **属性**
			- **name**[Option] 文档名 
			- **unid**[Option] 文档UNID
		
		- **head** 头信息
			- **Action** 操作
				- **属性**
					- **type**[Option] 操作类型。目前只支持默认的 fun一个方法。通过`name`属性指定方法名
					- **name** 操作名称 .关于支持的方法，参考`XmlColDoc` 对象方法。
				- **params** 参数
					- **属性** 
						- **name** 参数名称，关于参数，参考`XmlColDoc` 对象方法。
						- **value** 参数值
		- **body** 正文信息
			- **fieds**[Options] 字段列表
				- **field** 字段信息
					- **属性** 
						- **name**字段名称
						- **type**[Option] 字段类型，支持text、names、datatimes三种格式
						- **issummary**[Option]是否具有issummary标记，默认true，具有issummary时可以在视图内展示。
					- **value**字段值，多值有多高value标签 
			- **entries**[Options] 表记录，定义一个集合，改集合的每条记录都具有相同的字段，字段会按照集合内所在记录的索引顺序一字段名称加后缀"_n"格式保存到文档内
				- **属性**
					- **fields** entry内字段列表（可不与entry内一直，主要处理删除记录时对已经存在的记录进行删除操作）
					- **idproperty** 主键字段，默认为entriesfields的第一个
				- **entry** 具体记录
					- **field** 字段信息，参考`body>fields>field`  
 

## XMLCollection对象 ##

### 方法 ###

### 属性 ###


## XmlColDoc对象 ##

### 方法 ###

### 属性 ###