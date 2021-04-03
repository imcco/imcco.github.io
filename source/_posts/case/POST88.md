---
title: 医院信息管理信息系统
tags: case
category: case
date: 2018-02-04 15:44:36
---
![image](http://ovi3ob9p4.bkt.clouddn.com/TIETU/CT0111.jpg)

本手册以系统中的各功能模块为线索，以医院服务主要流程为导向，简单描述了医院信息管理系统中各主要模块的功能及操作方法，主要面向广大的软东医院信息管理系统的用户，目的在于协助其尽快掌握软件的基础使用与操作，同时也可以作为集中培训的基础教材。
<!--more-->

# [前言]()

因本手册其旨在于给医院各岗位工作人员提供快速操作指导，内容不足之处，敬请原谅，若有疑难问题请致电公司客服中心，我们将竭诚为您服务！

# [系统简介]()

“软东医院信息管理信息系统”是一套集成门诊挂号收费、住院收费、

药品管理等工作站的医院网络管理系统，本系统适应于二级医院，它操作简单方便，性能稳定；支持按大类收费和明细收费；大类项目和明细项目都能自定义；支持多种发票格式和收据格式，并且每种发票格式都可以自由修改；药品管理部分参考了国外一些优秀医院网络系统的先进经验，具有完善的药品进销存管理包括药品调价报损退库等强到管理功能，处方从门诊收费到药房完全通过网络传输，完全实现无纸化，数据一次性发生，摆药发药全部自动完成，99% 以上的操作实现了零输入。本系统采用先进的服务器客户端结构，客户端运行的只是和用户交互的界面，安装方便，对硬件的要求也极低，只要能运行winXP 以上操作系统的计算机都能正常运行。
# [系统配置]()

| 服 务 器  | 硬件最低配置                          | 2.0G CPU/2G内存/250G双硬盘 |
| ------ | ------------------------------- | --------------------- |
| 硬件推荐配置 | 2.8G CPU/4G内存/500G双硬盘  支持Raid-1 |                       |
| 操作系统   | Windows 2003 server             |                       |
| 数据库平台  | MS SQLserver2008                |                       |
| 工 作 站  | 操作系统Windows 2003/XP             |                       |

# [系统登录]()

点击RdClientManage.exe![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image002.jpg)进入系统后，会有登录界面出现。

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image004.jpg)

输入用户名的助记码，按回车键，如果用户存在，系统将自动显示该用户所对应的科室，输入口令，单击登录或者按回车键。如果口令正确，将根据系统管理员设置给该操作员的权限来登录系统。若口令不正确，系统将提示错误信息。如果不想进入系统点击取消即可。

# [门诊部分]()

## [1.1]()挂号管理

### 1.1.1门急诊挂号

  在左边导航栏中出现“挂号管理”的子系统图标，右边会列出挂号管理子系统的主要模块。如下!

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image006.jpg)

找到门急诊挂号模块图标点击进入。如下!

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image008.jpg)

根据要求填写相关信息，输入病人姓名等信息，可以使用回车键进行下一栏的输入（诊疗号不需要手动输入，系统会自动生成），填写完之后单击‘保存’，保存的病人信息会在挂号信息处显示。(保存完成后，系统会自动产生诊疗号和门诊号)如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image010.jpg)

 

如果要查看是否有预约患者的话，可以单击预约患者信息来查看。

如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image012.jpg)

如果未领票或票已用完系统也会给出提示信息.![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image013.gif)

如果在保存之前不对该病人进行信息输入，可以单击‘取消’。保存之前，但是信息已经输入完整，可以对该病人信息进行‘删除’。操作完成后，单击‘退出’以退出该界面。

### 1.1.2 挂号退号

   在挂号管理窗口中选择挂号退号，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image015.jpg)

 

输入该病人所对应的发票号或门诊号，单击‘查询’，选中该病人信息，单击‘确定’即可对该病人进行退号。发票号或门诊号输入错误时，单击‘取消’，重新输入。

