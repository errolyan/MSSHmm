---
title: Mandarin_singing_voice_with_Hmm
tags: 深度学习,机器学习,数据挖掘,
grammar_mindmap: true
renderNumberedHeading: true
grammar_code: true
---
```
Author : EroolYan(wechat: 13075851954   QQ:260187357 Email:2681506@gmail.com)    WriterID:缠中说禅
Describe: "音源训练方法 "
Date: 2018-11-27
```
[toc!?direction=lr]
# Mandarin_singing_voice_with_Hmm
- 问题以及解决办法：http://hts.sp.nitech.ac.jp/?Mailing%20List
- [邮件列表http://hts.sp.nitech.ac.jp/hts-users/spool/2017/msg00048.html]Wavesurfer (http://www.speech.kth.se/wavesurfer/) can load HTK style .lab files so if you have those you can use that.
## 1.HTK安装方法

HTK:始于剑桥大学工程系，后被微软收购。版权属于微软的。一、下载安装包 下载两个安装包（下载链接）分别是HTK-3.5.beta-2.tar.gz以及HDecode-3.5.beta-1.tar.gz二、安装所需要的依赖库1\. sudo apt-get install libx11-dev:i3862\. sudo apt-get install libc6-dev:i386附：{sudo apt-get install libx11-dev:i386 libx11-devsudo apt-get install g++-multilibsudo apt-get install osspd}三、解压编译安装 1、解压tar xzf HTK-3.5.beta-2.tar.gztar xzf HDecode-3.5.beta-1.tar.gz2、编译 1> HTK 3.5带有三套Makefiles（在这里以 CPU (standard)为例），并且支持编译。2> 在解压后的htk文件夹中分别查看HTKLib，HLMLib，HTKTools，HLMTools检查每个目录中的MakefileCPU文件，并更改CPU / GPU编译器和库的默认位置，以匹配我们将安装的HTK 3.5（可以不更改，我没有更改）。3> 依次在HTKLib，HLMLib，HTKTools和HLMTools中编译MakefileCPU文件make -f MakefileCPU install4> 进入 HTKLVRec文件夹下，执行以下命令1\. make -f MakefileCPU all2\. make -f MakefileCPU install四、将bin.cpu添加到环境变量当中去1> 命令行输入   vim .bashrc2>  按 i 进行编辑  输入      export PATH=$PATH:/htk3.5/bin.cpu（依据自己的盘符设置）3> 按esc  输入:w 保存，然后输入  ：q退出4>  然后回到命令行输入   source .bashrc  使得改变生效五、测试在命令行随便输入HTK命令，例如HInit，成功啦

## 2.音源训练方法

需要环境：Debain/Ubuntu x86_64系统

### 2.1.环境配置

首先下载训练包：<https://pan.baidu.com/s/1i6huEaD> ，下载后解压 下载安装HTK 安装HTS API 安装SPTK 安装HTS_for_HTK 开始训练 HTS 训练包 
```
sudo apt-get install libx11-dev:i386 
sudo apt-get install libc6-dev:i386 
sudo apt-get install libx11-dev:i386 libx11-dev 
sudo apt-get install g++-multilib 
sudo apt-get install osspd 
sudo apt install git csh libx11-dev cmake gcc g++ automake
```

### 2.2 安装顺序

先编译hts_engine_API，然后编译SPTK和HTS_for_HTK.编译时需要csh和libx11，apt里就有。编译hts_engine_API和SPTK后一定要make install，HTS_for_HTK可以不用install

### 2.3训练自己音源库

然后打开hts-train-demo文件夹，打开data，替换相应文件raw文件夹为音频文件，格式为：480000Hz Little Endian 跳过前2位可以使用ffmpeg进行转换：$ ffmpeg -i input.wav -f s16le -ar 48000 -acodec pcm_s16le output.rawlabel/full放入音乐的完整label，可以用Sinsy-remix导出$ sinsy -x dic -m jp.htsvoice -w c -o output.lab -l m infile.xmlexample $ sinsy -x /Sinsy-Remix/dic -m /Sinsy-Remix/nitech_jp_song070_f001.htsvoice -w c -o /sinsy_pro/XmltoLabel/hy_ch_s0037.lab -l m /sinsy_pro/xml_out/hy_ch_s0037.xml-x 指定发音表所在文件夹-m 指定任意一个htsvoice（不影响输出结果，必须指定一个htsvoice是bug）-w 指定语言，j代表日语，c代表中文注：如果需要使用其他语言进行训练，需要更改question文件（只需更改开头的音素不封，也就是LL-Phone到RR-Phone的区域），具体请查看：关于HTS问题集（question）的解析（一）label/mono指定的歌词和对应时间，元音和辅音要拆开来，时间一定要准确，否则影响音源合成效果注意：pau这个音的作用类似UTAU里的R，代表无声的范围，不能缺少。sil是用来填补曲子还没开始前的“上一个音”的空位具体怎么写可以参照官方的例子

### 2.4 开始训练

开始训练，需要的时间较长（6-12小时），建议在screen里运行screen -S train配置时一定要指定HTK的位置./configure --with-hts-search-path=../HTS_for_HTK/htk/HTKTools然后就可以开始make了make all如果训练时，perl提示找不到对应类，请手动执行下面的指令：perl scripts/Training.pl 当前的绝对路径/scripts/Config.pm大约6小时训练完成

### rawtowav

rawtowav [ fs(Hz) ] [ bit ] [ infile ] [ outfile ]

## 歌曲合成

$ sinsy -w j -x /Sinsy-Remix/dic -m /hts_train_song/HTS-train-demo/voices/qst001/ver1/nitech_jp_song070_f001.htsvoice -o /work/test.wav /work/xml/csongdb_f00002_000.xml

### docker 容器中中文出现乱码

1\. apt-get update && apt install vim2\. vim /etc/profile3\. 然后将export LANG="C.UTF-8"加到最后一行4\. 然后重新加载5\. source /etc/profile

## 关键点

*   label 生成

$ sinsy -x dic -m jp.htsvoice -w c -o output.lab -l m infile.xml #导出mono label$ sinsy -x dic -m jp.htsvoice -w c -o output.lab -l t infile.xml #导出full label

*   DataSet

- nitech_jp_song070_f001_003.lab-- DATASET = nitech_jp_song070 SPEAKER = f001- hy_ch_s0027.lab-- DATASET = hy SPEAKER = ch

*   configure
```
./configure --with-sptk-search-path=/usr/local/SPTK-3.9/bin \
                 --with-hts-search-path=../HTS_for_HTK/htk/HTKTools \
                 --with-hts-engine-search-path=/usr/local/hts_engine_API-1.10/bin \
                 MGCORDER=49
```               
                 
                 
![perl版本的问题](./images/1542168135906.png "perl版本的问题")perl版本的问题

*   提取特征后再次训练不需要重新提取（待验证）

cd make && make labels && cd .. && make voice

## 整体步骤

*   spider 爬虫获取wav
*   wav转raw 获取raw
*   xml转label mono -l m
*   xml转label full -l t

## 文本对齐（乐谱和wav）对齐工具

*   音素级别的对齐 <https://github.com/open-speech/speech-aligner>
*   ./bin/speech-aligner --allow-downsample=true --sample-frequency=48000 --config=egs/cn_phn/conf/align.conf egs/cn_phn/data/wav.scp egs/cn_phn/data/text egs/cn_phn/data/out1.ali
*   官方的命令：./bin/speech-aligner --config=egs/cn_phn/conf/align.conf egs/cn_phn/data/wav.scp egs/cn_phn/data/text egs/cn_phn/data/out.ali
```
--acoustic-scale=0.01    #声音范围
--allow-downsample=true # 允许降低采样频率
--careful=true  #精细化
--sample-frequency=16000
--ctm-output=true #输出ctm格式

--add-delta-pitch=true    #添加音高
--add-normalized-log-pitch  #添加正则化的音高值
--add-raw-log-pitch=true   #添加log（pitch）值到输出特征

./bin/speech-aligner --acoustic-scale=0.001 --careful=true --allow-downsample=true --sample-frequency=16000 --config=egs/cn_phn/conf/align.conf egs/cn_phn/data/wav.scp egs/cn_phn/data/text egs/cn_phn/data/out1.ali
```
## 批量转换wav的采样频率
```
#!/bin/bash
for file in *.wav; do
    echo $file
    c=${file}
    echo $c
    sox -v 0.9 $c -r 16000 -b 16 -c 1 new_$c
    rm -rf $c
    mv new_$c $c
done
```
![vioce 训练](./images/1543558682104.png)
## 对齐工具2
## praat 和转化工具
- praat 官网：http://www.fon.hum.uva.nl/praat/download_linux.html
- 转化工具地址：https://github.com/ecooper7/label-convert/blob/master/textgrid_to_lab.py
### 安装教程
-  sudo apt-get install praat
-  sudo apt-get install fonts-sil-charis
-   sudo apt-get install fonts-sil-doulos
-  **key key**：for lab in labels/mono/*.lab; do sed -i "s/ $//g" ${lab}; done
## An example of context-dependent label formatfor HMM-based singing voice synthesis

|  符号   |   含义  |翻译|
| --- | --- |---|
|   p1  | the language independent phoneme identity|语言独立的音ID
|p2|the phoneme identity before the previous phoneme| 音ID前面的音
|p3|the previous phoneme identity|以前的音ID
|p4|the current phoneme identity|当前的音的ID
|p5|the next phoneme identity|下一个音的ID
|p6|the phoneme idendity after the next phoneme|音ID后面一个的音
|p7|the phoneme flag before the previous phoneme|上一个音素之前的音素标志
|p8|the previous phoneme flag|以前的音素标志
|p9|the current phoneme flag|当前的因素标志
|p10|the next phoneme flag|下一个因素标志
|p11|the phoneme flag after the next phoneme|下一个音素后的音素标志
|p12|position of the current phoneme identity in the syllable (forward)|当前音素标识在音节中的位置（前进）
|p13|position of the current phoneme identity in the syllable (backward)|当前音素标识在音节中的位置（后面）
|p14|the distance from the previous vowel in the current syllable to the current consonant|从当前音节中的前一个元音到当前辅音的距离
|p15|the distance from the current consonant to the next vowel in the current syllable|在当前音节中当前辅音到下一个元音的距离
|p16|undefined context    |未定义的上下文
| --- | --- |---|
| a1|the number of phonemes in the previous syllable|上一个音节中的音素数量
|a2  |position of the previous syllable identity in the note (forward)|音符中前一个音节标识的位置（前进）
|a3|position of the previous syllable identity in the note (backward)|音符中前一个音节标识的位置（后面）
|a4|the language of the previous syllable|前一个音节的语言
|a5   |  the language dependent context of the previous syllable   |前一个音节的语言相关上下文
| --- | --- |---|
|b 1|the number of phonemes in the current syllable|当前音节中音素的数量
|b 2|position of the current syllable identity in the note (forward)|当前音节标识在笔记中的位置（向前）
|b 3|position of the current syllable identity in the note (backward)|音符中当前音节标识的位置（向后）
|b 4|the language of the current syllable|当前音节的语言
|b 5|the language dependent context of the current syllable|当前音节的语言相关上下文
| --- | --- |---|


























