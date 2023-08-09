# boxplot 和 barplot都可以用sns.catplot()实现
boxplot和barplot分别设为kind="box"和kind="bar"
# seaborn字体大小和背景的设定

```python

sns.set(font_scale=1.3)
sns.set_style("darkgrid")

```
# visualization that is ordered by the frequency counts of a Series  根据频率排序的绘图
使用一个有顺序的list对category那一列进行排序

```python
reviews["User continent"] = reviews["User continent"].cat.reorder_categories(new_categories=continent_categories)
```