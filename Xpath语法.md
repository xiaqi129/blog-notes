# Xpath语法

| 表达式                                   | 说明                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| article                                  | 选取所有article元素的所有子节点                              |
| /article                                 | 选取根元素article                                            |
| article/a                                | 选取所有属于article的子元素的a元素                           |
| //div                                    | 选取所有div子元素（无论出现在文档的任何地方）                |
| article//div                             | 选取所有属于article元素的后代的div元素，不管它出现在article之下的任何位置 |
| //@class                                 | 选取所有名为class的属性                                      |
| /article/div[1]                          | 选取属于article子元素的第一个div元素                         |
| /article/div[last()]                     | 选取属于article子元素的最后一个div元素                       |
| /article/div[last()-1]                   | 选取属于article子元素的倒数第二个div元素                     |
| //div[@lang]                             | 选取所有拥有lang属性的div元素                                |
| //div[@lang='eng']                       | 选取所有lang属性为eng的div元素                               |
| /div/*                                   | 选取属于div元素的所有子节点                                  |
| //*                                      | 选取所有元素                                                 |
| //div[@*]                                | 选取所有带属性的title元素                                    |
| /div/a \| //div/p                        | 选取所有div元素的a和p元素                                    |
| //span \| //ul                           | 选取文档中的span和ul元素                                     |
| article/div/p \| //span                  | 选取所有属于article元素的div元素的p元素以及文档中所有的span元素 |
| //span[contains(@class, 'vote-post-up')] | 选取span元素中包含指定class名的元素                          |

***注： .extract()查看selector里面的值**