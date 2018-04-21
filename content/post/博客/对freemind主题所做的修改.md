---
title: 对freemind主题所做的修改
toc: true
date: 2014-08-16 16:43:05
tags:
- freemind 主题
categories:
- 博客搭建
---

* 现已改用Jackman主题（做了些许修改）*

# 修改分类方式

原主题的侧边样的分类是无层次的分类，在此改为层次型的分类方式，好在hexo原本就支持这种分类方式。修改theme/freemind/layout/_widget/category.ejs文件如下：

``` html
<% if (site.categories.length){ %>
	<div class="widget">
		<h4><%= __('categories') %></h4>
		<ul class="tag_box list-unstyled">
			<%- list_categories() %>
		</ul>
	</div>
<% } %>
```

# 修改样式

原主题文章目录的间隔过小，特修改了style.css，增加了
``` css
.toc-article-item {
  margin-bottom: 5px;
}
```