### 1.1.3 门诊病人信息修改

  在挂号管理窗口中选择门诊病人信息修改，进入如下界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image017.jpg)

输入病人挂号时的门诊号或诊疗号，单击‘查询’，就可以对查出来的病人的信息进行修改，修改完成后单击‘确定’，否则单击‘取消’，操作完成后单击‘退出’。

1.1.5 操作员挂号报账单

在挂号管理窗口中选择专家挂号，进入如下界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image019.jpg)

选择起始时间和结束时间以及需要的统计类型和打印方式、所要报账的操作员，单击确定。

### 1.2.1门诊管理

  在左边导航栏中找到门诊医生站子系统图标点击进入门诊管理子系统，右边会列出门诊管理子系统的主要模块。找到门诊医生工作站模块图标点击进入。

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image020.gif)![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image022.jpg)

门诊医生工作站窗口打开：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image024.jpg)

如果病人是按专家挂号或者选择了相关医师，那么就选择左边的‘个人’按钮，如果病人按科室挂号，那么就选择左边的‘科室’按钮。

如果挂过号的病人信息没有出现，那么就单击‘刷新列表’。

若病人信息为白色，可以按鼠标右键，弹出菜单，点击‘呼叫病人’就可以将病人转化为待诊状态。

也可以将处于待诊状态的病人通过按鼠标右键的方法将其转为续诊。

若病人信息为橘黄色，则表明该病人处于待诊状态，可以就诊，双击病人信息，就会将病人的详细信息输出，如图所示：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image026.jpg)

.医生可以根据病人的情况填写相关内容（门诊病历、检查治疗、药品处方）。在填写过程中，输入栏中出现![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image028.gif)图标时，表示可以进行选择，也可以手动输入。如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image030.gif)

使用![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image032.gif)从待选项移到已选项，也可以手动输入进行筛选，然后单击‘确定’。

在输入‘检查治疗’‘药品处方’‘中草药处方’时，单击‘新增’就可以输入相关内容，选择处方内容时，可以输入药品名称汉语拼音的第一个字母按‘回车键’就可以出现相关信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image034.jpg)

完成后单击‘保存’， 

 

### 1.2.2诊断证明

在右边列出的门诊管理子系统的主要模块中找到诊断证明模块图标点击进入。

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image036.jpg)

### 1.2.3个人处方维护

在右边列出的门诊管理子系统的主要模块中找到个人处方模块图标点击进入：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image038.jpg)

个人处方维护所列出的组合名称只能为常用药品、常用检查、常用检验，在此维护的信息将在门诊医生工作站处调用。

### 1.2.4门诊医生接诊患者信息查询

在右边列出的门诊管理子系统的主要模块中找到门诊医生急诊患者信息查询模块图标点击进入。

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image040.jpg)

输入要查询的条件，单击‘查询’即可找到要查询的科室、医师所对应的所有病人信息。

## [1.3 ]()门诊收费站

### 1.3.1门诊划价收费

在左边导航栏中找到门诊管理子系统，右边会列出门诊收费站子系统的主要模块。找到门诊划价收费模块图标点击进入。输入就诊号或挂号号如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image042.jpg)

单击‘确定’，屏幕中会列出该病人所有要划价的费用，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image044.jpg)

单击‘保存’，如图：

 

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image046.jpg)

最后单击确定即可。如果以上信息输入不正确，可以单击‘取消’，重新输入。

### 1.3.2 病人信息维护

在右边的主要模块中找到病人信息维护模块图标点击进入，如图：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image048.jpg)**

根据需要输入相关信息（所有部门、检索条件、是否在院都是可选的），单击回车键或‘查询‘按钮，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image050.jpg)

选中要修改的病人，单击‘编辑’按钮，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image052.jpg)

修改完成后单击‘确定’即可。

### 1.3.3 门诊发票重打

