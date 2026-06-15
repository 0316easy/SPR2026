# SPR 2026 乳腺X线报告BI-RADS分类预测

## 项目目标
根据乳腺X线检查报告的“发现（Findings）”部分（葡萄牙语），自动预测BI-RADS风险等级（0~6级）。这是一个多类别文本分类任务。

## 数据
- 训练集：18,272 条真实临床报告，包含ID、文本、BI-RADS标签。
- 测试集：公开部分仅4条（完整测试集用于Kaggle提交）。
- 数据特点：类别极不平衡（低风险样本远多于高风险），文本已去除“印象/结论”部分。

## 预处理与特征
- 文本清洗、转小写、去停用词、词形还原
- TF-IDF向量化（5000维）
- 过采样：手动复制 + SMOTE
- 类别权重：`class_weight='balanced'`

## 模型与结果
| 模型 | 验证方式 | 宏平均F1 |
|------|----------|-----------|
| 逻辑回归 | 单次划分 | 0.81（不稳定）|
| XGBoost | 5折交叉验证 | 0.7013 |
| **LightGBM** | **5折交叉验证** | **0.7924** |

LightGBM 表现出最好的稳定性和泛化能力，5折分数波动小（0.76~0.81），无过拟合。

## 文件结构
├── data/
│ ├── train.csv # 需从Kaggle下载
│ └── test.csv
├── train_lgb.py
├── requirements.txt
└── README.md

## 运行方式
1. 克隆仓库或下载代码
2. 安装依赖：`pip install -r requirements.txt`
3. 将 Kaggle 比赛数据中的 `train.csv` 和 `test.csv` 放入 `data/` 文件夹
4. 运行训练脚本：`python train_lgb.py`
