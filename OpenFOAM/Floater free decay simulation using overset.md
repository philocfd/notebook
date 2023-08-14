# 注意事项
- overset会增加数值耗散，并且插值是非守恒的。
-  只有ESI版本的OpenFOAM中有overset
# 网格生成
浮体及仿真的范围如下图所示，背景网格可以采用均匀网格。
![浮体](../figs/floater_domain.jpg)
背景网格如下图所示，网格分辨率约为6m
![浮体](../figs/backgroundmesh.jpg)
浮体的x-y-z的尺寸分别为96、86、35.525，随体网格x-y-z尺寸分别定为300-300-70，单位是m，背景网格的分辨率为3m。