# UTIL.lib #

## UTIL ##

**Summary**
静态对象。可以直接使用`util.`前缀调用方法或属性，如`util.db`

### 属性 ###
- **session**[NotesSession]- R
	
	当前会话对象

- **db**[NotesDatabase] - R/W

	当前数据库对象，可设置db对象，这会对依赖util.db 进行计算的方法产生影响，如希望一些计算在指定的数据库环境中执行。

- **doc**[NotesDocument] - R/W

	当前文档对象，可以设置doc对象，这会对依赖util.doc 进行计算的方法产生影响，如希望一些计算在指定的数据库环境中执行。
- **Profile**[NotesDocument] - R

	简要表`sysProfileConfig`文档对象，新流程引擎具有此简要表

- **ServerTime**[String] - R

	获取服务器时间 

- **User**[String] - R/W

	当前用户，可以设置。不能修改代理实际执行用户，这会对依赖util.doc 进行计算的方法产生影响，如希望一些计算在指定的数据库环境中执行。

- **DNS**[String]

	当获方法服务器的DNS

- **HOSTURL**[String]

	当前范围数据库的URL地址	

- **WebURL**[String]

    当前访问数据和的URL

### 方法 ###

- **ArrayToString(var As Variant)** [String]

	将数组`var` 转换为一个字符串，使用`,`分隔多值



- **Empty(var As Variant)**[Boolean] 


	判断 `var`是否为空值.`var` 为字符串，或者字符串数组等简单`Variant`类型


- **EvalFormula(f As String,d As Variant)** [Variant]

    执行公式`f`。

	- **f** ： 要执行的公司
	- **d** ： `null`或者`NotesDocument`表示公式所计算的文档，如果设置为null，则使用`util.doc`
- **EvalFormulaToStr(f As String,d As Variant)**[String]

	同`EvalFormula`，返回值公式类型为`String`，多值以`,`分隔

- **getDicEntry(keys As Variant)** [NotesViewEntry]

	依赖新模板数据字典配置，返回数据字典key的第一条记录

- **getDicEntrys(keys As Variant)** [NotesViewEntryCollection]

	依赖新模板数据字典配置，返回数据字典key的记录集合

- **getServerTime(f As String )**[String]

	根据服参数F返回服务器时间

		util.getServerTime("HH:mm:ss").
	**具体参数写法可参考 `Format`函数**
- **getFullName(users As Variant)**[Variant]

	传入一个/组用户`users`，返回用户的全名`Cn=username/o=XXXXXX`格式，**如果用户不存在将被自动过滤**

- **getLastName(Users As Variant)**[Variant]

	传入一个/组`users`，使用公式`@Name( [CN];users]`返回名称

- **Post(KEY As String)**[Variant]

	传入一个KEY，根据key获取POST请求对应的字段数组值

		var unid = util.post("unid")(0)

- **CGI_Get(key)**[String]

	获取一个get方式传递给代理的参数，返回一个字符串。

- **URLDecode(strContent  As String )**[String]

	解析一段urlEncode编码的字符串。
- **TypeOf(var As Variant)**[String] 

	返回`var`类型。如果`String`类型值为空或者数组第一个值为空，且只有一个值，都返回`EMPTY`

- **SendMail(Subject As String,f As Variant,t As Variant,st As Variant,body As Variant)**

	发生邮件

	- **Subject**：主题
	- **f**	：发件人
	- **t** ：收件人
	- **st**：抄送人
	- **body**：邮件正文，支持html字符串。OutLook，FoxMail可正常显示。
- **SendTodo(info As BMap)**

	发生一个待办更新到待办中心

	参数BMap结构

	- key：待办关键字，必须，待办唯一映射关系
	- serverName（options）：服务器名，默认当前服务器名称
	- dbFilePath（options）：应用库路径，默认`util.db.filepath`
	- appName（options）：应用库名,默认`util.db.title`
	- flowid（options）: 流程单号,默认空
	- proposer（options）: 申请人,默认空
	- subject（options）: 待办主题,默认空
	- flowName（options）: 流程名称,默认空
	- docid（options）: 文档编号,默认空
	- newdealer（options）: 当前处理人,默认空
	- curdealer（options）: 上一处理人,默认空
	- status（options）: 当前状态,默认空
	- arriveTime（options）: 到达时间，默认`util.ServerTime`
	- todoType（options）: 待办类别(普通1,研发2/3等),默认1
	- dns（options）: 应用库所在服务器域,默认`util.DNS`
	- url（options）: 文档链接URL,默认空

		
			dim info as new BMap
			
			info.setOne("key",docTarget.WF_DocUNID)

			info.setOne("Url",docTarget.HttpURL)

			info.setOne("curdealer",util.user)

			util.sendTodo(info)
		

