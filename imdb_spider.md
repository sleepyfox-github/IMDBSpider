---
title: 电影大数据分析平台--爬虫
date: 2021-07-12 12:37:59
author:万理
group：实训第七组
---

#### 确认需求

在编写代码前，向数据分析师和项目经理确定了爬取的需求：网页为imdb的电影详情页面。

每个电影详情页面需要提取的内容如下：

电影名称，时长，类型，发行时间，简介，导演，编剧，演员，评分，评分人数，短评数，影评数，Metascore，语言，原产国，制作公司，估计预算，全球票房，获奖及提名数。

#### URL结构

每个电影详情页面的url结构为https://www.imdb.com/title/imdbid。其中，imdbid是每部电影对应的七位数字。

link.csv文件包含62000余部电影imdb id，依据这些id生成url进行爬取。link.csv来自于MovieLens发布于2019年12月的ml-25m数据集。ml-25m数据集网址：https://grouplens.org/datasets/movielens/25m/

![](https://gitee.com/wanli-0ziyuan/gitee-graph-bed/raw/master/img/20210712170340.jpg)

通过requests库依据这些id生成的url获取每部电影详情页面的HTML。

#### 日志

通过logging库对HTML的获取情况进行日志记录，便于debug和查看爬取进度。

![](https://gitee.com/wanli-0ziyuan/gitee-graph-bed/raw/master/img/20210712170318.jpg)

#### 解析和提取

借助bs4库的BeautifulSoup模块解析和提取 HTML数据。使用第三方的解析器lxml，比Python默认的解析器速度更快。主要用find（），find_all（），find_next()等函数在解析生成的文档树中提取感兴趣的信息。

提取时还用到了unicodedata库对一些信息进行了规范化处理。页面的缺失信息则通过异常捕获进行置空。

每个详情页的信息保存在一个list中，作为一行写入csv文件，所以要用到csv库。

#### 黑白名单

为了爬取中断后从断点继续爬取，引入了white_list.txt文件保存已经处理过的id及其imdbid，重新运行程序会跳过这些id，不用从头来过。

相应的，也有black_list.txt文件。用来保存获取HTML失败的电影id及其imdbid，可以后面单独处理。一般是两类：一是网页已经无法访问，即404；二是imdbid不足七位且未补0到七位，这种情况将black_list.txt另存为csv文件，在csv文件里用公式TEXT(B2：,REPT("0"，8))即可补0。

简单来说，black_list.txt文件中的电影可以不处理。处理的话，只需要转csv进行补0，然后作为给程序提供imdbid的文件，再运行一下程序即可。

#### 爬取结果

考虑到耗时和够用就行，最终只爬取了26000余部电影的信息，没有爬完。

![](https://gitee.com/wanli-0ziyuan/gitee-graph-bed/raw/master/img/20210712170247.png)