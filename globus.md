#从IMG上下载文件尝试 1
---
### 2023年9月15日15点14分
IMG的默认下载有globus这个选项，介于其他的选项我都搞不明白，且出于有现成的软件就用现成的软件的想法，开始尝试使用globus
	![](/image/001.jpg)
1. 注册账号
   1. 使用google账号注册，这样在下载的时候可以直接填写Gmail账号，希望可以省点事
2. 尝试新手教程
   1. 地址：https://docs.globus.org/how-to/get-started/
   2. 一些概念
        -  **Collection（集合）**
            集合是一个命名位置，其中包含您可以使用 Globus 访问的数据。集合可以托管在许多不同的系统上，包括校园存储、HPC 集群、笔记本电脑、Amazon S3 存储桶、Google Drive 和科学仪器。使用 Globus 时，您无需知道物理位置或有关存储的详细信息。您只需要一个集合名称。集合允许授权的 Globus 用户浏览和传输文件。集合还可用于与他人共享数据，并允许其他 Globus 用户发现。Globus Connect 用于托管集合。
        - **Fire-And-Forget Data Transfer**
            在您请求文件传输后，Globus 会接管并代表您完成工作。您可以离开文件管理器、关闭浏览器窗口，甚至注销。 
            Globus 将优化传输性能、监控传输的完成度和正确性，并从网络错误和收集停机中恢复。 Globus 服务通常会实现高可用性，对可靠性低得多的网络和收集主机上发生的数据传输提供几乎不间断的监督。当传输中途遇到问题时，Globus 会从故障点恢复，并且不会重新传输原始请求中指定的所有数据。 Globus 可以处理极大的数据传输，甚至是那些在集合的身份验证有效期内（由集合管理员控制）未完成的数据传输。如果您的凭据在传输完成之前过期，Globus 将通知您对集合重新进行身份验证，之后 Globus 将从暂停的位置继续传输。这些广泛的功能使 Globus 的数据传输真正“一劳永逸（Fire and forge）”。
       - **Endpoint(终结点)**
            终结点是托管集合的服务器。
            如果您希望能够使用 Globus 访问、共享、传输或管理数据，第一步是在存储（或将要）数据的系统上创建一个端点。 Globus Connect 用于创建端点。端点可以是笔记本电脑、个人桌面系统、实验室服务器、园区数据存储服务、云服务或 HPC 群集。如下所述，使用 Globus Connect Personal 在笔记本电脑或其他个人系统上设置您自己的 Globus 端点很容易。共享服务（如校园存储服务器）的管理员可以使用 Globus Connect Server 设置多用户端点。只要你获得终结点管理员或集合管理员的授权，就可以使用其他人设置的终结点。
3. 设置并使用 Globus Connect Personal(在linux服务器上)
    教程地址：https://docs.globus.org/how-to/globus-connect-personal-linux/
    - 前置要求：
    1. Tcl/Tk（不知道能不能用上，总之先搞一个试试）
       - 如果要使用图形界面，则必须安装 Tcl/Tk。如果没有 Tcl/Tk，您仍然可以使用 Globus Connect Personal，但只能在命令行模式下使用它。
       - Globus Connect Personal的图形设置工具不需要Tcl/Tk.只有设置后用于配置和管理安装的界面才具有此限制。

        `sudo apt-get install tk tcllib`
        但是我没有权限，所以用的是
        >`# 首先创建一个新的文件夹来存储下载的包`
        `mkdir TCL`
        `# 然后开始下载`
        `  apt source tk tcllib`
        `# 进入对应的文件夹`
        ` cd tcllib-1.20+dfsg/`
        `# 指定安装目录`
        ` ./configure --prefix=/home/menglili/IMG_dataset/TCL/`
        `make `
        `make install`
        
        >
            也许成功了吧，总之不管了
    2. 下载Globus Connect Personal 
        > `wget https://downloads.globus.org/globus-connect-personal/linux/stable/globusconnectpersonal-latest.tgz`