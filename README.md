# 基于GitHub Actions的微信自动化推送平台
工匠技能月之校园信息化管理平台设计源代码仓库

**目前实现：**

**1、每日推送天气的时候顺便推送当天的课程；**

**2、如果下一节有课，在上课前推送提醒；**

**3、每日晚安心语及第二天课程推送。**

不准备实现的功能：



## 实现原理：
​	最开始只有每天的天气推送（每天只需要定时推送一次就好），实现很简单，利用GitHub Actions创建一个定时的工作流就行。
​	增加需求后，最开始的想法不变，利用GitHub Actions创建工作流多跑项目，让程序一直执行，直到“晚安心语”推送完后就结束。但是有个**GitHub Actions有两个致命的限制**：1、一个月能够执行项目的总时常为2000分钟，程序一直执行很快就会花光时间！  2、假如你设置的每日推送时间是  7:40  , 由于GitHub Actions是排队执行，如果是高峰期会导致项目延迟执行（一般延迟20-40分钟），所以第二点直接否定了我们想要准时的需求。
**值得注意的是，如果我们手动触发GitHub Actions里面的工作流，则是实时执行（本实例部署时间一般是50s左右）。所以，问题转变，通过用腾讯云函数的定时功能来触发GitHub Actions里面的工作流文件，达到曲线救国！**
​	利用腾讯云函数定时触发的功能，只需要在程序设置的每日提醒、每节课上课提醒、每日晚安提醒时间的前两分钟触发Actions里面的工作流文件就能**完美解决GitHub Actions时间限制，和定时延迟的弊端。**

## 项目依赖：
#### 1、GitHub[（https://github.com/）](https://github.com/)

​	GitHub是一个由美国公司GitHub，Inc.开发的基于Git的版本控制和协作工具。它是世界上最大的代码托管平台之一，提供免费的代码仓库供开发者上传、存储和分享自己的代码，同时支持协作和版本控制，可以帮助开发团队高效协作、追踪项目进度和解决问题。在本例中，我们主要利用Github提供的工作流托管项目云端运行。

#### 2、微信公众平台[（https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login）](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login)

​	微信公众平台是指微信推出的一套公众号运营工具，是企业或个人在微信平台上开设、推广和管理公众号的一站式服务平台。公众号开发者可以通过微信公众平台进行账户的注册、设置、推广、内容管理和数据分析等操作。同时，微信公众平台也提供了一系列API来帮助开发者更方便快捷地进行公众号开发。在本例中，我们主要利用微信公众平台提供的测试号进行消息的推送。

#### 3、腾讯云函数[（https://cloud.tencent.com/）](https://cloud.tencent.com/)

​	腾讯云函数是一项无服务器计算服务，它允许开发者以代码的形式编写并在云中运行简单的、独立的函数。使用云函数，开发者可以在不需管理服务器和运维的情况下，快速构建和迭代应用程序。腾讯云函数支持多种语言(如Node.js、Python、Java等)，用户可以在几分钟内创建函数并获得一个独立的HTTP URL来触发执行，使用流量即付的计费模式，在函数规模自动上升的情况下，仅需按实际使用量支付费用。在本例中，我们主要利用腾讯云函数提供的定时触发服务，触发函数定时执行。

##### 4、天行数据（用于获取晚安心语内容）[（https://www.tianapi.com/）](https://www.tianapi.com/)

​	天行数据是一个提供各类 API 接口的数据服务提供商，涵盖了多种数据类型和行业，如新闻、股票、天气、影视等领域，可以为开发者提供定制化的数据服务。用户可以通过向天行数据提交请求，获取到对应 API 接口返回的数据结果，以便进行二次加工处理或直接应用于自己的项目中。在本实例中，我们使用了天行数据提供的“晚安心语”API，通过账户的方式获取到了每天的晚安心语内容并加以利用。