在右边的主要模块中找到门诊发票重打模块图标点击进入，如图：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image054.jpg)**

单击要重新打印的发票或输入要打印的发票号，单击‘确定‘，如图;

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image056.jpg)需要重打的话单击‘确定‘，不需要的话单击‘取消’。

### 1.3.4 门诊退费

在右边的主要模块中找到门诊退费模块图标点击进入，输入诊疗号或发票号，如图：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image058.jpg)**

双击该发票号对应的信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image060.jpg)

选择要退的费用，单击‘保存’

如图：![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image062.jpg) 单击‘确定’即可退费，否则不退费。

### 1.3.5 门诊日结账（在启用日结配置的情况下才能使用）

在右边的主要模块中找到门诊操作员结账模块图标点击进入，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image064.jpg)

根据具体需要进行选择。

### 1.3.6 结账信息取消（该功能慎用）

在右边的主要模块中找到结账信息取消模块图标点击进入，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image066.jpg)

输入要撤销结账的操作员代码，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image068.jpg)

如果要撤销记账的话，选中要撤销的账目，如果需要的话选中删除结账信息单选框，直接单击‘撤销结账数据’，如图：![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image070.jpg)

 

# [住院部分]()

根据系统管理员设置给该操作员的权限来登录系统。若口令不正确，系统将提示错误信息。如果不想进入系统点击取消即可。点击左边的住院管理系统，右边会出现相关模块

## [2.1]()入院

### 2.1.1 入院登记

双击右边的入院登记模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image072.jpg)**

单击‘新增’按钮，输入要住院的病人信息（从‘姓名栏’开始可以用‘回车键’依次输入其他信息），单击保存，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image074.jpg)然后单击‘确定’，出现如下对话框：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image076.jpg)如果要打印病历首页的话，单击‘确定’，否则单击‘取消’。

### 2.1.2 新生儿入院登记

双击右边的新生儿入院登记模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image078.jpg)**

单击‘新增’，输入相关信息（输入母亲的住院号，按‘回车键’进行其他项的选择），完成后单击 ‘保存’，出现如下对话框：![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image080.jpg)

再次输入母亲住院号，单击回车键，会出现对应孩子的信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image082.jpg)

双击该婴儿的信息，就可以对婴儿信息进行编辑。

### 2.1.3 入院登记许可项配置

双击右边的入院登记许可项配置模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image084.jpg)**

对需要的项打上勾，然后单击提交。

## [2.2]()住院医生站

### 2.2.1 导航管理

双击右边的导航管理模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image086.jpg)

 

选中某一病人，单击鼠标右键，会出现长期医嘱、临时医嘱、账目查询、电子病历、床头卡等选项，根据需要进行选择。

例：建立长期医嘱：如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image088.jpg)

单击‘新增’，输入要增加的长期医嘱，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image090.jpg)

输入完成后，单击保存，如图：![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image092.jpg)，保存完成后，要进行‘确认’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image094.jpg)

之后再单击‘保存’，如图

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image096.jpg)

如果确定该遗嘱的话，单击确定即可，否则单击取消，对该医嘱进行‘追加’、‘修改’、‘作废’等操作。对该病人进行长期医嘱时，也可填写临时医嘱。

### 2.2.2 住院电子病历

双击右边的住院电子病历模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image098.jpg)**

用鼠标选中左边的病人信息，右击鼠标，会出现收治患者、病案首页、书写病历、病历套打、会诊管理等选项，根据要求进行选择。

例： ‘病案首页’：（如果登录医生不是该病人的主治医生，那么如果要为该病人书写病案首页时，就要先收治该病人）

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image100.jpg)

填写病人的基本情况、诊断情况、治疗情况，完成后单击‘保存’即可。

 

## [2.3]()住院收费

### 2.3.1 住院划价记账

双击右边的住院划价记账模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image102.jpg)**

