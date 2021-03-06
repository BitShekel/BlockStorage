---

copyright:
  years: 2014, 2018
lastupdated: "2018-03-15"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# 在 Microsoft Windows 上连接到 MPIO iSCSI LUN

开始之前，请确保已通过 [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}对访问 {{site.data.keyword.blockstoragefull}} 卷的主机授权：

1. 在 {{site.data.keyword.blockstorageshort}} 列表页面中，单击与新供应的卷关联的**操作**按钮，然后单击**授权主机**。
2. 从列表中选择所需的一个或多个主机，然后单击**提交**；这将授权所选主机访问卷。

## 如何安装 {{site.data.keyword.blockstorageshort}} 卷

下面是将基于 Windows 的 {{site.data.keyword.BluSoftlayer_full}} 计算实例连接到多路径输入/输出 (MPIO) 因特网小型计算机系统接口 (iSCSI) 逻辑单元号 (LUN) 所需的步骤。示例基于 Windows Server 2012。对于其他 Windows 版本，可以根据相应操作系统 (OS) 供应商文档来调整这些步骤。

### 配置 MPIO 功能

1. 启动服务器管理器，并浏览至**管理** > **添加角色和功能**。
2. 单击**下一步**以转至“功能”菜单。
3. 向下滚动并单击**多路径 I/O** 复选框。
4. 单击**安装**以在主机服务器上安装 MPIO。
![添加角色和功能](/images/Roles_Features.png)

### 添加对 iSCSI 的 MPIO 支持

1. 打开“MPIO 属性”。要打开“MPIO 属性”，请单击**开始**，指向“管理工具”，然后单击 **MPIO**。
2. 单击**发现多路径**选项卡。
3. 选中**添加对 iSCSI 设备的支持**复选框，然后单击**添加**。系统提示重新启动计算机时，单击**是**。

**注**：对于 Windows Server 2008，添加对 iSCSI 的支持将允许 Microsoft 设备特定模块 (MSDSM) 声明所有要进行 MPIO 的 iSCSI 设备，这首先需要连接到 iSCSI 目标。

### 配置 iSCSI 启动器

1. 从服务器管理器启动 iSCSI 启动器，然后选择**工具** > **iSCSI 启动器**。
2. 单击**配置**选项卡。
    - “发起方名称”字段可能已使用类似于 iqn.1991-05.com.microsoft: 的条目填充。
    - 单击**更改**以将现有值替换为 iSCSI 限定名 (IQN)。可以从门户网站的“{{site.data.keyword.blockstorageshort}} 详细信息”屏幕中获取 IQN 名称。
    ![iSCSI 启动器属性](/images/iSCSI.png)
    - 单击**发现**选项卡，然后单击**发现门户网站**。
    - 输入 iSCSI 目标的 IP 地址，并使“端口”保留为缺省值 3260。 
    - 单击**高级**以启动“高级设置”窗口。
    - 选中**启用 CHAP 登录**以启用 CHAP 认证。
    ![启用 CHAP 登录](/images/Advanced_0.png)
    **注**：“名称”和“目标私钥”字段区分大小写。
         - 删除“名称”字段中的任何现有条目，然后输入门户网站中的用户名。
         - 在“目标私钥”字段中，输入门户网站中的密码。<br/>
         **注**：“名称”和“目标私钥”值可以从门户网站的“{{site.data.keyword.blockstorageshort}} 详细信息”屏幕中获取以分别作为“用户名”和“密码”。
    - 单击“高级设置”和“发现目标门户网站”窗口中的**确定**，以返回到主“iSCSI 启动器属性”屏幕。如果收到认证错误，请重新检查用户名和密码条目。
    ![不活动的目标](/images/Inactive_0.png)
    请注意，目标的名称显示在“发现的目标”部分中，但状态为“不活动”。 
    
    以下步骤说明如何激活目标。
    
### 激活目标