## 二、实现效果图
### 1、每日提醒
![每日提醒](https://img-blog.csdnimg.cn/7f2df556857842dca595013519408b5a.png)

### 2、上课提醒
![上课提醒](https://img-blog.csdnimg.cn/3b3023e6648847b4806d5ff38fc1c3ee.png)
### 3、晚安心语及第二天课程提醒
![晚安心语](https://img-blog.csdnimg.cn/d0e681648887499da7d3330e04cb31ad.png)
# 三、步骤
## 1、拉取GitHub项目
将仓库里面的项目fork到自己仓库
GitHub项目地址：[https://github.com/ghwmx/WeiXinPost](https://github.com/ghwmx/WeiXinPost)
## 2、更改项目中的配置文件：config.py
![更改配置文件](https://img-blog.csdnimg.cn/77dadb34a9544377a05539848eedc106.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/899f69fdb93d45019b599f55a81a6370.png)
## 3、微信公众平台相关配置，登录微信公众平台，免费注册接口测试公众号
复制**appID** 和 **appsecret** 填入**config.py** 对应位置
### ①
![在这里插入图片描述](https://img-blog.csdnimg.cn/e2e9cf548a904ea484e0b692f5305985.png)
### ②复制**appID** 和 **appsecret** 
![在这里插入图片描述](https://img-blog.csdnimg.cn/352eeed9201a4f11bfa04985e455a22a.png)
### ③填入**config.py** 对应位置
![在这里插入图片描述](https://img-blog.csdnimg.cn/421e0c67cbb54ce7b591101fc6f3219e.png)
**注意要填写在双引号里面**
### ④复制 config.py 文件最下面的模板，分别添加到微信公众平台
解释：模板中{{***}}以外的文字是固定显示，如图第90行代码，“今天是破壳日的第：{{...}} 天”，这句话对应程序是一个计时器，可以更改为：今天是和。。。恋爱的第{{....}} 天、今天是。。。。等等，根据自己需求更改。
同理，“距离开学还有：{{....}} 天” ，是一个倒计时，可以更改为生日等等，生日暂时只支持阳历，农历可以根据自己需求更改主程序。
**复制的时候记得去除每行前面的 “#”**，可以先复制到txt文档里面整理好后再添加。
![在这里插入图片描述](https://img-blog.csdnimg.cn/75c02fdc69eb4b089aaf6fc37296cdca.png)
#### 复制模板 1并添加：
**在微信公众平台，往下找到“模板消息接口”---->新增测试模板---->模板标题（就是微信上看到的标题）---->模板内容为刚才复制的内容----->提交**
![在这里插入图片描述](https://img-blog.csdnimg.cn/9e026ddaf8754a2b85d0400ca1730ade.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/04463305f71b4b738b6fae3683d8798a.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/7372c9acbd8f4d15a5787d0fc673daf6.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/7f85a50908914ec8803b2fbaf4b71215.png)
### ⑤复制模板ID，填到config.py的  template_id1
### ⑥后面的上课提醒模板 和  晚安心语模板同理
![在这里插入图片描述](https://img-blog.csdnimg.cn/f48204ff813740e09b4cdb52a437a773.png)
### ⑦扫描测试二维码，关注公众号，关注后复制微信号，填入config.py中的user
**注意：需要填写到双引号里面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/e6386e37d2e549e3a5074c55a2d2452a.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/6fa2e969a8694103915045f27ff5e800.png)
### ⑧保存修改
![在这里插入图片描述](https://img-blog.csdnimg.cn/6d408d75eb2f4a04aa2f0b044e8d982d.png)

**至此，微信公众平台配置完成！**
## 4、配置GitHub Actions
### ①打开actions工作流文件模板.yml，并复制里面所有内容
![在这里插入图片描述](https://img-blog.csdnimg.cn/5b56b08e5e984fe18e86c4ae12b78b15.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/9cf465acaaac41e8a726de7c1820a07f.png)

### ②点击Actions，配置工作流文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/b7401b5e6c3e47a79b88c4adf1e680e6.png)
**选择  set up a workflow yourself**
![在这里插入图片描述](https://img-blog.csdnimg.cn/21b5f6a573424d398bcbb5397c681509.png)
**删除所有内容，并将复制的内容粘贴到里面，保存**
![在这里插入图片描述](https://img-blog.csdnimg.cn/99de4a7fa5234ebdbd8697a15a3a79e7.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/546ea44e99eb4dedb932e57b7fe5b760.png)
**点击Actions，会出现刚才新建的工作流文件**
![在这里插入图片描述](https://img-blog.csdnimg.cn/f63c7cae696b4dbca8778bc11e707c81.png)
**测试工作流程是否正确**
![在这里插入图片描述](https://img-blog.csdnimg.cn/4387dd1907834dd0b406686dba56cc5f.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/a3bacc4779724a34816d2dcbfbeb8a6f.png)
*若运行失败，点击进去，查看运行过程中产生的错误
![在这里插入图片描述](https://img-blog.csdnimg.cn/def49ade63ef48e98e8f6b2b638f42fe.png)
*定位问题出现的原因，是环境配置不正确，还是程序本身的问题。以下问题是程序 main.py 第79行的函数：get_Today_Class运行时发生错误。原因：没有配置开学时间
![在这里插入图片描述](https://img-blog.csdnimg.cn/6cdeeeed62974a17916a2da5cf60c039.png)

### ③获取GitHub Token为后续腾讯云函数配置做准备
**点击个人设置**
![在这里插入图片描述](https://img-blog.csdnimg.cn/aaacc6175b9a4c3bbce0312c89a87673.png)

**滑动到最下面，选择‘开发者设置’**
![在这里插入图片描述](https://img-blog.csdnimg.cn/ab05553af8c34143bc061ddcdb7786aa.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/66b15b7e41b34ef8b02c8c945f197c55.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/a307345152774ba0851a33e6d19c7ea5.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/79815fb026db4598bf91140ef4e0b4f4.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/a091db7a91ae43e695f81118a050e921.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/8d333c14ffad49ad843e42c1872d5cd5.png)
**至此，github配置完成！**


## 5、配置腾讯云函数
### ①登录后搜索‘云函数’
![在这里插入图片描述](https://img-blog.csdnimg.cn/b59ebfb8ebf64be7bb488e70cab931d8.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/a71c04226e344ec19b5f4ed91a9e6ce3.png)

**接下来会有一些身份验证**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2ed424cbba804b60bf35e75a21ba85a4.png)

### ②完成相关认证后，选择‘函数服务’，‘新建’
![在这里插入图片描述](https://img-blog.csdnimg.cn/50621e2d1d9e4d1bae597149157c08af.png)

**选择‘从头开始’，函数的名字随意，运行环境选择‘Python3.6’**
![在这里插入图片描述](https://img-blog.csdnimg.cn/1a03642b5e7d4973a73874ac475b4f67.png)

**接下来更改函数体中的内容，打开GitHub中的  ‘txPost.py’  复制所有内容**
![在这里插入图片描述](https://img-blog.csdnimg.cn/a09c30f460184b7d8e86e7907afb936a.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/ff8247ee068b42edb81104ef439c5b5c.png)

**粘贴到窗口中，替换其中的token，用户名等信息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/65418e1268e64e3ea2cce9f0116e8898.png)

**用户名/项目名  如图所示**
![在这里插入图片描述](https://img-blog.csdnimg.cn/23e987313d4b40fe98e3f27599a9a86f.png)

**其余设置为默认**
![在这里插入图片描述](https://img-blog.csdnimg.cn/c3fac1d1578e466d9a6164651b5261e8.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/4f3d605f4cc44555b2af87051278a06d.png)


### ③创建触发函数的定时触发器，按图操作即可![在这里插入图片描述](https://img-blog.csdnimg.cn/99640eb365754daba4293b94cf6b06b0.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/afce3b368f1547a5bb14dcc9dcdc45ed.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/25c7e513c7204e78a498f65f709d3cd6.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/6ed439a15de543ee85d1506cb278801d.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/76c5266b966d49499412b988b4181245.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/03589be1b4ad4f2a848934c42478ea84.png)

**恭喜，你已经成功完成所有配置！**

## 结束语
​	因为本次的校园信息化平台建设活动，有幸接触到了微信公众号部署以及实现，完成了初中的自动化推送的设想，在此过程中也是边学习边收获，受益满满！谢谢你看到这里！