可以在对应的科室下面选择病人，也可以输入病人的住院号，按回车键调出病人的相关信息，输入执行科室和收费项目（收费项目可以输入项目的汉语拼音的第一个字母按回车键进行选择），然后单击保存。如果该病人余额不足，如果有“欠费权限的话”可以在‘允许欠费’栏输入金额或者在有担保人的情况下可以填写相关的担保金额。

### 2.3.2 允许欠费金额设置

双击右边的允许欠费金额设置模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image104.jpg)**

输入相关的检索条件，单击‘查询’，就可以调出该病人的信息（用以核对），如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image106.jpg)

然后在设定允许欠费金额文本框中输入允许欠费金额，单击‘设定’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image108.jpg)

表示设定成功。

### 2.3.8 增加担保人

双击右边的增加担保人模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image110.jpg)**

在左边的菜单栏根据科室选择病人或输入病人的住院号，按‘回车键’调出病人的相关信息，然后填写担保人、担保金额等内容。输入完成后，单击‘保存’即可。

### 2.3.3 预交款管理

双击右边的预交款管理模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image112.jpg)**

在左边的菜单栏根据科室选择病人或输入住院号，按回车键，调出病人的相关信息，然后输入缴款金额，单击‘收费’按钮即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image114.jpg)

该页面的下面还会出现该病人的明细账信息，如果要该病人要退费的话，可以选择相关的要退的费用，单击‘退费’按钮即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image116.jpg)

### 2.3.4 预交款发票重打

双击右边的预交款发票重打模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image118.jpg)**

在左边的菜单中根据科室选择病人或输入住院号，按回车键，页面中会出现该病人所有的预交款信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image120.jpg)

选择要重打的发票，单击‘重打’按钮进行重打，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image122.jpg)

单击‘确定’即可重打，否则‘取消’。

### 2.3.5 住院收入按科室统计

双击右边的住院收入按科室统计模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image124.jpg)**

选择要统计的起止时间、科室等信息，单击‘确定’即可。

### 2.3.6 住院收入按医师统计

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image126.jpg)**

选择要统计的起止时间、医师等信息，单击‘确定’即可。

### 2.3.7 病人费用类别修改

双击右边的病人费用类别修改模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image128.jpg)**

输入住院号，按回车键调出病人基本信息，然后输入要修改的病人费用类别信息，单击‘转换’即可。

### 2.3.9 住院退费

双击右边的住院退费模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image130.jpg)

在左边的菜单中根据科室选择病人或输入住院号，按回车键调出病人的相关信息和相关费用，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image132.jpg)

选中需要退的费用，然后单击‘保存’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image134.jpg)

需要退的话，单击‘确定’，否则单击‘取消’。

退费成功后，可以单击‘显示退费’，即可以看到该病人的退费情况。如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image136.jpg)

### 2.3.10 滚动退费

双击右边的滚动退费模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image138.jpg)**

在左边的菜单中根据科室选择病人或输入住院号，按回车键调出病人信息和相关的滚动费用，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image140.jpg)

选择要退的滚动费用，单击保存即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image142.jpg)

### 2.3.11 病人账目查询

双击右边的病人账目查询模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image144.jpg)**

输入病人的住院号，按回车键，调出病人的相关信息。如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image146.jpg)

可以通过按‘总账信息’，‘明细账信息’，‘未发药品信息’，‘未记账医嘱’按钮得出相关信息。

###   2.3.12 病人账目结算

双击右边的病人账目结算模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image148.jpg)

输入病人的住院号，按回车键调出病人信息，（如果该病人有未发药品，系统会出现如下对话框：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image150.jpg)

发药动作完成后，就可以正常结算，单击‘确定’，会出现“确定现在办理患者结算手续吗”的对话框，单击‘确定’即可办理结算手续，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image152.jpg)

 

。

###   2.3.13 结算召回

  双击右边的结算召回模块进入如图界面：

  ![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image154.jpg)

