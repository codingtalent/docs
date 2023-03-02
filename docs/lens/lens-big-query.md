# Lens BigQuery数据分析入门

## 前言
数据集各个表的介绍请看官方的文档
- [Lens Public BigQuery](https://docs.lens.xyz/docs/public-big-query)

## 准备
- 使用google账号登陆[BigQuery控制台][https://console.cloud.google.com/bigquery]

- 控制台内新建项目
![Create project](/docs/lens/images/create-project.png)

- 选择刚创建的项目并进入SQL工作区
![Enter sql workspace](/docs//lens//images/enter-big-query.png)

- 然后我们可以在编辑器中使用sql查询对应数据集的数据了。探索器(Explorer)中无法搜索到lens-public-data下的数据集，暂时不清楚原因，但目前只能通过在浏览器地址栏里加上参数进行来搜索lens的公开数据集。参数`p`为项目名称，我们指定`lens-public-data`。`d`为数据集id，我们指定查看`polygon`这个数据集。`page`指定类型为`dataset`。
例子：`https://console.cloud.google.com/bigquery?project=lens-analyze&p=lens-public-data&d=polygon&page=dataset`

## 实践案例
现在我们尝试对lens不同app下的publication进行统计，并生成可视化的图表。

1. 我们使用`lens-public-data.polygon.public_app_publication_stats`表来汇总lens下不同app发布的publication数量，并根据publication数量从大到小排序
    ```
    select app_id,
    count(*) as app_publications_count
    from `lens-public-data.polygon.public_app_publication_stats`
    group by 1
    order by 2 desc;
    ```
    ![Count publication by app](/docs//lens//images//count-lens-publication-by-app.png)

2. 接着我们使用这个查询来生成图表，Big Query目前支持三种方式来展示数据：`表格`(类似Excel)、`Looker Studio`(支持一些常用图表来对数据进行可视化处理)以及`Colab`(使用Python来处理数据并生成可视化图表)。我们这次使用`Looker Studio`来生成可视化图表。进入`Looker Studio`后就可以看到它自动帮我们生成了一个表格和一个柱状图，具体如下图：
![Looker Studio introduction](/docs//lens//images/looker-studio-introduction.png)
    1. 表示数据集，也即我们上面的sql执行后的结果集

    2. 可以点击来添加数据集，支持从我们的project里的数据集里获取数据，也支持通过查询来获取数据，这里我们暂时不需要新增，所以不多赘述。
    ![Add data source in Looker Studio](/docs//lens//images//add-data-source-in-looker-studio.png)

3. 接着我们对柱状图进行修正，因为它现在的x轴使用的是publication的汇总数量，我们将它调整为使用`app_id`。
    1. 首先点击柱状图，然后右侧会显示图表相关的属性，可以看到维度这里用的是`app_publications_count`，我们点击它下，并选择`app_id`。
    ![App publications chart](/docs//lens//images/app-publications-chart.png)
    可以看到x轴已经按照预期更新了。

    2. 由于柱状图默认只显示10条柱子，如果我们希望增加显示，例如显示20条，可以在点击`样式`这个tab，修改条形图的柱子条数。
    ![App publication bar chart](/docs//lens//images//app-publication-bar-chart.png)

    3. 最后我们在每根柱子上都显示对应的数量，并将数字紧凑化
    ![App publication bar chart2](/docs//lens//images//app-publication-bar-chart2.png)

4. 接着我们再插入一个饼状图，这样会更直观的看出每个app的publication的份额比例
    1. 首先左上菜单里点击`插入->饼图`
    ![App publication pie chart](/docs//lens//images//app-publication-pie-chart.png)

    2. `Looker Studio`自动帮我们配置好了饼图的相关参数
    ![App publication pie chart](/docs//lens//images//app-publication-pie-chart2.png)

最终结果：
![App publication chart](/docs//lens//images//app-publication-result.png)
从实践来看，`Looker Studio`其实类似MS的Word文档，可以比较方便地使用一些基础的图表来展示数据集/sql的结果，并以此来进行一些分析。

## 参考
- [Lens Public BigQuery Docs](https://docs.lens.xyz/docs/public-big-query)
- [Google BigQuery Docs](https://cloud.google.com/bigquery/docs/introduction?hl=en)

