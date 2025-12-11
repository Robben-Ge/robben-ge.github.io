---
layout: page
title: 项目功能使用指南
description: 这是一篇综合指南，展示了项目页面中可以使用的所有功能
img: assets/img/12.jpg
importance: 1
category: work
giscus_comments: true
related_publications: true
---

这是一篇综合指南，展示了在项目页面中可以使用的所有功能。你可以参考这个项目来了解如何创建和展示你的项目。

## 项目 Front Matter 配置

项目文件使用以下 front matter 配置：

```yaml
---
layout: page                    # 必须：使用 page 布局
title: 项目名称                 # 必须：项目标题
description: 项目描述           # 必须：项目简短描述
img: assets/img/12.jpg          # 可选：项目封面图（在项目列表页显示）
importance: 1                   # 可选：重要性（数字越大，显示越靠前）
category: work                  # 可选：分类（work 或 fun，需与 projects.md 中的 display_categories 匹配）
giscus_comments: true           # 可选：启用 Giscus 评论
related_publications: true      # 可选：显示相关出版物
redirect: https://example.com   # 可选：重定向到外部链接
---
```

## 1. 项目封面图

在 front matter 中设置 `img` 字段，可以在项目列表页显示封面图：

```yaml
img: assets/img/12.jpg
```

如果不设置或设置为空，项目将没有封面图：

```yaml
img:
# 或
img: null
```

## 2. 图片网格布局

项目页面支持灵活的图片网格布局，可以创建 1/3、2/3 或全宽度的图片展示。

### 三列等宽布局

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1.jpg" title="示例图片 1" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="示例图片 2" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="示例图片 3" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    三张图片等宽并排显示，每张占 1/3 宽度。
</div>

### 单张全宽图片

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="示例图片" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    单张图片全宽显示。
</div>

### 2/3 + 1/3 布局

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="示例图片" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="示例图片" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    左侧图片占 2/3 宽度，右侧图片占 1/3 宽度。
</div>

### 1/3 + 2/3 布局

<div class="row justify-content-sm-center">
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="示例图片" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="示例图片" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    左侧图片占 1/3 宽度，右侧图片占 2/3 宽度。
</div>

## 3. 图片说明（Caption）

每行图片后可以添加说明文字：

```html
<div class="caption">
    这里是图片说明文字
</div>
```

## 4. 文本内容

你可以在图片之间插入文本内容，描述项目的各个方面：

这是项目的主要功能描述。你可以在这里详细介绍项目的背景、目标、实现过程等。

### 技术栈

- **编程语言**：Python, C++
- **框架**：ROS, TensorFlow
- **工具**：Git, Docker

### 项目特点

1. 特点一：描述项目的第一个特点
2. 特点二：描述项目的第二个特点
3. 特点三：描述项目的第三个特点

## 5. 代码示例

你可以在项目页面中展示代码：

```python
def main():
    print("Hello, World!")
    return True

if __name__ == "__main__":
    main()
```

## 6. 引用

如果需要引用相关文献，可以使用：

这是一个引用示例 {% cite einstein1950meaning %}。

## 7. 混合内容布局

你可以在图片和文本之间自由组合，创建丰富的展示效果：

这是项目的第一部分介绍。

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1.jpg" title="示例图片" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="示例图片" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    项目中间阶段的展示。
</div>

这是项目的第二部分介绍，可以继续添加更多内容。

## 8. 项目分类

项目可以分类为 `work`（工作项目）或 `fun`（趣味项目），需要在 `_pages/projects.md` 中的 `display_categories` 中配置。

```yaml
category: work  # 工作项目
# 或
category: fun   # 趣味项目
```

## 9. 项目重要性排序

使用 `importance` 字段控制项目在列表页的显示顺序，数字越大，显示越靠前：

```yaml
importance: 1  # 显示在最后
importance: 5  # 显示在最前
```

## 10. 评论功能

启用 Giscus 评论功能：

```yaml
giscus_comments: true
```

## 11. 相关出版物

如果项目有相关出版物，可以启用：

```yaml
related_publications: true
```

## 12. 重定向功能

如果项目需要重定向到外部网站：

```yaml
redirect: https://example.com
```

## 代码模板

以下是创建新项目的完整模板：

```yaml
---
layout: page
title: 你的项目名称
description: 项目的简短描述
img: assets/img/your-image.jpg
importance: 1
category: work
giscus_comments: false
related_publications: false
---

# 项目介绍

这里是项目的详细介绍...

## 功能特点

- 特点 1
- 特点 2
- 特点 3

## 图片展示

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/your-image.jpg" title="图片标题" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    图片说明
</div>

## 技术实现

这里描述技术实现细节...

## 项目链接

- [GitHub](https://github.com/your-repo)
- [演示](https://your-demo.com)
```

## 使用提示

1. **图片路径**：图片应放在 `assets/img/` 目录下
2. **响应式设计**：使用 Bootstrap 网格系统，图片会自动适配不同屏幕尺寸
3. **图片样式**：
   - `img-fluid`：使图片响应式
   - `rounded`：圆角
   - `z-depth-1`：阴影效果
4. **加载优化**：使用 `loading="eager"` 可以优先加载首屏图片
5. **图片标题**：`title` 属性会在鼠标悬停时显示

## Bootstrap 网格系统

项目使用 Bootstrap 4 的网格系统：

- `row`：行容器
- `col-sm`：小屏幕及以上等宽列
- `col-sm-4`：小屏幕及以上占 4/12（1/3）宽度
- `col-sm-8`：小屏幕及以上占 8/12（2/3）宽度
- `mt-3`：上边距
- `mt-md-0`：中等屏幕及以上移除上边距
- `justify-content-sm-center`：小屏幕及以上居中对齐

更多信息请参考 [Bootstrap 网格系统文档](https://getbootstrap.com/docs/4.4/layout/grid/)。

---

希望这个指南对你有帮助！你可以参考这个项目来创建自己的项目页面。