在左边的菜单中根据科室选择结算的病人信息或输入要召回的病人的住院号，单击‘查询’，如果确定要召回该病人的话就单击‘确定’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image156.jpg)

###   2.3.14 住院结账

双击右边的住院结账模块进入如图界面：

 

###   2.3.15 催拖欠款

  双击右边的催拖欠款模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image158.jpg)

以上即为所有欠款病人的信息。

###   2.3.16出院发票重打

双击右边的出发票重打模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image160.jpg)

左边的菜单会列出相关的出院病人信息，选择出院病人或输入住院号，按回车键调出出院的病人信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image162.jpg)

单击‘确定’即可完成发票重打。

###   2.3.17 出院收入按医师统计

  双击右边的出院收入按医师统计模块进入如图界面：

 ![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image164.jpg)

输入要统计的信息，按‘确定’即可。

###   2.3.18出院收入按科室统计

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image166.jpg)

输入要统计的信息，按‘确定’即可。

### 2.3.19出院病人账目查询

双击右边的出院病人账目查询模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image168.jpg)**

输入要查询的病人的住院号，按‘回车键’调出病人的相关信息，通过按‘总账信息’，‘明细账信息’查出对应的账目信息。

## [2.4]()护士站

###   2.4.1 导航管理

**  **双击右边的导航管理模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image170.jpg)**

选择要执行医嘱的病人信息，按鼠标左键，会出现长期医嘱、临时医嘱、床位调整、账目查询、出院结算等选项卡。以长期医嘱为例，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image172.jpg)

如果该护士对该病人执行了以上操作，就可以单击‘查对’，如果病人余额不足，就可以选择部分查对，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image174.jpg)

###   2.4.2 长期医嘱单

双击右边的长期医嘱单模块进入如图界面：

**![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image176.jpg)**

输入已经执行的长期医嘱的病人的住院号，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image178.jpg)

单击确定，就可以看到该病人的长期医嘱单。

###   2.4.3 临时医嘱单

临时医嘱单的操作同长期医嘱单。

###   2.4.4 转床管理

双击右边的转床管理模块进入如图界面；

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image180.jpg)

输入要转床的病人的住院号按回车键就会调出病人的原床位号，然后输入病人要转入的现床位号，单击保存即可。

###   2.4.5 包床设置

双击右边的包床设置模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image182.jpg)

输入住院号，调出相关信息，按‘包床’键即可

###   2.4.6 临时加床

双击临时加床模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image184.jpg)

选择科室名，对该科室按照提示进行临时加床。

###   2.4.7 临时撤床

双击右边的临时撤床模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image186.jpg)

选择科室名，对该科室按照提示进行临时撤床。

### 2.4.8 转科管理

双击右边的转科管理模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image188.jpg)

### 2.4.9 在院病人一日清单

双击右边的在院病人一日清单模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image190.jpg)

选择要统计的方式（按住院号还是按科室），修改要统计的起止时间，按‘确定’键即可。

### 2.5.10 出院病人一日清单

双击右边的出院病人一日清单模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image192.jpg)

出院病人一日清单操作同在院病人一日清单

### 2.5.11 请领药单（汇总）

双击右边的请领药单（汇总）模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image194.jpg)

如果是住院药房的话选择住院药房，起始时间为上次打印或预览的时间，终止时间为当前时间，根据需要进行选择（重打（注：要输入申请号）、预览、打印），单击‘确定’即可。

### 2.5.12 请领药单（明细）

双击右边的请领药单（明细）模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image196.jpg)

选择医嘱类型和药品类别，其他操作同请领药单（汇总）

### 2.5.13 静滴单（输液卡）

双击右边的静滴单（输液卡）模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image198.jpg)

输入住院号，选择要打印或预览的静滴单的起止时间，单击确定即可，如果要按科室查询的话，就选择‘是否按科室查询’单选框，单击确定即可。

