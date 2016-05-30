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

修改Active Record关联

* **models/archive.rb**

```ruby
class Archive < ActiveRecord::Base
  has_many :blogs
end
```
* **models/blog.rb**

```ruby
class Blog < ActiveRecord::Base
  belongs_to :archive
  has_many :comments
  has_many :blog_tag_ships
  has_many :tags, through: :blog_tag_ships
end
```
* **models/comment.rb**

```ruby
class Comment < ActiveRecord::Base
  belongs_to :blog
end
```
* **models/tag.rb**

```ruby
class Tag < ActiveRecord::Base
  has_many :blog_tag_ships
  has_many :blogs, through: :blog_tag_ships
end

```
* **models/blog_tag_ship.rb**

```ruby
class BlogTagShip < ActiveRecord::Base
  belongs_to :tag
  belongs_to :blog
end
```

然后，使用 rake 命令运行迁移：

```bash
bin/rake db:migrate
```

为blog添加pubuliced变量

```bash
bin/rails g migration add_published_to_blogs
```
再次运行命令迁移

### 向数据库添加测试数据

更新**seeds.rb:**

```ruby
Archive.delete_all
archive = Archive.create({start_time: '2016-05-1 0:0:0',end_time: '2016-05-31 23:59:59'})

bg1 = archive.blogs.create(title: 'hello world', content: 'this is my first blog!!', published: true)

bg1.comments.create(commenter:"GOod",body: 'this is good')
bg1.comments.create(commenter:"GOod2",body: 'this is good2')

tag1 = Tag.create(name: 'linux')
tag2 = Tag.create(name: 'windows')

BlogTagShip.create(blog: bg1, tag: tag1)

archive.blogs.create(title: 'hello world2', content: 'this is my second blog!!', published: true)
```

### 添加博客主界面框架

创建新的文件 **model/layouts/blogs.html.slim**

```jade
doctype html
html
  = render 'layouts/head'
  body
    =render 'shared/navigation_bar'
    .container
        div class='panel panel-default'
          .panel-body
            row
              div class='col-md-9 col-lg-9'
                = yield :pages
              div class='col-md-9 col-lg-3'
                = yield :widgets
```

为blog添加help方法,将archive起始时间转换成显示字段：

```ruby
def parse_archive_time_range(start_time)
  Time.parse(start_time.to_s).strftime('%Y年 %m月')
end
```

创建新文件 **model/blogs/index.html.slim**

```jade
- content_for :pages do
  - for blog in @blogs
    div class='panel panel-article'
        h3 
          = link_to blog_path(blog) do
            i class='fa fa-file-text' &nbsp; #{blog.title}
    p 
      = blog.content
      
- content_for :widgets do
  h3
    small 标签
  div class='panel panel-default'
    div class='panel-body'
      - for tag in @tags
        = link_to tag_path(tag) do
          i class='fa fa-tag' &nbsp; #{tag.name}
  h3
    small 归档
  div class='panel panel-default'
    div class='panel-body'
      - for archive in @archives
        = link_to archive_path(archive) do
          i class='fa fa-calendar' &nbsp; #{parse_archive_time_range archive.start_time}
```

### 为tag和archive 添加controller

```bash
bin/rails g controller tags
bin/rails g controller archives
```

制定layout模版，并添加show 方法

```ruby
layout 'blogs'
  def show
    @blogs = Tag.find(params[:id]).blogs
    #当点击tag 后 显示所有关联的blogs
  end
```
> 这里archive配置类似，不再贴代码

为show 创建view模版

```jade
- content_for :pages do
  - for blog in @blogs
    div class='panel panel-article'
        h3 
          = link_to blog_path(blog) do
            i class='fa fa-file-text' &nbsp; #{blog.title}
    p 
      = blog.content
- content_for :widgets do
  h3
    small 最新文章
  div class='panel panel-default'
    div class='panel-body'
```

同时将路径添加至路由

```ruby
resources :tags, only: :show

resources :archives, only: [:show, :index]
```






