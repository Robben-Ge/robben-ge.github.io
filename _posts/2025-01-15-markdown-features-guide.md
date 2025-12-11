---
layout: post
title: Markdown 功能使用指南
date: 2025-01-15 14:00:00
description: 这是一篇综合指南，展示了博客文章中可以使用的所有 Markdown 和 Jekyll 功能
tags: 指南 教程 markdown
categories: 教程
thumbnail: assets/img/9.jpg
featured: true
giscus_comments: true
related_posts: true
toc:
  beginning: true
chart:
  chartjs: true
pretty_table: true
code_diff: true
---

这是一篇综合指南，展示了在博客文章中可以使用的所有功能。你可以参考这篇文章来了解如何使用各种 Markdown 和 Jekyll 特性。

## 1. 基础格式和链接

### 文本格式

你可以使用 **粗体**、*斜体*、~~删除线~~ 等格式。

### 链接

这是一个 [链接示例](https://example.com)。

### 列表

**无序列表：**
- 项目 1
- 项目 2
- 项目 3

**有序列表：**
1. 第一项
2. 第二项
3. 第三项

**任务列表：**
- [x] 已完成的任务
- [ ] 未完成的任务
  - [x] 子任务 1
  - [ ] 子任务 2

### 引用

> 这是一个普通的引用块
> 可以包含多行内容

---

## 2. 自定义引用块

你可以使用三种特殊样式的引用块：

> ##### TIP
>
> 这是一个提示信息，用于给出建议或提示。
{: .block-tip }

> ##### WARNING
>
> 这是一个警告信息，用于提醒用户注意某些事项。
{: .block-warning }

> ##### DANGER
>
> 这是一个危险提示，用于强调重要的警告信息。
{: .block-danger }

---

## 3. 代码高亮

### 基础代码块

```python
def hello_world():
    print("Hello, World!")
    return True
```

### 带行号的代码块

使用 Liquid 标签可以显示行号：

{% highlight python linenos %}
def fibonacci(n):
    if n <= 1:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
{% endhighlight %}

### 代码差异显示

使用 `diff` 语言显示代码差异：

```diff
- console.log("Hello World!")
+ console.log("Hello from Diff2Html!")
```

使用 `diff2html` 可以显示更美观的差异：

```diff2html
diff --git a/sample.js b/sample.js
index 0000001..0ddf2ba
--- a/sample.js
+++ b/sample.js
@@ -1 +1 @@
-console.log("Hello World!")
+console.log("Hello from Diff2Html!")
```

---

## 4. 数学公式

这个主题支持使用 MathJax 3 渲染数学公式。

### 行内公式

行内公式：$$ E = mc^2 $$

### 块级公式

$$
\sum_{k=1}^\infty |\langle x, e_k \rangle|^2 \leq \|x\|^2
$$

### 带编号的公式

\begin{equation}
\label{eq:cauchy-schwarz}
\left( \sum_{k=1}^n a_k b_k \right)^2 \leq \left( \sum_{k=1}^n a_k^2 \right) \left( \sum_{k=1}^n b_k^2 \right)
\end{equation}

引用公式：\eqref{eq:cauchy-schwarz}

---

## 5. 图片展示

### 单张图片

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/9.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    单张图片示例
</div>

### 多张图片网格

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/9.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/7.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    两张图片并排显示，支持缩放功能
</div>

### 三张图片网格

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/12.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/7.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    三张图片网格布局
</div>

---

## 6. 视频嵌入

### 本地视频

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="assets/video/pexels-engin-akyurt-6069112-960x540-30fps.mp4" class="img-fluid rounded z-depth-1" controls=true %}
    </div>
</div>
<div class="caption">
    本地视频文件示例
</div>

### YouTube 视频

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include video.liquid path="https://www.youtube.com/embed/jNQXAC9IVRw" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    YouTube 视频嵌入示例
</div>

---

## 7. 音频嵌入

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include audio.liquid path="assets/audio/epicaly-short-113909.mp3" controls=true %}
    </div>
</div>
<div class="caption">
    音频播放器示例
</div>

---

## 8. 表格

### Markdown 表格

在 front matter 中添加 `pretty_table: true` 可以美化表格：

| 左对齐 | 居中 | 右对齐 |
| :----- | :--: | -----: |
| 内容 1 | 内容 2 | 内容 3 |
| 内容 4 | 内容 5 | 内容 6 |

---

## 9. 图表

### Chart.js 图表

在 front matter 中添加 `chart: { chartjs: true }` 可以启用 Chart.js 支持。

**折线图示例：**

```chartjs
{
  "type": "line",
  "data": {
    "labels": ["一月", "二月", "三月", "四月", "五月", "六月", "七月"],
    "datasets": [{
      "label": "数据量",
      "fill": false,
      "lineTension": 0.1,
      "backgroundColor": "rgba(75,192,192,0.4)",
      "borderColor": "rgba(75,192,192,1)",
      "data": [65, 59, 80, 81, 56, 55, 40]
    }]
  },
  "options": {}
}
```

**饼图示例：**

```chartjs
{
  "type": "doughnut",
  "data": {
    "labels": ["红色", "蓝色", "黄色"],
    "datasets": [{
      "data": [300, 50, 100],
      "backgroundColor": ["#FF6384", "#36A2EB", "#FFCE56"]
    }]
  },
  "options": {}
}
```

---

## 10. 其他功能

> **注意**：Twitter 嵌入和 Jupyter Notebook 功能已从本网站移除。如需使用这些功能，请参考相关插件的文档。

---

## Front Matter 配置说明

以下是常用的 front matter 字段：

```yaml
---
layout: post                    # 必须：使用 post 布局
title: 文章标题                 # 必须：文章标题
date: 2025-01-15 14:00:00      # 必须：发布日期
description: 文章描述           # 可选：文章描述
tags: 标签1 标签2              # 可选：标签（空格分隔）
categories: 分类1              # 可选：分类
thumbnail: assets/img/9.jpg    # 可选：缩略图
featured: true                  # 可选：标记为精选文章
giscus_comments: true           # 可选：启用 Giscus 评论
related_posts: true             # 可选：显示相关文章
toc:                            # 可选：目录配置
  beginning: true               # 在文章开头显示目录
chart:                          # 可选：图表配置
  chartjs: true                 # 启用 Chart.js
pretty_table: true              # 可选：美化表格
code_diff: true                 # 可选：启用代码差异显示
---
```

---

## 使用提示

1. **图片路径**：图片应放在 `assets/img/` 目录下
2. **视频路径**：视频应放在 `assets/video/` 目录下
3. **音频路径**：音频应放在 `assets/audio/` 目录下
4. **网格布局**：使用 Bootstrap 的 `row` 和 `col-sm` 类来创建响应式布局
5. **图片缩放**：添加 `zoomable=true` 参数使图片可缩放
6. **代码高亮**：支持 100+ 种编程语言的语法高亮
7. **数学公式**：使用 `$$` 包裹公式，支持 LaTeX 语法

---

## 更多资源

- [Jekyll 官方文档](https://jekyllrb.com/docs/)
- [Markdown 语法指南](https://www.markdownguide.org/)
- [MathJax 文档](https://www.mathjax.org/)
- [Chart.js 文档](https://www.chartjs.org/)

---

希望这篇指南对你有帮助！如有问题，欢迎在评论区留言。

