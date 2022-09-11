## 曾经用到的正则表达式

```js
// 邮箱
let reg1 = /^(\w)+(\.\w+)*@(\w)+((\.\w+)+)$/g;
// 文件名不能带有的字符
let reg2 = /[\\\\/:*?"<>|]/g
// 去除所有空白
let reg3 = /\s/g
// 前后空格
let reg4 = /(^\s)|(\s*$)/g
// 行首空格
let reg5 = /(^\s)/g
// 行尾的空格
let reg6 = /(\s*$)/g
// 获取script标签内容
let reg7 = /<script>(.*?)<\/script>/ig
// 获取所有闭合标签内的文本
let reg8 = /<[a-zA-Z]+.*?>([\s\S]*?)<\/[a-zA-Z].*?>/ig
// Unicode码
let reg9 = /\\u[0-9a-fA-F]{4}/g
let reg10 = /(\\u)(\w{1,4})/gi
// 匹配图片名称(不包含后缀)
let reg11 = /\/(\w+(?:-)\w+)\.(?:gif|png|jpg)/i
// 匹配行首
let reg12 = /^[\t\r\n]+/
// 匹配行尾
let reg13 = /[\t\r\n\s]+$/
// 匹配所有的html标签。但不包括html标签内的内容
let reg14 = /<[^>]+>/gi;
// 匹配除img标签外的html标签  不包括html标签内的内容
let reg15 = /<(?!img).*?>/gi;
// 匹配除img、p标签外的html标签  不包括html标签内的内容
let reg16 = /<(?!img|p|\/p).*?>/gi;
// 只匹配img、br、hr、input标签
let reg17 = /<(img|br|hr|input)[^>]*>/gi;
// 匹配所有的div标签。不包括div标签内的内容
let reg18 = /<(div|\/div).*?>/gi;
// 匹配所有的div标签。包括div标签内的内容
let reg19 = /<div[^>]*>[^<]*<\/div>/gi;
// 匹配所有的html标签，包括html标签内的内容
let reg20 = /<[^>]*>[^<]*(<[^>]*>)?/gi;
// 分组匹配，过滤所有的html标签，包括内容
let reg21 = /<(\S*)[^>]*>[^<]*<\/(\1)>/gi;
// 身份证
let reg22 = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/
```