### 2.5.14 请领药单（口服和非口服）

双击右边的请领药单（口服和非口服）模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image200.jpg)

操作同请领药单（汇总）。

### 2.5.15 口服药单

双击右边的口服药单模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image202.jpg)

基本操作同静滴单

### 2.5.16 注射单

双击右边的注射单模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image204.jpg)

操作同口服药单。

### 2.5.17 处置单（医技）

双击右边的处置单模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image206.jpg)

操作同上。

### 2.5.18 治疗处方单

双击右边的治疗处方单模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image208.jpg)

 

### 2.5.19 皮试结果查询

双击右边的皮试结果查询模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image210.jpg)

输入住院号就会出现做过皮试的病人的皮试结果信息查询。

### 2.5.20 护理记录单

双击右边的护理记录单模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image212.jpg)

记录护士在护理过程中病人的基本信息。

## [2.5]()出院

###   2.5.1 出院

  双击右边的出院模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image214.jpg)

左边菜单会出现相关的可以出院的病人或输入住院号，按回车键，单击‘确定’如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image216.jpg)

要办理出院的话，直接单击‘确定’即可。否则单击‘取消’。

### 2.5.2 出院病人召回管理

双击右边的出院病人召回管理模块进入如图界面：****

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image218.jpg)

输入要召回的病人的住院号，按‘回车键’调出病人的信息，单击‘确定’即可。

# [药库药房部分]()

## [3.1 ]()药库管理

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image220.jpg)

### 3.1.1. 药品初始录入

双击右边的药品初始录入模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image222.jpg)

选择药房名称、库房名称等信息（输入药品名称的时可以输入药品名称的汉语拼音的首字母，按回车键，选择相关药品）。输入完成后，单击‘保存’即可。

### 3.1.2药品入库

双击右边的药品入库模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image224.jpg)

点击‘新增’，输入入库的药品信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image226.jpg)

单击‘保存’即可。

### 3.1.3 药品库存调整

双击右边的药品库存调整模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image228.jpg)

输入调整部门，点击‘显示’，屏幕中会列出该药房的所有药品信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image230.jpg)

也可以在按拼音码检索中输入药品的汉语拼音的首字母，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image232.jpg)

选中要调整的药品，，输入实际库存和调整原因，单击‘确定’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image234.jpg)

点击‘调整’即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image236.jpg)

### 3.1.4 药品调拨管理

双击右边的药品调拨管理模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image238.jpg)

单击‘新增’，输入要调拨的库房名称和药品，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image240.jpg)

单击‘保存’即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image242.jpg)

也可以对已经调拨的药品进行编辑，单击‘编辑’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image244.jpg)

选择要编辑的药品，进行‘编辑’即可。

保存完成后，要对药品信息要进行审核，单击‘审核’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image246.jpg)

选中要审核的药品信息，双击即可。

### 3.1.5 药品盘点

双击右边的药品盘点模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image248.jpg)

单击‘新增盘点表’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image250.jpg)

如果要显示这些药品对应的库存，可以单击‘导入库存’，盘点完成后，单击‘保存’，数据无误后，单击‘审核’、‘保存’，药品盘点任务完成。

### 3.1.6药品库存上下限维护

双击右边的药品库存上下限维护模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image252.jpg)

输入库存下限和库存上限，单击‘保存’即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image254.jpg)

### 3.1.7药品调价

双击右边的药品调价模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image256.jpg)

单击‘新增’，输入相关信息（文件号、调价原因必写），输入药品的新购入价如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image258.jpg)

单击保存即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image260.jpg)

保存之后，要对修改过的信息进行审核，点击‘审核’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image262.jpg)

选择部门，单击‘查询’，选择要审核的调价号，单击‘确定’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image264.jpg)

选中要审核的药品信息，单击‘确定’即可。

### 3.1.8药品报损管理

双击右边的药品报损管理模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image266.jpg)

