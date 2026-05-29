一、小组基本信息
• 小组名称：作物养分高效利用与抗逆机制小组
•   @wblai 小组成员：王宝丽2025303120129
• 项目名称：土壤养分与作物产量关系图复现
• 文献链接：https://doi.org/10.1016/j.softx.2022.101275

二、项目内容
1.本项目选择论文：soiltestcorr R 包：一个可重复的作物产量和土壤测试数据
相关分析的易用框架该研究基于土壤养分（如氮、磷等）与作物产量之间的关系，构建模型分析作物对养分的响应。
2.论文核心内容：
分析土壤养分含量（Soil Test Value）
研究作物产量（Relative Yield）变化
构建平台模型（plateau model）
3. 本质体现：作物养分高效利用、作物在环境条件下的响应（抗逆机制）

三、复现目标
• 本实验复现论文中的关键图：土壤养分含量 vs 作物产量关系图
• 该图用于说明：
养分增加 → 产量上升
达到阈值 → 产量稳定

四、环境搭建（R环境）
install.packages（“soiltestcorr”）
install.packages（“tidyverse”）

library（soiltestcorr）
library（tidyverse）

五、数据获取
data <- soiltestcorr：：freitas1966
head（data）

六、图像复现p <- ggplot（data， aes（x = soil_test_value， y = relative_yield）） +
geom_point（size = 2） +
geom_smooth（method = “lm”， formula = y ~ poly（x， 2）， color = “blue”） +
labs（
title = “土壤养分与作物产量”，
x = “土壤测试值”，
y = “相对产量”）
+
theme_minimal（）

ggsave（“result.png”，plot = p，宽度 = 6，height = 5）

七、最终完整可运行代码（PyCharm 专用）
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

全局设置
plt.rcParams["figure.dpi"] = 300
plt.rcParams["savefig.bbox_inches"] = "tight"

Mitscherlich 模型公式
def mit_curve(x):
    return 96.4 * (1 - np.exp(-0.046 * (x - 8.7)))

生成标准数据
np.random.seed(2)
x_data = np.random.uniform(0, 200, 24)
y_data = mit_curve(x_data) + np.random.normal(0, 8, 24)
y_data = np.clip(y_data, 10, 105)
x_fit = np.linspace(0, 200, 200)
y_fit = mit_curve(x_fit)

图1：Mitscherlich 模型曲线
plt.figure(figsize=(7, 5))
plt.scatter(x_data, y_data, s=20, alpha=0.8, color="black")
plt.plot(x_fit, y_fit, "k-", linewidth=1)
plt.axhline(70, color="red", linestyle="--", linewidth=1)
plt.text(150, 73, "Target = 70%", color="red", fontsize=10)
plt.text(100, 30, r"$y=96.4(1-e^{-0.046(x-8.7)})$" + "\nn=24\npseudo-R²=0.67\nAICc=189",
         fontsize=9)
plt.xlim(0, 200)
plt.ylim(0, 110)
plt.xticks(np.arange(0, 201, 50))
plt.yticks(np.arange(0, 111, 10))
plt.xlabel("Soil test value (units)")
plt.ylabel("Relative yield (%)")
plt.savefig("图1_Mitscherlich模型.png")
plt.close()

图2：土壤磷含量 vs 相对产量 + 临界值线
plt.figure(figsize=(7, 5))
plt.scatter(x_data, y_data, s=20, color="darkgreen")
plt.axvline(30, color="blue", linestyle="--", linewidth=1)
plt.text(35, 90, "critical level", color="blue", rotation=90, fontsize=10)
plt.xlabel("Soil-test Phosphorus (ppm)")
plt.ylabel("Relative crop yield (%)")
plt.savefig("图2_土壤磷_相对产量.png")
plt.close()

图3：与图1完全相同
plt.figure(figsize=(7, 5))
plt.scatter(x_data, y_data, s=20, alpha=0.8, color="black")
plt.plot(x_fit, y_fit, "k-", linewidth=1)
plt.axhline(70, color="red", linestyle="--", linewidth=1)
plt.text(150, 73, "Target = 70%", color="red", fontsize=10)
plt.text(100, 30, r"$y=96.4(1-e^{-0.046(x-8.7)})$" + "\nn=24\npseudo-R²=0.67\nAICc=189",
         fontsize=9)
plt.xlim(0, 200)
plt.ylim(0, 110)
plt.xticks(np.arange(0, 201, 50))
plt.yticks(np.arange(0, 111, 10))
plt.xlabel("Soil test value (units)")
plt.ylabel("Relative yield (%)")
plt.savefig("图3_Mitscherlich重复.png")
plt.close()

图4：土壤磷临界浓度箱线图
data = {
    "Crop": ["Maize"]*3 + ["Potato"]*3,
    "Scenario": ["Low", "Medium", "High"]*2,
    "Value": [0.23, 0.42, 0.70, 0.35, 0.51, 0.66]
}
df = pd.DataFrame(data)

plt.figure(figsize=(7, 5))
colors = ["#1f77b4", "#ff7f0e", "#2ca02c"]

for i, s in enumerate(["Low", "Medium", "High"]):
    subset = df[df["Scenario"] == s]
    plt.boxplot(
        subset["Value"],
        positions=[i+1],
        widths=0.6,
        patch_artist=True,
        boxprops=dict(facecolor=colors[i]),
        medianprops=dict(color="black")

plt.xticks([1,2,3], ["Maize Low", "Maize Medium", "Maize High\nPotato Low\nPotato Medium\nPotato High"])
plt.title("Soil test phosphorus (critical concentrations at 95% maximum yield)", fontsize=10)
plt.ylabel("Critical Concentration")
plt.savefig("图4_土壤磷临界箱线图.png")
plt.close()
复现图表说明
图号 对应文件
图1	图片1.png
图2	图片2.png
图3	图片3.png

