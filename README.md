# Web-Crawler
novice

## 背景知识
<table>
<tr>
  <td rowspan="2"><b>数据交换语言</b><br/>
    <td>XML</td>
    <td> </td>
</tr>
<tr>
    <td>JSON</td>
    <td> </td>
</tr>
<tr>
  <td rowspan="5"><b>访问方式</b><br/>
    <td rowspan="4">GET<br/>
      <td >RCurl-GET</td>  
    </tr>
    <tr>
        <td >httr-GET</td>  
    </tr>
    <td >base-GET</td>  
    </tr>
    <tr>
        <td >rvest-GET</td>  
    </tr>
    <td rowspan="1">POST<br/>
    <td> RCurl-POST
</table>

以上是写爬虫需要了解的基本知识，数据交换语言可以理解为是目标网页存储数据的方式，访问方式有两种，目前我学到的是get有四种命令可以使用，post有一种。不同的存储方式和访问方式都会影响到你要调用的命令。

对上面这个简单的表格有个印象之后，会有助于下面的观察步骤。

## 爬虫前的观察
**工具：Chrome浏览器 使用F12打开Network Ctrl+U可以查看源代码**

观察步骤是为了确认几件重要的事情：
1. 网页的数据存储方式（即为XML or JSON）
2. 网页的访问方式（即GET or POST）
3. 爬虫的结构

**Tips：第三点尤其重要，以品牌库的爬虫为例，有时候一家公司的网站并不会只使用一种数据存储方式，这时候我们就需要确认每一层的信息都是以什么方式存储的，需要调用什么样的命令。通常新一线写的循环是省份-->城市-->区/县-->页--->门店，观察的时候就要确定每一层信息是以XML还是以JSON的方式存储的。**

一般情况下，我们点开每个网页的**headers**都能获取URL、访问方式和数据交换语言这些必备信息
而**Preview选项卡**则会显示这个网页返回的信息

