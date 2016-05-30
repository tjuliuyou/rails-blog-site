### 博客中图片处理

为了处理图片上传／存储／显示等问题，这里使用开源框架：**Paperclip**。具体安装参考Github上的[说明文档](https://github.com/thoughtbot/paperclip)。

一篇博客可能包含多个图片链接，一张图片属于一篇博客：

```bash
rails g model image name:string file:attachment file_meta:text blog:references
```
