# xuexiziliao
spring 源码
spring注解编程发展    纯配置文件方式 -> 基于注解的方式
	
	1.0
	transation
	2.0 还是没有脱离配置文件 但是简化开发 业务拆分 <import>
	3.0 @configuation 标识的配置类 @bean 需要commont scan 配置文件
		3.1 版本 componenscan 理论可以脱离配置文件
			扫描包以及子包当前下的类
		业务拆分 @ipmort	导入第三方的配置类 也可以将类导入ioc中
		如果类型实现了 importselector 不会将该类型注入 ， 而是注入重写方法中的bean ,以便实现动态注入
		动态注入 还能实现importbeanderedister 接口   相当于注册器 
		（）
		@eanble**   本质就是结合@import
	4.0 @condition  条件注解
		用来 条件是否纳入ioc
		根据特定的业务需求 是否注入
		onclass onbean 
	5.0 @indexed 解决@companScan 
		扫描路径多 目录多 文件多 速度慢
		@indexed 在编译的时候 处理 
		将路径保存在一个文件，加载时直接加载
		增加启动速度
	
	
SPI      定义接口   实现交给其他   meta-inf/services 告知接口的实现
波波烤鸭



读取 meta-inf/spring.factories
默认 131个
加载 130 + 
去重  
排除 显示声明的 比如datasorceautoconfigation
过滤 根据另一个文件中的配置 过滤 使用 condition  比如 
	conditiononbean conditiononclass conditionafter 等等
	比如redis自动配置文件 
	conditiononclass  = redisoperation 
	当存在该类时才会加载redis的自动装配
过滤完成剩下20+个



其他厂商加入了 spring.factories 的默认文件
为什么 spring 加入了这么多皇亲国戚？

springfactoeloader.load   



defeninportselector

延迟
getimportgroup 方法先执行了
自定义group 执行了 
  如果返回null  就会执行父类中的方法
如果有实现 就不会执行其中的方法
这就是为什么不走 importselect方法

为什么要这么设计延迟？

@import 注解逻辑 会在@importresource @bean 之前
使用  这种延迟 便于做进一步的扩展

初始化构造方法
	推断工程类型  serlvet 响应式 非web
	设置初始化器 7个
			
		
		
	设置监听器
		
		
		

	推断主类
		通过获取栈对象 ， 获取 “main” 方法所在class

执行run 方法

 启动计时
加载错误reporter 启动错误回调接口
设置无显示器启动
获取事件发布器
	evenpulistrunlisteing  
	执行构造方法
	关联上了监听器   初始11个
发布starting 事件  监听starting 事件的监听器就会触发
	事件类型  applicationstartingevent
		
应用参数信息
系统环境变量
	会发布 环境配置事件
打印banner
创建上下文对象 
加载配置的启动异常处理器
刷新上下文之前的操作
	发布上下文准备刷新事件
刷新应用上下文 完成spring容器初始化
刷新后操作
	发布上下文刷新完毕事件
结束记录启动时间
发布stared事件 启动完成
callrunner 调用 applicationrunner 、commanline实现类 加载初始化操作
如果启动失败 会发布启动失败事件
发布运行中事件
返回上下文






	                                                      

springboot 自动装配
spring run
属性文件加载过程
	configfileapplicationlistener
		监听所有的事件
	在onapplicationevent中判断
	如果是环境准备事件        ApplicationEnvironmentPreparedEvent
	如果是应用准备完成事件	ApplicationPreparedEvent
	方法会加载 environmentpostproess 环境后置处理器
		用spring.factories文件中的4个， 然后 add 自身configfileapplicationlistener 一共五个
		为什么能add自身 因为 configfileapplicationlistener 也实现了environmentpostproess 接口
		4个系统本身的不用管 主要看 configfileapplicationlistener 重写的方法 postpreocessenvironment
		该方法中 首先是 针对配置文件中的随机值的处理
			    然后 new loader().load 方法去加载配置文件
		首先是初始化 new loader对象 
			首先是环境   jdk 参数等等
			然后通过 springfacorierloader.loadfactorier 去加载配置文件加载器 2个 一个用于加载properes 文件 一个针对 yml 文件
		然后是load方法
			处理多环境 初始化 遍历 解析 load 方法
			先找到文件，然后根据文件后缀，找到处理器 
			有一个load 方法会去找配置文件
				怎么找？
				如果定义了spring.location.property,就会去自定义目录中找
				否则使用默认的4个路径
					/config  / classpath ./config classpath:/
				
				可以自定义文件名称 不一定是application 
			首先判断是否是文本，如果不是就报错
			然后 创建set 集合 
			使用 propertysourceLoader 加载
				propertysourceLoader 2个 就是之前的 一个处理prreperes 一个处理yml
				取出记载器所支持的后缀  preperesd xml yml yaml 
				然后去加载对应的文件
					使用resourceloader.getresource(location)
					得到资源对象
					判断是否存在 不存在就跳过
					然后 loaddocuments 加载文档
						获取缓存文档的主键key 然后通过key查找
						由此可知，属性文件只会加载 一次
						如果缓存中没有该文件 就会使用 loader.load加载
						loader 这个load 有两个实现类 就是之前的两个 一个处理properes 一个处理yml 
							loadproperteaa 返回一个map 该map 就保存了k v 键值对
								if xml 结尾 else application结尾
								处理 applicaton 结尾的 对象为 origintrackedpreopereloader 
									调用其 load 方法
										首先创建 charatreader 对象
											然后调用其 read 方法处理 得到 key value
						
						
项目中有 yml bootstrap.proerod 文件 
一般 bootstrap 用于连接配置中心的配置
	当前应用的名称 配置中心的地址
bootstrap 会优先执行
	加载到远程配置信息
	然后加载项目的yml文件信息
	
yml  文件有一个 springallication 容器
bootstarp 也有一个 springapplicaton 容器
	bootstrap 是父容器
	yml   是子容器
	类似于 spring springmvc
如果bootstrap 中定义的与工程中yml 文件key 重复
工程中的yml 文件会覆盖掉 bootstrap 中的
bootstarp 应用在 cloud 工程中
bootstarp  yml   同时存在 application.run 方法会执行两遍
spring.favtoea 文件中存在一个 bootstraplistener 监听器
bootstarp 监听器 会第一个启动 排在listener 第一个 因为实现了order接口
当系统启动的时候 发布环境准备完成事件
该监听器就会捕获该事件
有一个springapplicationbuilder 创建一个springapplication  对象
然后 bdulder.run 
该run方法 会调用启动时的run方法 这就是为什么会执行两次
先于系统容器加载
	比如加载配置中心的文件
	









监听
		
	在系统启动的关键节点 可以做一些扩展
		默认启动过程中的发布器来发布事件
			eventpublishrunlistener
			staring 
			enentmentprepared
			contextprepared
			strared
			fail
			running
	11个监听器会监听不同的事件


自定义监听器 实现applicationlistener
	监听的事件类型为 applicationevnet	
				表示监听所有的事件
	需要把自定义监听器添加到spring.factorier中
	

	事件广播器  用于广播事件 就是遍历 调用相应了 onapplicationevent方法 我们继承applicationlistener 就会实现这个方法
	springpiblier 的事件发布器是提供给用户使用的，上层也是委托给事件广播器处理
	事件广播器只有一个简单的实现类 simple事件广播器



tomca
acuator