1. 单击**连接**以连接到目标。
2. 选中**启用多路径**复选框以启用到目标的多路径 IO。
![启用多路径](/images/Connect_0.png)
3. 单击**高级**，然后选择**启用 CHAP 登录**。
![启用 CHAP](/images/chap_0.png)
4. 在“名称”字段中输入用户名，然后在“目标私钥”字段中输入密码。<br/>
**注**：“名称”和“目标私钥”字段值可以从门户网站的“{{site.data.keyword.blockstorageshort}} 详细信息”屏幕中获取以分别作为“用户名”和“密码”
5. 单击**确定**，直至显示“iSCSI 启动器属性”窗口。“发现的目标”部分中目标的状态已从“不活动”变为“已连接”。
![“已连接”状态](/images/Connected.png) 


### 在 iSCSI 启动器中配置 MPIO

1. 启动 iSCSI 启动器，然后在“目标”选项卡上，单击**属性**。
2. 在“属性”窗口中，单击**添加会话**以启动“连接到目标”窗口。
3. 选中**启用多路径**复选框，然后单击**高级...**。
![目标](/images/Target.png) 
  
4. 在“高级设置”窗口中
   - 使“本地适配器”和“启动器 IP”字段的值保留为“缺省值”。对于将多个接口连接到 iSCSI 的主机服务器，需要为“启动器 IP”字段选择相应的值。
   - 从“目标门户网站 IP”下拉列表中，选择 iSCSI 存储器的 IP。
   - 单击**启用 CHAP 登录**复选框。
   - 输入从门户网站中获取的“名称”和“目标私钥”值，然后单击**确定**。
   - 在“连接到目标”窗口上，单击**确定**以返回到“属性”窗口。“属性”窗口的“标识”窗口中现在应该会显示多个会话。现在，iSCSI 存储器中有多个会话。
![设置](/images/Settings.png) 
   
5. 在“属性”窗口中，单击**设备**并启动“设备”窗口。设备接口名称应该在设备名的开头具有 mpio。<br/>
  ![设备](/images/Devices.png) 
  
6. 单击 **MPIO** 以启动“设备详细信息”窗口。此窗口允许您为 MPIO 选择负载均衡策略，并且还会显示 iSCSI 的路径。在此示例中，显示了两条可用于 MPIO 的路径，并且负载均衡策略为“带子集的循环法”。
  ![设备详细信息](/images/DeviceDetails.png) 
  
7. 单击**确定**多次以退出 iSCSI 启动器。



## 如何验证是否在 Windows 操作系统中正确配置了 MPIO

要验证是否已配置 Windows MPIO，必须先确保已启用 MPIO 附加组件，并且已完成必需的重新引导：

![Roles_Features_0](/images/Roles_Features_0.png)

一旦完成了重新引导并且添加了“性能存储设备”，就可以验证 MPIO 是否已配置且生效。要执行此操作，请查看**目标设备详细信息**，然后单击 **MPIO**：
![DeviceDetails_0](/images/DeviceDetails_0.png)

如果尚未在“性能存储设备”上配置 MPIO，并且 {{site.data.keyword.BluSoftlayer_full}} 团队执行了维护或发生网络中断，那么存储设备会断开连接并变为不可用。MPIO 将确保在发生这些事件期间获得额外级别的连接，并且会保留已建立的会话，使活动读/写操作转至 LUN。

## 如何卸装 {{site.data.keyword.blockstorageshort}} 卷

下面是断开基于 Windows 的 Bluemix 计算实例与 MPIO iSCSI LUN 的连接所需的步骤。示例基于 Windows Server 2012。对于其他 Windows 版本，可以根据相应操作系统供应商文档来调整这些步骤。

### 启动 iSCSI 启动器。

1. 单击**目标**选项卡。
2. 选择要除去的目标，然后单击**断开连接**。

### （可选）如果不再需要访问 iSCSI 目标，请执行以下操作：

1. 单击 iSCSI 启动器中的**发现**选项卡。
2. 突出显示与存储卷关联的目标门户网站，然后单击**除去**。
