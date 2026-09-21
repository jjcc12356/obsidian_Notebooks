DD 十进制度
DMS度分秒
DMM/DDM 度分
UTM 通用横轴墨卡托
MGRS 军事网格参考系统

应注意的点是
- 控制点分布不均匀 RMS小不代表全图没有变形
北回归线如何制作
特殊的不规则经纬网如何制作
**空间基准与代表点构建**  
Hospital XY → Project  
Tract Polygon → Centroid

↓

**几何距离评价**  
Pairwise Distance → Euclidean / Manhattan → Nearest Hospital

↓

**道路网络成本建模**  
State Boundary → Road Clip → Speed Conversion → Time Cost → Network Dataset

↓

**网络可达性分析**  
Service Area / Closest Facility / OD Matrix

↓

**成果输出**  
服务范围图 / 最快救援路线 / ODTime.csv