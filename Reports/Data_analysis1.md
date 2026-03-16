# BDD100K Exploratory Data Analysis Report

## 1. Dataset Overview & Integrity
The dataset consists of high-resolution images split into training and validation sets.

* **Split Statistics**:
    * **Train Set**: 70,000 total images; 69,863 JSON samples (137 images missing labels).
    * **Validation Set**: 10,000 images with 10,000 JSON samples.
* **Data Consistency**:
    * **Temporal Analysis**: All timestamps are 10,000, preventing time-based analysis.
    * **Label Schema**: Verified uniform schema; each bounding box has an attached label.

![Dataset Split and Schema](assets/BDD_train_val_split.png) | ![Uniform Schema](assets/uniform_schema.png)
:---:|:---:
*Training/Validation Split* | *Label Consistency & Schema*

---

## 2. Distribution & Environmental Analysis
The dataset shows significant class imbalance and varied environmental conditions.

### Key Distributions
* **Category Imbalance**: Classes like "train" are extremely underrepresented.
* **Attributes**: Metadata includes weather, scene type, time of day, and occlusion levels.

| Weather | Scene | Time of Day |
| :---: | :---: | :---: |
| ![Weather](assets/dist_weather.png) | ![Scene](assets/dist_scene.png) | ![Time](assets/dist_timeofday.png) |

### Cross-Feature Insights
* **Scene vs. Category**: Analysis of object frequency within specific environments.
* **Weather vs. Category**: Impact of weather conditions on object presence.
* **Occlusion**: Correlation between visibility and time of day.

![Scene v Cat](assets/scene_v_cat.png) | ![Weather v Cat](assets/weather_v_cat.png) | ![Time v Occlusion](assets/time_v_o.png)
:---:|:---:|:---:

---

## 3. Spatial & Geometric Analysis

### Spatial Bias
Object locations are heavily influenced by the dashboard camera's fixed position:
* **Central Band**: Cars and heavy vehicles dominate the central driving lanes.
* **Lateral Edges**: Pedestrians and bicycles appear more frequently toward the sides.
* **Traffic Infrastructure**: Lights show a wider horizontal spread than vertical.

![Spatial Bias Map](assets/spatial_bias.png)

### Bounding Box Geometry
* **Scale**: Objects categorized as Small, Medium, or Large based on COCO standards.
* **Aspect Ratio (AR)**: Car and Traffic Light samples show some AR anomalies (e.g., AR of 500), while Person/Rider categories correctly show low AR (0.3–0.5).

| COCO Scale Distribution | Aspect Ratio Boxplot |
| :---: | :---: |
| ![COCO Scale](assets/bb_coco.png) | ![AR Boxplot](assets/box_plot_ar.png) |

---

## 4. Edge Case Gallery
Visual summary of extreme scenarios found within the BDD100K dataset.

| Most Objects | Least Objects | Large Objects |
| :---: | :---: | :---: |
| ![Most](assets/i_most_obj.png) | ![Least](assets/i_least_obj.png) | ![Large](assets/i_large_obj.png) |

| Small Objects | Extreme Wide | Extreme Tall |
| :---: | :---: | :---: |
| ![Small](assets/i_small_obj.png) | ![Wide](assets/i_wide_obj.png) | ![Tall](assets/i_tall_obj.png) |