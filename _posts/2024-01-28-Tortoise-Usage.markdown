---
title: "Tortoise 的基本语法和使用指南"
layout: post
date: 2024-01-28 18:01
tag:
 - FastAPI
 - ORM
 - CN
blog: true
author: Topaz
summary: "Tortoise 的基本语法和使用指南"
permalink: tortoise-usage
---
<h1 class="title"> Tortoise 的基本语法和使用指南 </h1>

<h2> Table of Contents </h2>
- [1. 安装 Tortoise-ORM ](#c1)
- [2. 基本模型定义](#c2)
    - [2.1 一对一关系](#c21)
    - [2.2 一对多关系](#c22)
    - [2.3 多对多关系](#c23)
    - [2.4 Meta 类的作用](#c24)

- [3. 基本操作](#c3)
    - [3.1 CURD](#c31)
    - [3.2 Deployment YAML](#c32)
    - [3.3 Service YAML](#c33)
    - [3.4 PersistentVolumeClaim (PVC) YAML](#c34)
    - [3.5 NetworkPolicy YAML](#c35)      

<h2 id="c1"> 1. 安装 Tortoise-ORM </h2>

{% highlight bash %}
pip install tortoise-orm aiosqlite
{% endhighlight %}


<h2 id="c2">  基本模型定义 </h2>

<h3 id="c21"> 2.1 一对一关系 </h3>

定义
{% highlight python %}
class User(Model):
    id = fields.IntField(pk=True)
    username = fields.CharField(max_length=50)
    wallet: fields.ReverseRelation["Wallet"]  # 反向关系

class Wallet(Model):
    id = fields.IntField(pk=True)
    coins = fields.IntField(null=True)
    user = fields.OneToOneField("models.User", related_name="wallet")  # 一对一外键
{% endhighlight %}

使用
{% highlight python %}
# 创建用户和钱包
user = await User.create(username="John")
wallet = await Wallet.create(coins=200, user=user)

# 查询
## 正向查询（从 User 查 Wallet)
user = await User.get(id=1).select_related("wallet")
print(user.username, user.wallet.coins)

## 反向查询（从 Profile 查 User）
wallet = await Wallet.get(id=1).select_related("user")
print(wallet.coins, wallet.user.username)

# 改（Update）
profile = await Profile.get(id=1)
profile.bio = "Updated bio for John."
await profile.save()

# 删（Delete）
profile = await Profile.get(id=1)
await profile.delete()

{% endhighlight %}


<h3 id="c23"> 2.2 一对多关系 </h3>

定义
{% highlight python %}
class User(Model):
    """
    用户表（与 Post 一对多）
    """
    id = fields.IntField(pk=True)
    username = fields.CharField(max_length=50)
    email = fields.CharField(max_length=100, unique=True)

    posts: fields.ReverseRelation["Post"]  # 反向一对多关系

{% endhighlight %}


{% highlight python %}
class Post(Model):
    """
    帖子表
    """
    id = fields.IntField(pk=True)
    title = fields.CharField(max_length=100)
    user = fields.ForeignKeyField("models.User", related_name="posts")  # 一对多外键关系
{% endhighlight %}


使用
{% highlight python %}
# 创建用户和帖子
user = await User.create(username="Alice")
await Post.create(title="Alice's first post", user=user)
await Post.create(title="Alice's second post", user=user)

# 查询
## 正向查询（从 User 查 Posts）
user = await User.get(id=1).prefetch_related("posts")
print(user.username, [post.title for post in user.posts])


## 反向查询（从 Post 查 User）
post = await Post.get(id=1).select_related("user")
print(post.title, post.user.username)

# 改（Update）
post = await Post.get(id=1)
post.title = "Updated post title"
await post.save()


# 删（Delete）
post = await Post.get(id=1)
await post.delete()



{% endhighlight %}



<h3 id="c23"> 2.3 多对多关系 </h3>
定义
{% highlight python %}
class Author(Model):
    """
    作者表（与 Book 多对多）
    """
    id = fields.IntField(pk=True)
    name = fields.CharField(max_length=100)
    books: fields.ManyToManyRelation["Book"]  # 反向多对多关系
{% endhighlight %}


{% highlight python %}
class Book(Model):
    """
    书籍表
    """
    id = fields.IntField(pk=True)
    title = fields.CharField(max_length=100)
    authors = fields.ManyToManyField("models.Author", related_name="books")  # 多对多关系
{% endhighlight %}

使用

{% highlight python %}
# 创建作者和书籍
author1 = await Author.create(name="Author A")
author2 = await Author.create(name="Author B")

book1 = await Book.create(title="Book 1")
book2 = await Book.create(title="Book 2")

# 关联作者和书籍
await author1.books.add(book1, book2)
await author2.books.add(book1)


#查（Retrieve）
## 正向查询（从 Author 查 Books）
author = await Author.get(id=1).prefetch_related("books")
print(author.name, [book.title for book in author.books])

## 反向查询（从 Book 查 Authors）
book = await Book.get(id=1).prefetch_related("authors")
print(book.title, [author.name for author in book.authors])

# 改（Update）
# 为作者添加新书
author = await Author.get(id=1)
book = await Book.create(title="New Book")
await author.books.add(book)

## 删（Delete）
# 移除作者与书籍的关系
author = await Author.get(id=1)
book = await Book.get(id=2)
await author.books.remove(book)

# 删除书籍
await book.delete()


{% endhighlight %}


<h3 id="c24"> 2.4 Meta 类的作用 </h3>

{% highlight python %}
class User(Model):
    id = fields.IntField(pk=True)
    username = fields.CharField(max_length=50)

    class Meta:
        table = "user"  # 指定数据库中的表名
{% endhighlight %}