单击‘新增’，选择损益类别，输入报损的药品数量，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image268.jpg)

单击‘保存’即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image270.jpg)

保存完成后，要对输入的信息进行审核，单击‘审核’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image272.jpg)

双击要审核报损信息或者选择该审核报损信息单击‘确定’，再次单击‘保存’即可审计成功，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image274.jpg)

单击‘确定’；

 

### 3.1.9药品购物计划

双击右边的药品购物计划模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image276.jpg)

单击‘新增’，输入相关信息，即可出现该库房的药品记录信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image278.jpg)

如果只想显示小于库存下限的药品信息，可以在药品信息（仅显示小于库存下限的药品）前面方框点击即可。也可以利用辅助查找（输入药品汉语拼音的第一个字母）来查找有关药品信息。

选中某一药品，右击，会出现‘定位查找’，‘添加到购物计划明细’对话框。

定位查找，如图：点击‘ok’进行查找。

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image280.jpg)

也可以利用单价进行查找。

添加到购物计划明细，输入采购数量和购入价等相关信息，单击‘保存’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image282.jpg)

确定信息无误后，单击‘审核’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image284.jpg)

选中要审核的药品，单击‘保存’即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image286.jpg)

完成购物计划后，点击‘保存’即可。会出现相关的审核单；

### 3.1.10药品退货管理

双击右边的药品退货管理模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image288.jpg)

单击‘新增’，输入相关信息和要退的药品信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image290.jpg)

单击‘保存’即可。保存完成后，也要对该信息进行审核。

### 3.1.11 药库药品出入库汇总

双击右边的药库药品出入库汇总模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image292.jpg)

选择要统计的药库名称、历史数据、汇总数据的时间，单击‘汇总’即可。

### 3.1.12 药房药品出入库汇总

双击右边的药房药品出入库汇总模块进入如图界面：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image294.jpg)

选择要统计的药库名称、历史数据、汇总数据的时间，单击‘汇总’即可。

## [3.2]()药房

药房操作员根据自己的代码进入药房管理系统，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image296.jpg)

### 3.2.1药房发药

双击右边的药房发药模块，如图所示：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image298.jpg)

确定是门诊发药还是住院发药，选择药方名称，系统会调出相关的未发药的病人信息，如果病人过多的话，也可以通过输入诊疗号按回车键调出该病人的未发药的信息。如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image300.jpg)

发完药后，点击‘确定’即可，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image302.jpg)

点击‘确定’之后，会打印相应的发药单；

### 3.2.2药房退药

双击右边的药房发药模块，如图所示：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image304.jpg)

选择退药的部门，单击‘查询’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image306.jpg)

选择要退药的病人信息，单击‘确定’，然后双击该病人信息，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image308.jpg)

选择要退的药品，单击‘确定’，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image310.jpg)

单击是，即可完成退药，否则单击‘否’。

### 3.2.3 药品有效期管理

双击右边的药品有效期管理模块，如图所示：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image312.jpg)

根据提示，输入要查询的药品的有效期，单击‘查询’即可。

### 3.2. 4药房药品维护

双击右边的药房药品维护模块，如图所示：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image314.jpg)

输入库存上下限，单击‘保存’即可。

### 3.2.5药品医保类型维护

双击右边的药品医保类型维护模块，如图所示：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image316.jpg)

输入要将修改的药品的汉语拼音的首字母或药品编号、药品名称等相关信息，调出该药品的信息，选择‘修改’按钮，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image318.jpg)

选择该药品的医保类型，最后‘保存’即可。

### 3.2. 6药品划价

双击右边的药品划价模块，如图所示：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image320.jpg)

左边列出了已挂号未划价的病人信息，对该病人信息进行双击，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image322.jpg)

输入处方的药品名称，如图：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image324.jpg)

单击保存即可：

![img](http://ovi3ob9p4.bkt.clouddn.com/yxmgr/clip_image326.jpg)

 
