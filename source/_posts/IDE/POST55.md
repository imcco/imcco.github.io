---
title: SVN的使用
tags:
  - SVN
category: SVN
abbrlink: 57755
date: 2017-12-18 20:34:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0006.jpg)

SVN是Subversion的简称，是一个开放源代码的版本控制系统，相较于RCS、CVS，它采用了分支管理系统，它的设计目标就是取代CVS。互联网上很多版本控制服务已从CVS迁移到Subversion。说得简单一点SVN就是用于多个人共同开发同一个项目，共用资源的目的。
<!--more-->

### 为什么要使用配置管理
及时了解团队中其他成员的进度。

轻松比较不同版本间的细微差别；

记录每个文件成长的每步细节，利于成果的复用(reuse);

资料共享，避免以往靠邮件发送文件造成的版本混乱；

人人为我，我为人人。所有成员维护的实际是同一个版本库，无需专人维护所有文件的最新版本；

协同工作，大大提高团队工作效率，无论团队成员分布在天涯还是海角；

### Subversion/TortoiseSVN的版本控制系统
Subversion：是一个开源的版本控制系统，拥有CVS的大部分特征，并在CVS的基础上有更强的扩展，用来代替 CVS 系统。

TortoiseSVN：SVN的客户端工具，和资源管理器完美集成，基于TortoiseCVS的代码开发，使用上与TortioseCVS极其相似;



### 配置库（ Repository ）
SVN的核心是配置库，储存所有的数据，配置库按照文件树形式储存数据－包括文件和目录，任意数量的客户端可以连接到配置库，读写这些文件。通过写数据，别人可以看到这些信息；通过读数据，可以看到别人的修改。 

最特别的是 Subversion 会记录配置库中的每一次更改，不仅针对文件也包括目录本身，包括增加、删除和重新组织文件和目录。

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN001.jpg)

### 工作副本（WorkSpace）
与位于中央配置库相对应的是每个人的工作空间，它是每个程序员工作的地方，程序员从配置库拿到源代码，放在本地作为工作副本，在工作副本上进行查看、修改、编译、运行、测试等操作，并把新版本的代码从这里提交回配置库库中。

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN002.jpg)

### SVN工作模式
#### 复制-修改-合并方案(Subversion默认的模式)

在这种模型里，每一个客户读取项目配置库建立一个私有工作副本——版本库中文件和目录的本地映射。用户并行工作，修改各自的工作副本，最终，各个私有的复制合并在一起，成为最终的版本，这种系统通常可以辅助合并操作，但是最终要靠人工去确定正误。

#### 锁定-修改-解锁方案


在这样的模型里，在一个时间段里配置库的一个文件只允许被一个人修改。 此模式不适合软件开发这种工作。

### SVN安装

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN003.jpg)

安装完毕后，在“资源管理器”里点击右键，会有如下菜单出现：

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN004.jpg)

### 目录结构

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN005.png)

### SVN使用流程

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN006.png)

### SVN使用图标说明

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN007.png)

### SVN CheckOut检出

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN008.png)

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN009.png)

### SVN CheckOut检出注意事项

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN010.jpg)

检出深度：

1. 全递归(默认选择)

   检出完整的目录树，包含所有的文件或子目录。

2. 直接节点，包含目录 

 检出目录，包含其中的文件或子目录，但是不递归展开子目录。

3. 仅文件子节点

  检出指定目录，包含所有文件，但是不检出任何子目录。

4. 仅此项。 

 只检出目录。不包含其中的文件或子目录。

省略外部引用：如果项目含有外部项目的引用，而这些引用我们不希望同时检出，请选中忽略外部项目复选框。如果选中了这个复选框，更新的时候要使用命令”更新至版本Update to Revision...”

### SVN Update更新
作用：更新工作副本使其成为版本库中的最新版本

SVN将显示出更新的文件和更新的次数

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN011.jpg)

### SVN Commit提交

对工作副本进行编辑后提交到SVN

在右键菜单中点击SVN Commit

提交前写好信息，点击确定

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN012.jpg)

### SVN 权限控制
当进行提交文件操作的时候您将看到权限提示信息

输入您的用户名和密码

保存权限设置（见红圈） ，可以避免将来重复输入用户名和密码

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN013.jpg)

### SVN 如何删除认证数据
>  步骤：点击右键 —— 选择设置  ——  以保存数据  ——  清除认证数据

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN014.jpg)

### SVN  Show log显示日志

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN015.jpg)

### SVN  日志信息填写规则
好的日志信息和糟糕的日志信息

日志信息主要记录的是每次的修改内容。建议把一些重要数据、关键操作写到日志信息中。

注：修改人和提交时间由软件自动记录，无需人工写入日志信息

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN016.jpg)

### SVN  如何得到历史版本
工作副本右键 - 显示日志 - 选择所需的版本号 - 保存版本至

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN017.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN018.jpg)

### SVN  Revert(还原)
作用：撤销本地所有未提交的修改

> 注意：还没有执行Commit操作之前执行此命令才可以，否则无效

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN019.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN020.jpg)

### SVN  Add添加
选中文件/文件夹（在新文件/文件夹所在父文件夹点击右键），

在菜单中选择“添加Add”命令。不需要受SVN控制的文件请取消打钩。

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN021.jpg)

