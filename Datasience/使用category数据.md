# 为什么使用category数据类型
使用categoty数据可以节省大量内存，高达90%，种类越少节省的越多。
# 数据清洗
纠正错误拼写可以使用pandas的replace方法
# visualization that is ordered by the frequency counts of a Series  根据频率排序的绘图
使用一个有顺序的list对category那一列进行排序

```python
reviews["User continent"] = reviews["User continent"].cat.reorder_categories(new_categories=continent_categories)
```
# 注意事项