![google1](https://lh6.googleusercontent.com/2uGXsa4_AJIZ0C0cMBISyJ19BvMy7l7ZOpXmaju0Q6QmzQDzyBmSqzqvDjeYkgETQ1JFI5OvzKxzwLsRUqbk3DQCrHMFA8Iy83Jd4x3zZozPa3v8f0PulAxLhGxfTpmNZ62zMjuf)

上图是一个典型的XML+GET的网页

![google2](https://lh3.googleusercontent.com/DQD1vhoRHXWJcpDMDcjye91kYD8xdUhuO3cE-ekVKJF_BBdbo5g0AhYkuCKLo1_qDL1QOr_ONvkBi8xbht0woTmQ2lrXuuQDl5Nunqre)

这是该XML型网页返回的信息格式，爬取XML的信息就需要跟这些代码写成的标签作斗争

![google3](https://lh4.googleusercontent.com/-DHl0lQI75yRvSeR_A6gHfMjCNRn9HwrYLONf3jtALEhRL772XEcP2WusXrAEiupLTXYGt_oLp2rUEWXpu4odz2fy0k_DFv1DVlTAonF)

这是一个典型的JSON+GET的网页

![google4](https://lh6.googleusercontent.com/b3G_BWPOiYBYXlYe7WdfcuEYViTgY3ljW1bOy8JAHfryjjnWDr4PN4dRLrCkI5aUIXzesZPTLJ4M3NECZpgiS_YQyukspQ-4pP3XLlfaEkRH-1vxyX5Lvt5Kuivj1ncpWtD9nFYa)

这是一个JSON网页返回信息的格式，点开每个小三角形都能看到具体的门店信息

![google5](https://lh6.googleusercontent.com/GnRGDXXjzFKaKqx8dw2ejkX2UTwhVDsfEVX6TYNQFzCLv3UgGLZy2HajUhIFRkDES9YQPuXv5NIgskdzIbok3g_xqzWxOOa-WLqTiV51lricmX-nW0XE5PQw6BUGC-fwTbwBNmW8)

上图是一个JSON+POST的网页，实际上我们还需要多关注一个信息是Form Data， 看下图:

![google6](https://lh3.googleusercontent.com/0u624QEuADetreEgV2_wAABMoAW1-LjI0N7Fks1_1365f27R8tr7NWI7JjNbR3N7H3mjuoMKir2P83X7sdCZdbfVHG1MSsGKCRnS9dnrr6WQflzbVDq-_epbYJiO_7l7_-SPSIbR)

点击View source你将会看到后面写代码需要用到的定位信息 一般会是省份、城市、区域等信息

**Tips： 之前爬到了几个很难找network文件的网站（华为和月星家居）
首先，基本上刷新无法加载出你想要的信息页面的时候，就可以判定他们的信息在一开始的时候就全部载入了，要把最初的network都检查一遍。
其次，有可能会出现一些你以为根本不会是信息所在的network文件里，比如，那个网址是用unicode编码的，不会出现中文，但是信息都藏在里面了。比如下图：**

![gooogle7](https://lh4.googleusercontent.com/6xx9WHbbtSOfWx534vuxn9o5TRpnmdxMy8rLUwpJgtOA197nfa82AwYTMb-RNpC-o_qMxQ-awd0dejT9gSiwpZJt0YmKpVo7QLeDJrDYwMuWpkWcvIhTjf_TsLfiLMxMhl9oHIRa)

这条url打开后，长这样：

![google8](https://lh5.googleusercontent.com/DORus5F9mFOSE0jsT4IlMb5B3ExwAR0CMZwMn2hi8_jdyau329J88stOSb3Jo1UqHFbj19LeiMWOwNEotAif-AzzKqev5UER1-VDbwQbIl0zp3k73R3FZxyHdrrUQe817EpBaubE)

## 爬虫实操
好了，当你点开Network观察了这么久，脑海里应该已经有爬虫的框架了，假如给你一张纸，应该能准确画出每一层信息的数据存储方式、访问方式和定位条件（if POST）。

先来瞎讲讲爬虫的基本原理吧，其实就是你的程序伪装成一个浏览器不断地访问目标网站，根据对方的数据交换语言获取所需信息。

所以，第一个问题就是，如何伪装。首先，如果你是一个要浏览网站的人的话，你应该先知道那个网站的地址，才能访问（用搜索引擎搜也得搜到），所以爬虫的第一条代码通常是
```ruby
url <- ‘目标网站网址’
```
**tips：这个url很可能不是通常我们查询信息的那个网址，而是我们在network里面翻找很久的，真正藏有信息的那个网址（所以看headers真的很重要！找文件是一种技能！）**

其次，要怎么真诚地假装自己是个浏览器呢，我们需要一个报头（呜呜呜，让我坚持写mkd动力就是他们的代码块好好看，可是写mkd也太虐了）：
```ruby
 myheader=c(
    "User-Agent"="Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:46.0) Gecko/20100101 
Firefox/46.0",
    "Accept"="text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
    "Accept-Language"="zh-TW,en-US;q=0.7,en;q=0.3",
    "Connection"="keep-alive",
    "X-Requested-With"="XMLHttpRequest",
    #"Cookies"='CURRENT_CITY_CODE=330100; CURRENT_CITY_NAME=%E6%9D%AD%E5%B7%9E; gr_user_id=16b36f75-aa33-4b70-bd7f-1ec609993450; gr_session_id_8da2730aaedd7628=0989ec12-f3b3-416f-bd4c-5c4e7682e062; mapType=%20; Hm_lvt_038002b56790c097b74c818a80e3a68e=1499838629; Hm_lpvt_038002b56790c097b74c818a80e3a68e=1499838761; _ga=GA1.2.1964519744.1499838629; _gid=GA1.2.1689810241.1499838629; PHPSESSID=k3ku5lci63muikqvmui2lkcm21; __utma=109191004.1964519744.1499838629.1499838745.1499838745.1; __utmb=109191004.1.10.1499838745; __utmc=109191004; __utmz=109191004.1499838745.1.1.utmcsr=(direct)|utmccn=(direct)|utmcmd=(none); CURRENT_CITY_CODE=330100; CURRENT_CITY_NAME=%E6%9D%AD%E5%B7%9E; td_cookie=18446744070009021559',
    "Accept-Charset"="GB2312,utf-8;q=0.7,*;q=0.7"
  )
```
**tips:被注释掉的cookie有时候是需要加的，万一加载不出网页就去headers里面copy一下cookie，有时候有用，大部分时候我没有用cookie**

好了 ，以上，你已经是一个成功的伪装者了。（怎么可能）

接下来，不同的数据交换语言和不同的访问方式，应该配合不同的命令进行操作，所以分开解释。

根据代码顺序，应该是先写访问方式的代码（这部分我太懒了 能看懂的就直接copy大象老师的lofter，呜呜呜我觉得大象老师用LOFTER记代码笔记是正确的）：
### 1. GET
- RCurl-GET
> 方法1，按参数设定（一般用这种就好了）
```ruby
webpage <- getURL(url,httpheader=myheader,.encoding="utf-8")
```
 
> 方法2：将参数统一放置在curl.opts中
```ruby
curl.opts <- list(
  httpheader =myheader,
  postfields = request.body
)
webpage <- getURL(url,.opts = curl.opts)
```
- httr-GET
```ruby
content(GET(url,add_headers(myheader)))
```
- base-GET
```ruby
webpage <- readLines(x = url, encoding = 'GB18030')
```
- rvest-GET（以前写链家用的是这种）
```ruby
webpage <- read_html(x = url, encoding = 'GB18030')
```
**tips:需要注意的是encoding，可以在浏览器的工具-->编码里面查找该页面的编码模式，我只遇到过UTF-8**

### 2. POST
- RCurl-POST
> 方法1：按参数设定（用这个的话，观察form data的时候请选择view parse 把"："改成"="就好）
```ruby
webpage <- postForm('http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=cname',
                    .params=list(
                  	cname="上海",
                  	pageIndex=1,
                  	pageSize=5000)
)
```
> 方法2：将参数统一放置在curl.opts中（推荐用这个）
```ruby
request.body <- paste('city_id=',decathlon.province.id[i],'&is_province=1')
curl.opts <- list(
  httpheader =myheader,
  postfields = request.body
)
webpage <- postForm(url,.opts = curl.opts)
```
写完访问方式就可以写读取数据格式开始作妖啦！！！
也即，以下代码都写在以上代码之后（我为什么要写这么愚蠢的提示.....）

### 3. XML
```ruby
pagexml <- htmlTreeParse(webpage,encoding="utf-8", error=function(...){},useInternalNodes = TRUE,trim=TRUE)
```
读取完之后，请使用火狐浏览器的查看器，根据数据标签，一层一层地获取信息，主要有两种格式，如果是纯文字信息，一般是以下代码：
```ruby
 dist_name_temp<- xpathSApply(pagexml,'//*[@class="clearfix filterList"]/li/span/a',xmlValue)
 ```

如果是href信息，一般是以下代码：
```ruby
 dist_url_temp<- xpathSApply(pagexml, '//*[@class="clearfix filterList"]/li/span[1]//a',name = 'href', xmlGetAttr)
 ```

标签通常要多试几次，不过如果抓下来是空的，**请先回去check你的url是否载入成功**，如果前面几步都出现问题的话，这一步是提取不出来的.....不要本末倒置了。

**爬虫的基本原则就是，有什么就抓什么，信息尽量抓全。**

“/”和“//”表示两种从属关系，大象讲了很多次我都记不得...但每次都是试验几下就能抓到了QAQ

**每个数据都要存到一个向量里**，在循环外建立空向量，抓好以后把数据合到向量里，最后用data.table合在一张表格里，一般会是下面这样的代码：
```ruby
        title <- c(title,title_temp)
        href <- c(href,href_temp)
        id <- c(id, id_temp)
        name <- c(name, name_temp)
        floor <- c(floor,floor_temp)
        year <- c(year, year_temp)
        house_type <- c(house_type,house_type_temp)
        rent <- c(rent, rent_temp)
        area <- c(area, area_temp)

content <- data.table(id,name,title,href,floor,year,house_type,rent,area,dist,plate)
```

至于要不要write.csv(“路径及文件名”)就看自己的需求了

### 4. JSON
在抓取信息这件事情上，JSON堪称业界良心，抓下来就是个list，转成Table就好了，完全不需要写标签一个个去试好开心啊！！！！

```ruby
c_list <- fromJSON(webpage)
c_table <- rbind.fill(lapply(c_list, function(y) {as.data.frame(t(y), stringsAsFactors = FALSE)}))
```

第一行是把list抓下来，第二行是转成表格，这里不用data.table(do.call())的原因是防止由于信息缺失出现表格错行的情况。用这个更保险。

通常写了循环，我们需要将每一次抓下来的表格合并在同一张表里，就用rbind的命令
```ruby
 list <- rbind(list, store_list,fill=TRUE)
```
注意开头要写list <- NULL清空表格

**（写品牌库的话 ，这个要写成table大象会爱你的）**

## 爬虫常用到的工具箱
### 1. 拼贴（循环里的url小帮手）
- 无缝拼贴
```ruby
paste0()  
```
### 2. 循环
```ruby
#for 循环#
for (i in 1: blabla）{
}

#while循环#
while( 条件）{

}
```
while 用的少...但是像三星和华为那种翻页却找不到总页数的时候，while比for好用，条件就是当一页满多少个门店的时候就翻页。

### 3. 字符串提取与正则表达式
- **提取**我一般用
```ruby
str_extract_all(text,‘’)
```
- **替换**一般用
```ruby
gsub(‘’,text)
```
正则表达式真的太好用了嗷！！！！

```ruby
#数字#[:digit:],#多位数字# :digit:]{1,}
#字信息#[:print:]
#果还需要具体的定位信息则可以这样写:digit:]{1,}+[平米]
#除所有非汉字字符一般用：#
city_name <-  gsub('[^\u4e00-\u9fff]','',c_list)
#对，[^\u4e00-\u9fff] 这玩意就是非汉字字符的意思#
```
如果有更多需求 **可以在help里面查询regular expression**

### 4. 其他
```ruby
as.numeric()#文本转数字
as.character() #这个还真不知道是为什么.....似乎是为了产生字符这个类型....我用到的情境是爬华为的时候把省份表格里的每个provinceid都用了一下....
content <- formList2Character(content)  #防止里面包含list
```
msg至今还不会 每次都叫大象来救


## 参考资料
- 大象的lofter：
http://helianthus-code.lofter.com
- 吕奕的数据科学博客：
http://leoluyi.logdown.com/
还是蛮有趣的 观察那部分是学POST和JSON的时候突然看到的 此前一直觉得爬XML的时候chrome用处不大 找文件也全凭感觉 后来才醒悟到观察network的重要性

## 爬过的东西总结
- GET+XML:
  > 自如
  
  > 链家（反爬大boss）
  
  > 鲜芋仙
  
  > forever21
  
  > 鼎泰丰（network找不需要翻页的文件）
  
  > Under Armour（同上）
  
  > 西贝莜面村

- GET+JSON：
  > 三星
  
  > 华为
  
  > urban revivo（微信 network里找文件）

- GET+JSON+XML：
  > Tommy Hilfliger

- POST+JSON+XML:
  > 民生银行


更新品牌库代码已完成。
感恩这两个月。