- **updateTodo(todoDoc as NotesDocument)**
	
	更新待办到待办中心

	- **docTodo.TodoKey** ：待办key
	- **docTodo.FlowName**：流程名称
	- **docTodo.Subject** ：待办主题
	- **docTodo.DocId** 	 ：待办文档UNID
	- **docTodo.FlowId**  ：待办（流程）文档ID
	- **docTodo.Proposer**:文档创建人
	- **docTodo.PreDealer**:待办更新后上一处理人（即待办更新人） 
	- **docTodo.CurDealer**：当前待办人员
	- **docTodo.Status**	  :流程状态
	- **docTodo.Url**	：流程文档Url
	
		
			On Error Goto erh
			Dim docTodo As New NotesDocument(me.DBApp) 
	 		'获取当前流程的状态
			Dim i As Integer
			Dim ncs As Variant ' 当前状态节点 
			Dim node As BNode
			Dim isDo As  Boolean 
			ncs = Me.DocTarget.Getitemvalue(G.FIELD_WF_CURRENTNODE)
			Dim usersTodo As  Variant
			Dim unauthors As Variant 
			Dim subject  As String
			subject = ""
			For i = 0 To Ubound(ncs) 
				Set node = Me.GetNode(ncs(i))
	 			'获取节点是否需要发送待办
				If(node.DocConfig.Hasitem(G.FIELD_NODE_ISSENDTODO)) And  node.DocConfig.Getitemvalue(G.FIELD_NODE_ISSENDTODO)(0)<>"" Then
					isDo = Cint(node.DocConfig.Getitemvalue(G.FIELD_NODE_ISSENDTODO)(0))
				Else 
					isDo = 1
				End If
				If isDo Then	
					unauthors = node.GetUnHandleAuthors()
					If util.Empty(usersTodo) Then
						usersTodo = unauthors
					Else 
						usersTodo = Arrayappend(usersTodo,unauthors)
					End If
					If subject = "" Then
						subject = node.ToDoSubject
					End If 
				End If 
			Next   
			If subject = "" Then
				subject = Me.NodeActive.ToDoSubject
			End If 
			Dim key As Variant
		 
			key = Me.DocTarget.Getitemvalue(G.FIELD_WF_DOCUNID)
			
			Dim url As String
			url = util.Profile.WebBaseUrl(0)+"/0/"+me.DocTarget.Universalid +"?opendocument"
			
			docTodo.TodoKey  = key
			docTodo.FlowName = Me.FlowName
			docTodo.Subject = subject
			docTodo.DocId =  Me.DocTarget.Getitemvalue(G.FIELD_WF_DOCUNID)
			docTodo.FlowId =  Me.DocTarget.Getitemvalue("WF_DocNo")
			docTodo.Proposer = Me.DocTarget.Getitemvalue(G.FIELD_WF_CREATOR)
			docTodo.PreDealer = util.User
			docTodo.CurDealer =  usersTodo
			docTodo.Status =  Me.DocTarget.Getitemvalue(G.FIELD_WF_STATUS) 
			docTodo.Url =  url 
			Call util.UpdateToDo(doctodo)

- **GetProfile(db As NotesDatabase,profileName As String,profilekey As String)**
  
	获取数据库简要表信息，此方法获取简要表，如果简要表不存在，返回noting
	database对象getProfile返回的简要表，如果不存在会自动创建，二者主要差异处

	- **db**:简要表所在数据库
	- **profileName**：简要表名称
	- **profilekey**：简要表key值
	 

## BLog ##
**引用util.lib 后可以直接通过`L.Add`对象调用方法，代理执行完毕会自动执行Save操作**

### 方法 ###

- **Add**
	
	L.add(desc as String,flag as Boolean)
	- 参数
 		- **desc**（String）要写入日志的文本内容
 		- **flag**（Boolean）是否错误，true表示错误信息，false表示非错误信息。

- **Save**
	
	L.Save(flag as Boolean)
	
	Save方法会被自动执行，因此不需要手动执行此方法。

	- 参数
		- **flag** （Boolean）为true宝石只保存错误，为false表示保存所有通过L.add方法添加到信息 
