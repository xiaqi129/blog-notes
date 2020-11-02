# CSS选择器

| 表达式                    | 说明                                   |
| ------------------------- | -------------------------------------- |
| *                         | 选取所有节点                           |
| #container                | 选择id为container的节点                |
| .container                | 选取所有class包含container的节点       |
| li a                      | 选取所有li下的所有a节点                |
| ul + p                    | 选取ul后面的第一个p元素                |
| div#container > ul        | 选取id为container的div的第一个ul子元素 |
| ul ~ p                    | 选取与ul相邻的所有p元素                |
| a[title]                  | 选取所有又title属性的a元素             |
| a[href="123.com"]         | 选取所有href属性为123.com值 的a元素    |
| a[href*=“123]             | 选取所有href属性包含123的a元素         |
| a[href^="http"]           | 选取所有href属性值以http开头的a元素    |
| a[href$=".jpg"]           | 选取所有href属性值以.jpg结尾的a元素    |
| input[type=radio]:checked | 选取选中的radio的元素                  |
| div:not(#container)       | 选取所有id非container的div元素         |
| li:nth-child(3)           | 选取第三个li元素                       |
| tr:nth-child(2n)          | 第偶数个tr                             |

***注： .extract()查看selector里面的值;   ::text查看里面文字**

