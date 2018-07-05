##第一部分 走进java
####Author：Sirice

###一、java技术体系

传统意义来看java分为以下几个组成部分：
- java程序设计语言
- 各种硬件平台的java虚拟机
- Class文件格式
- Java API类库
- 商业机构和第三方java类库

其中，前三点统称JDK（Java Development Kit）

java API类库中的java SE和java虚拟机称为JRE（Java Runtime Enviornment）

java技术体系可以分为4个平台
- Java Card (智能卡平台，嵌入式applets)
- Java ME(Micro Edition)
- Java SE(Standard Edition)
- Java EE(Enterprise Edition)

###二、java发展历史
<table>
<thead>
<tr>
  <th>版本</th>
  <th align="center">描述</th>
</tr>
</thead>
<tbody><tr>
  <td>1991年1月</td>
  <td align="center">Sun公司成立了Green项目小组,专攻智能家电的嵌入式控制系统</td>
</tr>
<tr>
  <td>1991年2月</td>
  <td align="center">放弃C++,开发新语言，命名为“Oak”</td>
</tr>
<tr>
  <td>1991年6月</td>
  <td align="center">JamesGosling开发了Oak的解释器</td>
</tr>
<tr>
  <td>1992年1月</td>
  <td align="center">Green完成了Green操作系统、Oak语言、类库等开发</td>
</tr>
<tr>
  <td>1992年11月</td>
  <td align="center">Green计划转化成“FirstPerson”，一个Sun公司的全资母公司</td>
</tr>
<tr>
  <td>1993年2月</td>
  <td align="center">获得时代华纳的电视机顶盒交互系统的订单，于是开发的重心从家庭消费电子产品转到了电视盒机顶盒的相关平台上。</td>
</tr>
<tr>
  <td>1994年6月</td>
  <td align="center">FirstPerson公司倒闭，员工都合并到Sun公司。Liveoak计划启动了，目标是使用Oak语言设计出一个操作系统。</td>
</tr>
<tr>
  <td>1994年7月</td>
  <td align="center">第一个Java语言的Web浏览器WebRunner（后来改名为HotJava），Oak更名为Java。</td>
</tr>
<tr>
  <td>1994年10月</td>
  <td align="center">VanHoff编写的Java编译器用于Java语言</td>
</tr>
<tr>
  <td>1995年3月</td>
  <td align="center">在SunWorld大会，Sun公司正式介绍了Java和HotJava。</td>
</tr>
<tr>
  <td>1996年1月</td>
  <td align="center">JDK1.0发布</td>
</tr>
<tr>
  <td>1997年2月</td>
  <td align="center">J2SE1.1发布</td>
</tr>
<tr>
  <td>1998年12月</td>
  <td align="center">J2SE1.2发布</td>
</tr>
<tr>
  <td>1999年6月</td>
  <td align="center">发布Java的三个版本：J2SE、J2EE、J2ME</td>
</tr>
<tr>
  <td>2000年5月</td>
  <td align="center">J2SE1.3发布</td>
</tr>
<tr>
  <td>2001年9月</td>
  <td align="center">J2EE1.3发布</td>
</tr>
<tr>
  <td>2002年2月</td>
  <td align="center">J2SE1.4发布</td>
</tr>
<tr>
  <td>2004年9月</td>
  <td align="center">J2SE1.5发布，将J2SE1.5改名JavaSE5.0</td>
</tr>
<tr>
  <td>2005年6月</td>
  <td align="center">JavaSE6.0发布,J2EE更名为JavaEE，J2SE更名为JavaSE，J2ME更名为JavaME</td>
</tr>
<tr>
  <td>2006年12月</td>
  <td align="center">JRE6.0发布</td>
</tr>
<tr>
  <td>2006年12月</td>
  <td align="center">JavaSE6发布</td>
</tr>
<tr>
  <td>2009年12月</td>
  <td align="center">JavaEE6发布</td>
</tr>
<tr>
  <td>2009年4月</td>
  <td align="center">Oracle收购Sun</td>
</tr>
<tr>
  <td>2011年7月</td>
  <td align="center">JavaSE7发布</td>
</tr>
<tr>
  <td>2014年3月</td>
  <td align="center">JavaSE8发布</td>
</tr>
</tbody></table>

###三、jvm发展历史
1.Sun Classic / Exact VM - 世界第一款商用虚拟机

1996年1月，Sun公司发布JDK1.0，所含的就是这款虚拟机。这个虚拟机只能用解释器的方式执行java代码，但是可以外挂JIT编译器，然而编译器和解释器不能协同工作，“java语言很慢”的印象就是这时候在用户心中树立的。
Exact VM是Solaris平台上的初具高性能虚拟机的雏形，有两级即时编译器，编译器解释器协同工作模式，然而很快被HotSpot取代。

2.Sun HotSpot VM - 使用范围最广的java虚拟机

HotSpot VM继承了上面两款虚拟机的优点，也有自己的特有优势——热点代码探测技术，可以通过执行计数器找出最具有编译价值的代码然后通知jit以方法为单位进行编译。多次调用的方法可以触发标准编译和栈上替换等优化。

3.IBM V9/BEA JRockit - 最著名的第三方JVM

BEA JRockit号称"世界上速度最快的java虚拟机"（有点罗永浩广告词的意思哈）不关注程序启动速度，因此这款产品没有解析器，全部代码都靠即时编译器编译执行，在众多虚拟机中处于领先水平。
IBM V9是一个设计上想从服务器到桌面应用再到嵌入式全面考虑的虚拟机，开发目的是作为IBM的各种java产品执行平台。

4.MicroSoft JVM - 微软唯一指定win平台JVM

这个历史不说不快，微软当时为了在IE3中支持Applets而开发了jvm，只有windows平台下的虚拟机，却是当时win平台性能最好的虚拟机（巨硬的技术实力可见一斑）在1997、1998连续两年获得《PC magazine》的编辑选择奖，然而97年10月，Sun以侵犯商标、不正当竞争等罪名控告微软，这个虚拟机被作为有力证据送到法庭，这个官司的结果是微软赔偿2000万刀给Sun（最终微软因垄断赔偿给Sun的总金额达到10亿刀！）承诺终止java jvm的开发，并在win平台上移除相关功能。在XP SP3时代java jvm被完全抹去的时候，Sun公司到处登报纸希望微软不要这样做，并号召消费者“要求微软在Windows XP中包括Java平台”，当时采访WinXP高级经理Jim Cullinan称“我们用了3年时间和Sun打官司，现在我们这样做了他们却又抱怨”

（Sun公司：**真香！！！**）

（微软：MMP，IE赔了这么多，IE内嵌的Java Applets又tm赔这么一大笔，嘤嘤嘤）


###四、java未来发展

1、模块化

2、混合语言

主要是结合脚本语言，展示层用JRuby等，中间层是Java，每个应用层都有不同的编程语言，最终运行在同一个虚拟机上（理想总是美好的，其他公司不得告你垄断……仅从技术上来说的确会解决很多问题，省去很多开发成本，API重用性可以提高）

3、多核并行

4、丰富语法

自动装箱、lambda表达式等等这是已经实现的，还没实现的可以畅想一下

5、64位虚拟机

====第一部分结束====
