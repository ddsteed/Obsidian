
SAFER: SmArt Forecast Emergency Regulator

# 系统功能
## V 1.0
SAFER 是借助感知源（目前主要是摄像头），获取公共场所人群密度，进行人工智能预测，分析得到当前和未来的精细人群聚集密度，并以此作为主要判据之一，给出风险等级，提供安全执行预案，帮助管理者和当事人进行风险防范。

## V 2.0

# 设计思想

# 核心技术
1. 视频流取帧: OpenCV-python
2. 照片识别人群: PyTorch+YOLOV5
3. 智能预测
4. 多进程运行: multiprocessing
5. socket 通信 between slave/master
6. MySQL 数据库
7. Redis 数据库

# 流程图
