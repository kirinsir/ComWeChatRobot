# 描述
PC微信机器人，实现以下功能：
1. 获取通讯录  
2. 发送文本、图片、文件、xml文章、名片、群艾特消息  
3. 根据wxid查询好友信息  
4. 根据群ID获取所有群成员wxid  
5. 检测好友状态（是否好友、被删除、被拉黑）  
6. 接收各类消息，可写回调函数进行处理  
7. 封装COM接口，方便使用自己喜欢的语言进行调用  

# 用途
1. 淘客发单  
2. 无痕清粉  
3. 微信公众号采集  
4. 聊天记录备份  
5. 其他你能想到的用途  

# 可用版本
微信电脑版**3.5.0.46**  
微信电脑版**3.6.0.18**  
微信电脑版**3.7.0.26**  
主分支对应微信3.7.0.26版本，其他版本请查看对应分支。  

# 写给大家  
大家好，最近大家报了好多issue，有的是BUG，有的是问新功能，有的是问如何实现某种功能，看到并且有空的时候就会回复，不过作者没有三头六臂，如果回复不及时还请见谅。  

最近填了之前的一些坑，比如数据库无法查询BLOB类型，冗余的接口、缺少的注释，甚至是某个单词拼写错误。。   

一些老哥提的需求，也尽可能的往上加了，功能比3.6.0.18又丰富了一些，别的功能，短期是不会添加了，优先解决一些接口导致的崩溃（主要是接收消息），新功能就留到下个微信版本吧。  

这几天也一直在着手解决接收消息的bug，不得不说，又学会了一些奇奇怪怪的东西，比如COM的连接点，个人感觉这东西还挺好用的，但COM毕竟是不怎么火的技术，大家想用别的语言实现连接点，资料可能都找不到。  

也实现了64位程序注入DLL到32位进程，还有如何使用汇编在内存中构造一个函数。各种花里胡哨。  
要是最开始就解决了64位注入32位，可能就根本不会使用COM了。  

本来还想在不引入第三方库的情况下，完成http通信，不过想了想，重复发明轮子太耗费脑细胞，等做好说不定微信4.0都出来了。

现在http只有一个测试接口，暂时还不能使用。后面会加上所有功能接口。  

一个人搞这个真的很累，有些老哥做了C#、易语言的SDK，感谢你们，有空我会把资源整理一下，写在这里。  
也感谢提交BUG的各位，是你们让此项目变得更健壮。  
# 编译环境
**Visual Studio 2019**(平台配置：win32(x86))
# 原理
通过逆向PC微信，定位到关键CALL，dll内联汇编调用  
注册32位COM组件，供64位/32位进程外部调用  
# 目录说明
`./CWeChatRobot`：COM组件的实现代码  
`./DWeChatRobot`：注入的DLL实现代码，根据平台配置可编译出socket版和COM版  
`./wxRobot`:  包含C#的调用示例  
`./Python`：python示例和接口测试文件  
`./wxDriver`：driver的实现代码，有些函数具有一定的学习意义  
`./Release/CWeChatRobot.exe`：编译的COM组件  
`./Release/DWeChatRobot.dll`：编译的DLL文件  
`./Release/socket`：包含wxDriver.dll和socket接口的DLL  
`./Release/WeChatTools.exe`：用于调试时注入或卸载DLL程序，具体参阅相关代码  
# 注册COM
以管理员权限执行以下命令：  
```shell
# 安装
CWeChatRobot.exe /regserver
# 卸载
CWeChatRobot.exe /unregserver
```
# 调用
**Python：**  
参考[wxRobot.py](/Python/wxRobot.py)  
**C#：**  
参考[Program.cs](/wxRobot/Program.cs)
# 更多功能 
后续计划功能：  
1. 修改好友备注  
2. 优化语音和图片Hook  

有空的时候会按照上述顺序进行开发，不过嘛，计划只是计划，如果未实现也请见谅    
**也欢迎您提交PR**  
# 更新记录
## 2022.04.01
1. 使用SAFEARRAY返回通讯录列表，可正确显示好友昵称中的特殊符号  
2. README中添加目录说明  
3. 更新C#示例代码，添加好友列表的遍历示例  
## 2022.04.11  
1. 修改获取个人信息接口和发送文章接口，兼容老版wxid（未经测试，如有问题请提ISSUE）  
2. 添加接收消息的接口，可以写回调对消息进行处理（参考Python示例文件）  
## 2022.04.12
1. 添加发送群艾特消息的接口  
## 2022.04.12
1. 添加通过群ID获取所有群成员wxid接口  
## 2022.04.13
1. 更新群艾特接口，可同时艾特多人  
## 2022.04.18
1. 添加获取数据库句柄接口（部分句柄，获取全量句柄需Hook）  
2. 添加执行SQL命令接口  
3. 添加在线数据库备份接口  
## 2022.06.01  
1. 适配微信**3.7.0.26**版本，部分功能未经测试，如有问题请报issue  
## 2022.06.02  
1. 添加通过好友申请接口（配合接收消息接口可自动通过好友）  
2. 添加获取聊天记录数据库句柄（好友申请消息类型为0x25）  
3. 优化了StartService接口，重复注入时不再关闭远程进程  
## 2022.06.04  
1. 完成通过wxid和v3数据加好友的COM接口（后续添加通过微信号、手机号、QQ号查询V3数据接口）  
2. 优化接收消息逻辑，添加消息时间；新增Hook发送消息，返回数据中以一个BOOL值区分发送和接收  
3. 修复了一个BUG，该BUG可能导致Release配置下，COM接口无法正常加载DWeChatRobot.dll计算偏移  
## 2022.06.07  
1. 添加获取当前微信版本（读注册表）和启动微信的接口  
2. 优化数据库查询接口，现在可以正常查询BLOB类型  
## 2022.06.10  
1. 新增关注公众号、网络查询用户信息、Hook语音、未加密图片、自定义微信版本号接口  
2. Hook语音和图片的接口暂时还有瑕疵，图片收到后有可能不会自动下载；语音消息的文件名暂时是时间戳，计划替换为该条消息id。有空再做优化。  
## 2022.06.13  
1. 优化发送艾特消息接口，新增一个参数，指示是否自动填充被艾特人昵称  
2. 优化发送文章消息接口，新增一个参数，用于展示消息卡片缩略图  
3. 新增删除好友接口  
4. 新增发送小程序接口  
## 2022.06.18  
1. 修复了多个BUG  
2. 整理代码结构，方便后续开发基于websocket的接口  
3. 添加64位程序注入DLL到32位程序的driver 
# 打赏作者
请给作者一个star，感谢感谢  
# 免责声明
代码仅供交流学习使用，请勿用于非法用途和商业用途！如因此产生任何法律纠纷，均与作者无关！
