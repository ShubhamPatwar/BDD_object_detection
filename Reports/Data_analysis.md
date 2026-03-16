# Install Environment

```bash
pip install -r requirements.txt
```

# Data Analysis
Find data analysis in this [notebook](notebooks/data_analysis.ipynb)
- Data analysis is done on train set of BDD with 70000 images , but on anlaysis came to  know some 137 images , labelling is missing.
- no of train samples jsons  69863
no of val samples jsons  10000
 checked , no. of images in train folder 70000
 checked , no. of images in val folder 10000

<!-- ![BDD_train_val_split](assets/BDD_train_val_split.png) -->

<p align="center">
  <img src="assets/BDD_train_val_split.png" width="400">
</p>

- Distribution of category in dataset is given as
![BDD_train_val_split](assets/bdd_category_split.png)
This shows that classes are imbalanced , so training detector on low data count will be hard

- Also time stamp all the images is showing as 10000 , so no possiblity of some time based analysis ( temporal)

- Verified that there are no label missing , for each image , each bounding boxes , there is lable attach ,also for all images , scene level and object level information is provided in json file.
![BDD_train_val_split](assets/uniform_schema.png)

- Distribution of weather is given as 
![BDD_train_val_split](assets/dist_weather.png)

- Distribution of Scene is given as 
![BDD_train_val_split](assets/dist_scene.png)

- Distribution of time of the day is given as
![BDD_train_val_split](assets/dist_timeofday.png)

- Distribution of occluded dataset
![BDD_train_val_split](assets/dist_occluded.png)

- Distribution of traffic light color 
![BDD_train_val_split](assets/dist_trafficlightcolor.png)

- Viewing the data in cross with another data can give best insights
    - Scene V/s category
    ![BDD_train_val_split](assets/scene_v_cat.png) 

    - Weather V/S category
    ![BDD_train_val_split](assets/weather_v_cat.png) 

    - Time of day v/s Occluded
    ![BDD_train_val_split](assets/time_v_o.png)

- Some interesting images are shown below
