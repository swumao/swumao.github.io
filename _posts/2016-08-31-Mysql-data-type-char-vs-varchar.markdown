---
layout: post
title:  "Mysql char vs varchar"
date:   2016-08-31 21:56:00 +0800
categories: swumao update mysql char varchar
---

在mysql中，CHAR和VARCHAR都是用来存储字符串的，两者声明时都需要指示长度，如CHAR(30)代表最长存储30个字符的数据。下面说明下两者的区别。

## CHAR

- 长度范围：0-255
- 存储方式：将要存储的数据的右侧填充空格到指定长度。
- 读取方式：除非PAD_CHAR_TO_FULL_LENGTH模式被开启，否则将会去掉空格。

## VARCHAR

- 长度范围：0-65535
- 存储方式：VARCHAR存储时会带上1-byte或者2-byte长度的前缀，这个前缀用来指示这个数据中有多少字节(byte)

	- 当字符串长度没有超过255字节时，使用1-byte来存储前缀
	- 当字符串长度超过255字节时，使用2-byte来存储前缀
	- VARCHAR的值将不会被填充空格保存，但是为了与标准SQL保持一致，如果要存储的值的末尾有空格，则会被保存下来。

## 相同点

- 当要存储的字符串长度超过指定的长度时，如果是strict SQL模式，则字符会被截断并产生一个error；如果是非strict模式，则会被截断并产生一个warning，因此官方文档建议使用严格模式。

下面的表格是个例子：
<table>
	<tr>
		<th>Value</th>
		<th>CHAR(4)</th>
		<th>Storage Required</th>
		<th>VARCHAR(4)</th>
		<th>Storage Required</th>
	</tr>
	<tr>
		<td>''</td>
		<td>'    '</td>
		<td>4 bytes</td>
		<td>''</td>
		<td>1 byte</td>
	</tr>
		<tr>
		<td>'ab'</td>
		<td>'ab  '</td>
		<td>4 bytes</td>
		<td>'ab'</td>
		<td>3 bytes</td>
	</tr>
		<tr>
		<td>'abcd'</td>
		<td>'abcd'</td>
		<td>4 bytes</td>
		<td>'abcd'</td>
		<td>5 bytes</td>
	</tr>
		<tr>
		<td>'abcdefgh'</td>
		<td>'abcd'</td>
		<td>4 bytes</td>
		<td>'abcd'</td>
		<td>5 bytes</td>
	</tr>
</table>

## 未完待续...