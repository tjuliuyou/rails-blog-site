#Build My Site Step By Step 2

### 创建blogs 数据库

博客包含博客名和内容。同时，一篇博客可能包含多个评论、多个标签；一句评论只属于一篇博客；一个标签可能包含多个文章；日期归档包含多篇文章，一篇文章只属于一个归档。

```bash
rails g model archive start:datetime, end:datetime
rails g model blog title:string content:text archive:references
rails g model tag name:string, count:integer
rails g model blog_tag_ship blog:references, tag:references
rails g model comment commenter:string body:text blog:references
```


### 博客中图片处理

为了处理图片上传／存储／显示等问题，这里使用开源框架：**Paperclip**。具体安装参考Github上的[说明文档](https://github.com/thoughtbot/paperclip)。

一篇博客可能包含多个图片链接，一张图片属于一篇博客：

```bash
rails g model image name:string file:attachment file_meta:text blog:references
```


