---
title: "[CN] Python Elasticsearch 常见操作"
layout: post
date: 2025-03-31 11:11
tag:
 - Python
 - Note
 - CN
blog: true
author: Topaz
summary: "Python"
permalink: Python-Elasticsearch-cn
---

<h1 class="title"> Python 学习笔记 01 </h1>

<h2> Elasticsearch 查询类型总结 </h2>
- [基础查询](#c1)
    - [1.1 match 查询](#c11)
    - [1.2 terms 查询: 精确查找多个可能的值](#c12)
    - [1.3 range 查询: 基于数值、日期等字段的范围查询。](#c13)
    - [1.4 wildcard 查询: 基于通配符进行模糊查询](#c14)
    - [1.5 fuzzy 查询: 模糊匹配](#c15)
    - [1.6 exists 查询: 检查某个字段是否存在](#c16)

- [组合查询类型](#c2)
    - [2.1 bool 查询](#c21)
    - [2.2 nested 查询](#c22)

<h2 id="c1">基础查询</h2>


Input Example
```
{
  "title": "Elasticsearch Guide",
  "content": "Learn Elasticsearch with examples and use cases.",
  "author": "John Doe",
  "status": "active",
  "published_date": "2023-01-01",
  "comments": [  #嵌套字段
    { "user": "Alice", "message": "Great article!", "date": "2022-12-01" },
    { "user": "Bob", "message": "Very helpful!", "date": "2022-12-05" }
  ]
}

```


Output Example
```
{
    "hits": {  # 查询结果的根节点，包含了查询结果相关的信息
    "total": {  # 表示总的匹配文档数量
      "value": 1,  # 返回的匹配文档的总数量，这里是 1，表示查询返回了一个文档
      "relation": "eq"  # 查询的关系类型，这里是 "eq" 表示匹配数量是精确的（等于）
    },
    "hits": [  # 匹配文档的数组，包含所有符合条件的文档
      {
        "_index": "my_index",  # 该文档所在的索引名称，表示文档来自哪个索引
        "_id": "1",  # 文档的唯一标识符（ID），在该索引中的文档唯一标识
        "_score": 0.8,  # 相关性评分，表示文档与查询条件的匹配度，0.8 表示匹配度较高
        "_source": {  # 返回的文档内容部分，包含了文档的所有字段数据
          "title": "Elasticsearch Guide",  # 文档的标题字段，类型为字符串
          "content": "Learn Elasticsearch with examples and use cases.",  # 文档的内容字段，类型为字符串
          "author": "John Doe",  # 作者字段，类型为字符串
          "status": "active",  # 状态字段，表示该文档的状态为 "active"
          "published_date": "2023-01-01",  # 发布日期字段，类型为日期
          "comments": [  # 嵌套字段 "comments"，包含了文档中的评论信息，是一个数组
            {
              "user": "Alice",  # 评论者用户名，类型为字符串
              "message": "Great article!",  # 评论内容，类型为字符串
              "date": "2022-12-01"  # 评论日期，类型为日期
            },
            {
              "user": "Bob",  # 评论者用户名，类型为字符串
              "message": "Very helpful!",  # 评论内容，类型为字符串
              "date": "2022-12-05"  # 评论日期，类型为日期
            }
          ]
        }
      }
    ]
    }
}

```


<h3 id="c11"> 1.1 match 查询 </h3>

`match` 查询适用于分析过的文本字段（`text` 类型）。

#### 子关键字:
- **`operator`**: 用来设置该查询的相关性提升因子，影响文档的得分。
- **`fuzziness`**: 指定查询匹配的值。

Code
```
{
  "query": {
    "match": {
      "content": "Elasticsearch use"  # 查找字段 "content" 中包含 "Elasticsearch" 的文档
      #"operator": "AND",  # 所有词语都必须出现，使用 "AND" 操作符
      #"fuzziness": "AUTO"  # 自动计算模糊度，允许拼写错误或近似匹配
      #"prefix_length": 3  # 前缀长度为 3，只对后缀部分进行模糊匹配
      "analyzer": "standard"  # 定义分词器，根据具体需求选择不同的分析器（如 standard, whitespace, english 等）。


    }
  }
}

```

#### 嵌套支持
    - 可以作为 **bool** 查询下 must、should、filter 的子查询。
    code
    ```
    {
      "query": {
        "bool": {
          "must": [
            {
              "match": {
                "title": "Elasticsearch"  # 这里的 match 查询作为 bool 查询的 must 部分
              }
            },
            {
              "match": {
                "content": "guide"  # 另一个 match 查询作为 bool 查询的 must 部分
              }
            }
          ]
        }
      }
    }

    ```

    - 不能作为 **nested** 的子查询



<h3 id="c12"> 1.2 terms 查询 </h3>
terms 查询用于精确查找多个可能的值，适用于 keyword 类型字段。


#### 子关键字:
- **`boost`**: 控制多个词语之间的操作符，默认为 `OR`，可以改为 `AND`。
- **`value`**: 控制模糊匹配的程度（适用于 `match` 类型）。


Code
```
# terms 查询用于精确查找多个可能的值。这里查询的是 status 字段的值是否为 active 或 pending。
{
  "query": {
    "terms": {
      "status": ["active", "pending"],  # 查询 "status" 字段是否为 "active" 或 "pending"
      "boost": 2.0  # 提升该查询条件的相关性评分
    }
  }
}

```

#### 嵌套支持
    - 可以作为 **bool** 查询下 must、should、filter 的子查询。
    - 不能作为其他查询类型（如 match、range）的子查询。



<h3 id="c13"> 1.3 range 查询 </h3>
range 查询用于数值、日期等字段的范围查询。

#### 子关键字:
- **`gte`**: 大于等于。
- **`lte`**: 小于等于。
- **`gt`**: 大于
- **`lt`**: 小于
- **`boost`**: 用来设置该查询的相关性提升因子，影响文档的得分。

Code
```
    {
    "query": {
        "range": {
        "field": {
            "gte": 10,  # 大于等于 10
            "lte": 20   # 小于等于 20
            }
            }
            }
    }

```

#### 嵌套支持
    - 可以作为 **bool** 查询下 must、should、filter 的子查询。
    - 不能作为其他查询类型（如 match、range）的子查询。


<h3 id="c14"> 1.4 wildcard 查询 </h3>
使用通配符进行模糊查询，支持 * 和 ?。

#### 子关键字:
- **`rewrite`**: 用来设置查询的重写规则。
- **`boost`**: 用来设置该查询的相关性提升因子，影响文档的得分。

Code
```
    {
      "query": {
        "wildcard": {
          "title": {
            "value": "Elastic*"  # 查找 "title" 字段中以 "Elastic" 开头的文档
          }
        }
      }
    }

```

#### 嵌套支持
    - 可以作为 **bool** 查询下 must、should、filter 的子查询。
    - 不能作为 **nested** 的子查询


<h3 id="c15"> 1.5 fuzzy 查询 </h3>

模糊匹配，适用于拼写错误查询，基于编辑距离。

#### 子关键字:
- **`fuzziness`**: 控制模糊匹配的程度（适用于 `match` 类型）。
- **`prefix_length`**: 控制前缀长度，用于模糊查询时的前缀长度。
- **`max_expansions`**: 限制模糊匹配的扩展数量。

Code
```
    {
      "query": {
        "fuzzy": {
          "title": {
            "value": "valu",  # 查找 "title" 字段中拼写相似的文档
            "fuzziness": "AUTO",  # 自动计算模糊度
            "prefix_length": 2  # 前两个字符必须精确匹配，剩余部分进行模糊匹配
          }
        }
      }
    }
```


#### 嵌套支持
    - 可以作为 **bool** 查询下 must、should、filter 的子查询。
    - 不能作为 **nested** 的子查询





<h3 id="c16"> 1.6 exists 查询 </h3>

查询数据中某个字段是否存在。

#### 子关键字:
- **`field`**: 用于检查数据中指定字段名称是否存在。

Code
```
    {
      "query": {
        "exists": {
          "field": "author"  // 查询字段 "author" 是否存在
        }
      }
    }

```


#### 嵌套支持
    - 可以作为 **bool** 查询下 must、should、filter 的子查询。
    Code
    ```
    {
      "query": {
        "bool": {
          "must": [
            {
              "term": { "status": "active" }  # 仅查询 "status" 为 "active" 的文档
            }
          ],
          "filter": [
            {
              "exists": {
                "field": "author"  # 查询 "author" 字段是否存在
              }
            }
          ]
        }
      }
    }

    ```

    - 不能作为 **nested** 的子查询






<h2 id="c2">组合查询类型</h2>


<h3 id="c21"> 2.1 bool 查询 </h3>
#### 子关键字:
- **`must`**: 所有条件都必须匹配。
- **`must_not`**: 条件不能匹配，用于排除文档。
- **`should`**: 条件可以匹配，匹配时增加文档的相关性得分。
- **`filter`**: 条件必须匹配，但不参与评分，用于高效过滤
- **`boost`**: 用来设置该查询的相关性提升因子，影响文档的得分
- **`minimum_should_match`**: 指定至少要匹配 should 条件中的多少个。

Code
```
    # status 必须是 "active"（must）, author 字段存在（filter，不参与评分, content 包含 "Elasticsearch"（should，增加相关性评分）。
    {
      "query": {
        "bool": {
          "must": [
            {
              "term": { "status": "active" }  # 必须查询 "status" 为 "active"
            }
          ],
          "filter": [
            {
              "exists": {
                "field": "author"  # 必须查询 "author" 字段存在
              }
            }
          ],
          "should": [
            {
              "match": {
                "content": "Elasticsearch"  # "content" 中包含 "Elasticsearch" 提升相关性得分
              }
            }
          ]
        }
      }
    }


```


#### 嵌套支持
    - 可以作为 **bool** 查询下 must、should、filter 的子查询。
    - 不能作为 **nested** 的子查询


<h3 id="c22"> 2.1 nested 查询 </h3>
用于查询嵌套对象或数组数据。

#### 子关键字:
- **`path`**: 控制多个词语之间的操作符，默认为 `OR`，可以改为 `AND`。
- **`query`**: 控制模糊匹配的程度（适用于 `match` 类型）。

Code
```
    {
      "query": {
        "nested": {
          "path": "comments",  # 查询嵌套字段 "comments"
          "query": {
            "term": {
              "comments.user": "Alice"  # 查询 "comments" 中的 "user" 字段值为 "Alice"
            }
          }
        }
      }
    }


```


#### 嵌套支持
    - 可以作为 **bool** 查询下 must、should、filter 的子查询。
    Code
    ```
    # status 字段必须为 "active"， comments 字段中包含 user 为 "Alice" 的评论（nested 查询）, content 中包含 "Elasticsearch"。
    {
      "query": {
        "bool": {
          "must": [
            {
              "term": { "status": "active" }  # 查询 "status" 为 "active"
            }
          ],
          "filter": [
            {
              "nested": {
                "path": "comments",  # 在嵌套字段 "comments" 中查询
                "query": {
                  "term": {
                    "comments.user": "Alice"  # 查询 "comments.user" 为 "Alice"
                  }
                }
              }
            }
          ],
          "should": [
            {
              "match": {
                "content": "Elasticsearch"  # 如果 "content" 包含 "Elasticsearch"，则增加相关性得分
              }
            }
          ]
        }
      }
    }


    ```
