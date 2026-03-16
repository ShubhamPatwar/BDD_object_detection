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

![ClassDist](assets/bdd_category_split.png)

### Key Distributions
* **Category Imbalance**: Classes like `train`, `motor`, `rider` are extremely underrepresented.
* **Attributes**: Metadata includes weather, scene type, time of day, and occlusion levels.

| Weather | Scene | Time of Day |
| :---: | :---: | :---: |
| ![Weather](assets/dist_weather.png) | ![Scene](assets/dist_scene.png) | ![Time](assets/dist_timeofday.png) |

### Cross-Feature Insights
* **Scene vs. Category**: Analysis of object frequency within specific environments.
* **Weather vs. Category**: Impact of weather conditions on object presence.
* **Occlusion**: Correlation between visibility and time of day.

| scene v/s categories | weather v/s categories | time v/s categories | 
| ---- | ---- | ---- |
![Scene v Cat](assets/scene_v_cat.png) | ![Weather v Cat](assets/weather_v_cat.png) | ![Time v Occlusion](assets/time_v_o.png)

---

## 3. Spatial & Geometric Analysis

### Spatial Bias
Object locations are heavily influenced by the dashboard camera's fixed position:
* **Central Band**: Cars and heavy vehicles dominate the central driving lanes.
* **Lateral Edges**: Person, rider, bikes appear more frequently toward the sides.
* **Traffic Infrastructure**: Traffic Lights and Traffic Signs show a wider horizontal spread than vertical.

![Spatial Bias Map](assets/spatial_bias.png)

### Bounding Box Geometry
* **Scale**: There are more small objects than medium objects. There are more medium objects than large objects.
* **Aspect Ratio (AR)**: Car and Traffic Light samples show some AR anomalies (e.g., AR of 500), while Person/Rider categories correctly show low AR (0.3–0.5).

| Scale Distribution | Aspect Ratio Boxplot |
| :---: | :---: |
| ![COCO Scale](assets/bb_coco.png) | ![AR Boxplot](assets/box_plot_ar.png) |

---

## 4. Sample Gallery
Visual summary of interesting scenarios found within the BDD100K training dataset.

| Atribute | Image |
| -------- | ----- |
| Most Objects | ![Most](assets/i_most_obj.png) |
| Least Objects | ![Least](assets/i_least_obj.png) |
| Large Objects | ![Large](assets/i_large_obj.png) |
| Small Objects | ![Small](assets/i_small_obj.png) |
| Extreme Wide | ![Wide](assets/i_wide_obj.png) |
| Extreme Tall | ![Tall](assets/i_tall_obj.png) |

## 5. Key Insights
1. The dataset is extremely long-tailed, with over represntation of car and underrepresentation of classes like rider, train, bike, motor.
2. There is imbalance in the background setting of images w.r.t weather, scenes and time of day. Majority of samples are from clear weather and city scene and daytime.
3. Spatial  bias exist for majority of classes where central band is taken up by cars, bus, truck lateral edges are consumed by rider , bike and motor and train shows least bias.
4. Aspect ratio analysis shows anomalies for certain bounding boxes like car and traffic light which can be seen in the images above. 