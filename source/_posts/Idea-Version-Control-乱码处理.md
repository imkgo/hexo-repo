title: Idea Version Control 乱码处理
date: 2016-06-28 16:01:34
tags: [Idea]
---
# 问题描述
原来在使用idea开发时没有使用idea自带的version control工具，后来使用了一下感觉还不错，也挺方便的。
在使用的时候发现version control log里面有乱码，如图：

![images](http://imkgo.github.io/img/idea/idea1.png)
# 问题分析
首先想到的就是由于设置的字体导致的，字体不支持汉字？找遍了idea里面所有字体设置的地方，都是可以支持汉字的。所以我先放弃了从字体上去找原因。后来我想是不是因为我用的idea主题有问题，于是我把idea的主题切换回了自带的黑色主题。结果在去看的时候已经没有乱码了。这时候显示的字体是Monospaced，当我把字体切换回Consolas(这是我觉得看着最舒服的字体)，version control的乱码又出现了。于是我确定乱码还是跟字体有关系。

在很长的一段时间内，我一直切换字体，找到几个不会产生乱码的字体，但看起来始终不是太习惯，于是乎我有切换回Consolas字体，我只能忍受version control的乱码了。

我是一个有一点强迫症的人，虽然那里的乱码对于编码工作来说没有任何影响，显示的内容可以完全无视，但是我始终觉得这个问题是可以解决的。于是我又开始了在各个不同的字体之间进行着反复的切换，当我切换到有一个字体叫InputDialog的时候，终于在version control不在显示乱码了。我想知道这个叫InputDialog的字体有什么特点，为什么不会是乱码呢?上网去搜了一下，真的找到了我想要的答案，大体上是说，InputDialog不是一个实际存在的字体，是一个字体的映射，至于映射到哪种字体上，是有一个配置文件的，配置文件的路径在jre/lib/fontconfig.properties.src。文件内容截取关键部分:

```
dialoginput.plain.alphabetic=Courier New
dialoginput.plain.chinese-ms950=MingLiU
dialoginput.plain.chinese-ms950-extb=MingLiU-ExtB
dialoginput.plain.hebrew=David
dialoginput.plain.japanese=MS Gothic
dialoginput.plain.korean=Gulim

dialoginput.bold.alphabetic=Courier New Bold
dialoginput.bold.chinese-ms950=PMingLiU
dialoginput.bold.chinese-ms950-extb=PMingLiU-ExtB
dialoginput.bold.hebrew=David Bold
dialoginput.bold.japanese=MS Gothic
dialoginput.bold.korean=Gulim

dialoginput.italic.alphabetic=Courier New Italic
dialoginput.italic.chinese-ms950=PMingLiU
dialoginput.italic.chinese-ms950-extb=PMingLiU-ExtB
dialoginput.italic.hebrew=David
dialoginput.italic.japanese=MS Gothic
dialoginput.italic.korean=Gulim

dialoginput.bolditalic.alphabetic=Courier New Bold Italic
dialoginput.bolditalic.chinese-ms950=PMingLiU
dialoginput.bolditalic.chinese-ms950-extb=PMingLiU-ExtB
dialoginput.bolditalic.hebrew=David Bold
dialoginput.bolditalic.japanese=MS Gothic
dialoginput.bolditalic.korean=Gulim
```

# 问题处理

修改该配置的方法是复制fontconfig.properties.src文件,另存为fontconfig.properties，然后在fontconfig.properties文件中修改字体映射。我修改后的内容如下：
```
dialoginput.plain.alphabetic=Consolas
dialoginput.plain.chinese-ms950=MingLiU
dialoginput.plain.chinese-ms950-extb=MingLiU-ExtB
dialoginput.plain.hebrew=David
dialoginput.plain.japanese=MS Gothic
dialoginput.plain.korean=Gulim

dialoginput.bold.alphabetic=Consolas Bold
dialoginput.bold.chinese-ms950=PMingLiU
dialoginput.bold.chinese-ms950-extb=PMingLiU-ExtB
dialoginput.bold.hebrew=David Bold
dialoginput.bold.japanese=MS Gothic
dialoginput.bold.korean=Gulim

dialoginput.italic.alphabetic=Consolas Italic
dialoginput.italic.chinese-ms950=PMingLiU
dialoginput.italic.chinese-ms950-extb=PMingLiU-ExtB
dialoginput.italic.hebrew=David
dialoginput.italic.japanese=MS Gothic
dialoginput.italic.korean=Gulim

dialoginput.bolditalic.alphabetic=Consolas Bold Italic
dialoginput.bolditalic.chinese-ms950=PMingLiU
dialoginput.bolditalic.chinese-ms950-extb=PMingLiU-ExtB
dialoginput.bolditalic.hebrew=David Bold
dialoginput.bolditalic.japanese=MS Gothic
dialoginput.bolditalic.korean=Gulim
```
另外在配置文件最后增加新映射字体的实际路径配置：
```
filename.Consolas=consola.ttf
filename.Consolas_Bold=consolab.ttf
filename.Consolas_Italic=consolai.ttf
filename.Consolas_Bold_Italic=consolaz.ttf
```

重启一下idea，然后version control就不会是乱码了，编辑器的字体也是Consolas字体了。