### SVN  Add to Ignore List （忽略文件）
- 添加忽略文件

  右键一个单独的未进入版本控制文件TortoiseSVN (加入忽略列表)Add to Ignore List，会出现一个子菜单允许你仅选择该文件，或者所有具有相同后缀的文件。 

- 删除忽略文件：

  如果你想从忽略列表中移除一个或多个条目，右击这些条目，选择TortoiseSVN → 从忽略列表删除。

已进入版本控制的文件或目录不能够忽略  

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN022.jpg)

### SVN  版本分支
这个版本历史分析图能够显示分支/标签从什么地方开始创建，以及什么时候删除。

版本分支图将显示以下内容: 

1. 增加文件/文件夹
2. 已删除文件/文件夹
3. 分支最新版本
4. 一般的文件/文件夹 

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN023.jpg)

### SVN  如何创建分支
两种方式：

1. 在版本库浏览器中创建分支

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN024.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN025.jpg)

2. 在工作副本创建分支

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN026.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN027.jpg)

创建分支成功

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN028.jpg)

### SVN  Switch切换
 文件创建分支后，可通过Switch选择在主干工作或者在分支工作;

注意:

1. 切换操作起来就象更新，因为它没有丢弃我们在本地做的修改。
2. 在工作副本里，当我们进行切换的时候，任何没有提交过的修改都会被混合。

解决方法：

1. 在切换前提交修改;
2. 把工作副本恢复到一个已经提交过的版本(比如最新版本)。 

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN029.jpg)

### SVN  合并
假设我们在分支上进行修改：添加一条输出语句：

 System.out.println(“测试是否能将分支的修改合并到主干中!”) ;提交到配置库中，现在执行将分支合并到主干的操作
 
 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN030.jpg)
 
### SVN  分支合并到主干
右键- 主干的文件- 选择合并

 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN031.jpg)
 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN032.jpg)
 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN033.jpg)
 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN034.jpg)

### SVN  合并时出现冲突

 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN035.jpg)
 
### SVN  编辑冲突

 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN036.jpg)

### SVN  冲突已解决

 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN037.jpg)

### SVN  合并成功

 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN038.jpg)

### SVN  什么是冲突
团队协同工作时，当多位团队成员同时修改同一个文件，造成本地文件与SVN系统中的文件版本不一致，而导致文件无法提交的情况

### SVN  冲突产生原因
当团队协同工作的时候，多位团队成员同时操作一个文件。团队成员A操作完成后，将该文件提交到SVN上。此时，其他团队成员的本地文件与SVN上的文件版本不一致。当团队成员B操作完成并对文件进行提交操作时，就会产生冲突

### SVN  冲突提示页面
#### 提交时产出冲突

 ![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN039.jpg)
 
### SVN  冲突解决方案
遇到冲突时，需要update该文件。我们将看到三个带问号的文件

　filename.mine

　filename.rOLDREV

　filename.rNEWREV
　
　![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN040.jpg)
　
面对文件冲突，我们可以选择以下两种方式解决冲突

使用工具解决冲突

用revert放弃所做的修改

### SVN  使用工具解决冲突
使用自带工具，如图所示

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN041.jpg)

对比两个版本的文件，解决冲突

工具中会列出两个版本冲突的部分，并让我们选择使用哪个版本的内容

### SVN  用revert放弃所做的修改
选择SVN还原（revert） 放弃自己所做的修改

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN042.jpg)

### SVN  解决冲突之后
通过上面所说的方法，解决冲突后要选择已解决的（resolved）

解决后，带问号的三个文件将自动删除

最后选择commit提交到SVN

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN043.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN044.jpg)

### SVN  关于冲突的建议
修改文件之前，先进行一次update操作

修改完成后，及时commit，不要在本地停留过长时间

在多位团队成员协作时，尽量修改自己撰写的部分，尽量不要修改不属于自己撰写的部分

出现冲突很正常，可以通过前面的方法解决，不要相互覆盖

### SVN  其他SVN的功能

- Export（导出）

导出你需要的文件，导出后不在受SVN控制；而checkout检出的文件仍受SVN控制；

- Import（导入）

将需要的文件导入到版本库中；

- Relocated（重新定位）

当服务器上的版本库目录已经改变，我们可以把工作复本重新定位；

- CleanUp（清理）

递归的清理工作副本，删除锁定和保留的未完成操作，如果你得到工作副本已经锁定的错误，运行这个命令删除无用的锁定，并将工作副本恢复到可用的状态；

### SVN  Subclipse的安装步骤（Eclipse集成）

1. 帮助 > 软件更新 >查找并安装 

2. 选择“要安装的新功能部件” 选项并点击Next。

3. 点击“新站点”并且创建一远程站点，

   名字:Subclipse

   URL:http://subclipse.tigris.org/update_1.2.x

4. 在结果安装窗口中，把"Subeclipse in the Features"    选择到安装列表中，并且通过向导来开始安装插件。

5. 完成这些之后，重新启动Eclipse。 

### SVN  在Eclipse下使用的图标

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN045.jpg)

### SVN  Eclipse下使用的主要功能

将版本库导入到SVN资源库

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN046.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN047.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN048.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN049.jpg)

将新建项目导入到版本库

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN050.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN051.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN052.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN053.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN054.jpg)

Eclipse中小组的使用

![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN055.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN056.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN057.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN058.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN059.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN060.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN061.jpg)
![image](http://ovi3ob9p4.bkt.clouddn.com/SVN/SVN062.jpg)
