---
title: python-img-to-base64
date: 2018-12-17 10:41:40
tags:
- python
- base64
---

```python
import base64

f = open('./img/1536204659.342245.jpg', 'rb')
ls_f = base64.b64encode(f.read())
f.close()
print(str(ls_f, 'utf-8'))

```

注意事项：

1. 文件名称不能是base64.py
2. python3中字符串为unicode编码，而b64encode函数的参数为byte类型，所以必须先转码
`encodestr = base64.b64encode('abcr34r344r'.encode('utf-8'))`，打印`print(encodestr)`
结果为：`b'YWJjcjM0cjM0NHI='`
多了一个`b`表示byte的意思。只要再讲byte转换回去就好了`print(str(encodestr,'utf-8'))`结果为`YWJjcjM0cjM0NHI=`


完整代码：
```bash
import base64

encodestr = base64.b64encode('abcr34r344r'.encode('utf-8'))
print(str(encodestr,'utf-8'))

YWJjcjM0cjM0NHI=
```
