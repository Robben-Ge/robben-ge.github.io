---
layout: page
title: 数学建模竞赛
description: 使用 MATLAB 进行数据分析和数学建模，包括数据预处理、统计分析、多元线性回归等
img: 
importance: 2
category: work
---

## 项目简介

数模是一时兴起打算去打的，以前也算是有过数据分析的经验吧，打过泰迪杯啥的，但是其实数据处理当时是用 Python，这次用的是 MATLAB，发现 MATLAB 是真好用，但是从几乎是零开始也挺难的，不过面对 ChatGPT 编程就好多了，三天比赛也算是学了很多。

## 主要工作

### 1. 数据预处理

将附件二导入 MATLAB，获取销售流水明细数据，以获取每个商品的销售记录，检查数据是否包含缺失值、异常值，并进行必要的数据清洗和处理，以确保数据的质量。

```matlab
%导入附件2
data = readtable('数值化.xlsx');

% 获取列名
column_names = data.Properties.VariableNames;

% 使用 renamevars 函数将列名更改为原列名
data = renamevars(data, {'x____','x______','x_____1','x_______1','x__________','x_____2','x_______2','x___'}, 
                  {'销售日期','扫码销售时间','单品编码','销量(千克)','销售单价(元/千克)','销售类型','是否打折销售','销售额'});

%导入附件1
data1 = readtable('附件1.xlsx');
data1 = renamevars(data1, {'x____', 'x_____1','x_____2','x_____3'}, 
                   {'单品编码','单品名称','分类编码','分类名称'});

%data2用于储存单品编码与单品名称的映射
data1 = data1(:, {'单品编码', '单品名称','分类名称'});

%将单品名称映射进data中
data2 = join(data, data1, 'Keys', '单品编码');

%将单品名称移到开头
data2 = data2(:, {'单品名称','销售日期','分类名称','扫码销售时间','单品编码','销量(千克)','销售单价(元/千克)','是否打折销售','销售额','销售类型'});

sales = data2.('销量(千克)');

%删除销量为负值的数据，退货不在考虑范围
valid_rows = sales >= 0;
data2 = data2(valid_rows, :);

writetable(data2, '12合并去负值.xlsx');
```

### 2. 合并品类、单品

```matlab
summaryTable = groupsummary(data2, '分类名称', 'sum', '销量(千克)');
pinglei = summaryTable(:, {'分类名称', 'sum_销量(千克)'});
pinglei = renamevars(pinglei, {'分类名称', 'sum_销量(千克)'}, {'pinleimingcheng', 'Total_Sales_KG'});
writetable(pinglei, '品类与销量.xlsx');

summaryTable = groupsummary(data2, '单品名称', 'sum', '销量(千克)');
danpin = summaryTable(:, {'单品名称', 'sum_销量(千克)'});
danpin = renamevars(danpin, {'单品名称', 'sum_销量(千克)'}, {'danpinmingcheng', 'Total_Sales_KG'});
writetable(danpin, '单品与销量.xlsx');
```

### 3. 数据可视化

#### 折线图

```matlab
x = 1:length(pinglei.pinleimingcheng);
plot(x, pinglei.Total_Sales_KG, '-o', 'LineWidth', 2, 'MarkerSize', 8);
xticks(x);
xticklabels(pinglei.pinleimingcheng);
title('商品销售量');
xlabel('商品品类');
ylabel('销售量 (千克)');
legend('销售量');
set(gcf, 'Position', [100, 100, 800, 400]);
```

#### 箱线图

```matlab
%绘制销量的箱线图，发现销量带负值，为退货，有160kg销售鲜粽叶的特殊值
sales = data2.('销量(千克)');
boxplot(sales);
title('销量(千克)的箱线图');
ylabel('销量(千克)');

%绘制销售单价的箱线图
sales1 = data2.('销售单价(元/千克)');
boxplot(sales1);
title('销售单价(元/千克)的箱线图');
ylabel('销售单价(元/千克)');
```

### 4. 异常值识别

使用 IQR（四分位距）方法识别异常值：

```matlab
outliers = false(size(data3));  % 创建与数据表格相同大小的逻辑数组

for col = 2:size(data3, 2)  % 从第二列开始（第一列通常是标签）
    column_data = data3{:, col};  % 获取当前列数据
    Q1 = quantile(column_data, 0.25);  % 计算下四分位数
    Q3 = quantile(column_data, 0.75);  % 计算上四分位数
    IQR = Q3 - Q1;  % 计算IQR
    
    k = 1.5;  % 调整异常值的阈值
    lower_threshold = Q1 - k * IQR;  % 下界阈值
    upper_threshold = Q3 + k * IQR;  % 上界阈值
    
    % 识别异常值
    outliers(:, col) = column_data < lower_threshold | column_data > upper_threshold;
end
```

### 5. 相关性分析

计算斯皮尔曼相关系数、绘制热力图：

```matlab
% 按品类每日汇总销售量
category_sales = varfun(@sum, data2, 'GroupingVariables', {'time','sortname'},'InputVariables', 'xiaoliang');
% 按单品每日汇总销售量
category_sales1 = varfun(@sum, data2, 'GroupingVariables', {'time','danpingname'},'InputVariables', 'xiaoliang');

% 计算相关性
correlation_matrix = corr(table2array(category_sales));
```

### 6. 时间序列分析

分析单品在时间上的分布规律：

```matlab
sortname = category_sales.sortname;
time = category_sales.time;
sum_xiaoliang = category_sales.sum_xiaoliang;

unique_sortnames = unique(sortname);

figure;
hold on;

% 遍历每个不同的sortname值，绘制不同的线段
for i = 1:length(unique_sortnames)
    current_sortname = unique_sortnames{i};
    mask = strcmp(sortname, current_sortname);
    plot(time(mask), sum_xiaoliang(mask), 'DisplayName', current_sortname);
end

title('不同品类的销售量折线图');
xlabel('时间');
ylabel('销量');
legend('Location', 'best');
hold off;
```

### 7. 多元线性回归

```matlab
% 合并数据
data3 = readtable('附件3.xlsx');
data3 = renamevars(data3, {'x__', 'x____','x__________'}, {'time','singlema','pifajia'});
mergedData = join(data2, data3, 'Keys',{'time','singlema'});

% 进行多元线性回归分析
% ...
```

### 8. 利润计算

```matlab
%首先计算出收益
data.shouyi = (data.xiaoliang .* data.danjia) - (data.xiaoliang .* data.pifajia ./ (1 - data.sunhao ./100));

%通过时间与单品名称聚类，计算每日单品收益和
category_sales = varfun(@sum, data, 'GroupingVariables', {'time','danpingname'},'InputVariables', 'shouyi');
%通过时间与单品名称聚类，计算每日单品销量和
category_sales1 = varfun(@sum, data, 'GroupingVariables', {'time','danpingname'},'InputVariables', 'xiaoliang');
%合并
mergedData = join(category_sales1, category_sales, 'Keys',{'time','danpingname'});
```

## 技术栈

- **MATLAB**: 主要开发工具
- **数据分析**: 数据清洗、预处理、统计分析
- **数据可视化**: 折线图、箱线图、热力图
- **统计方法**: IQR 异常值检测、相关性分析、多元线性回归

## 项目总结

通过这次数学建模竞赛，我深入学习了：
- MATLAB 数据处理和分析
- 数据清洗和预处理技术
- 统计分析方法
- 数据可视化
- 多元线性回归模型
- 时间序列分析

虽然是从零开始学习 MATLAB，但通过 ChatGPT 辅助编程和三天的高强度学习，最终完成了比赛并获得了宝贵的经验。

