---
layout: post
title: "通过调用 youdao api 实现 linux 终端命令查词"
date: 2019-06-23 15:23:48 +0800
image: dict.jpg
tags: [dict, youdao, linux, terminal]
categories: linux
---

##	 0x01 向 `.bashrc` 文件添加功能所需的脚本代码  
- 向 `~/.bashrc` 文件中添加以下代码  

```
# youdao translate
t(){
	words=""
		for word in $@;  # $@ 为传入的参数
	do
		# 判断是否存在以传入参数为名的文件，若存在，则进行文本翻译
		if [ -e $word ];then
			# 读取文件内容并整合为一行
			cat $word|while read line;
			do
				# 将需要翻译的源文本写入 word.out 文件
				echo $line >>$word.out;
				# 将源文本中的`空格`替换为`%20`，以防止请求地址被分割成多个参数
				line=`echo $line|sed 's/[ ]/%20/g'`;
				# -s 参数为静默模式
				curl -s http://fanyi.youdao.com/openapi.do?keyfrom=minede\&key=1074042860\&type=data\&doctype=json\&version=1.2\&translate=on\&q=$line|sed 's/{\"translation\":\[\"\(.*\)\"\],\"\(b\|q\).*/\1\n/g' >>$word.out
			done;
		else
			# 若传入参数不是文件名，则作为单词进行查词。`jq`为json解析工具，需要单独安装
			curl -s http://fanyi.youdao.com/openapi.do?keyfrom=minede\&key=1074042860\&type=data\&doctype=json\&version=1.2\&q=$word|jq '.';
		fi
	done
	return ;
}
```

- `source ~/.bashrc` 使之生效  

## 0x02 使用方法  
- 查词：  
  - 通过 `t word` 查询单词 word  
- 翻译句子或文本：  
  - 将需要翻译的文本写入当前目录下 `文件名` 的文件中  
  - 通过 `t 文件名` 执行翻译  
  - 翻译完成的文本将保存在 `文件名.out` 文件中  