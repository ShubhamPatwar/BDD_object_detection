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
 ![BDD_train_val_split](assets/BDD_train_val_split.png)



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

- Checking for Spatial Biasness
  ![BDD_train_val_split](assets/spatial_bias.png)

  - Most road participants appear in the central horizontal band of the image due to the forward facing dashboard camera
  - Cars appear near the central driving lane directly ahead of the  image capturing dashboard camera
  - Traffic ligths are spread more in left and right , that is horizontal spread is more than vertical , due to fact that they are       located    along roadside infrastructure rather than in the driving lane. 
  - Pedestrians appear more frequently toward the lateral
  - Riders and bicycles occupy similar spatial regions as pedestrians, typically near road edges rather than the central driving lane.
  - Heavy vehicles share spatial distribution patterns with cars because they occupy the same road lanes
  - The train class is extremely underrepresented, indicating a strong class imbalance in the dataset.
  - Motor bikes are utilizing left , right , centre as they behave as bicycle and cars both  , however biccyle is just on the left and right , same as pedestrians



- Bounding box area distribution
![BDD_train_val_split](assets/bb_traffic_light.png)
![BDD_train_val_split](assets/bb_traffic_sign.png)
![BDD_train_val_split](assets/bb_car.png)
![BDD_train_val_split](assets/bb_person.png)
![BDD_train_val_split](assets/bb_truck.png)
![BDD_train_val_split](assets/bb_rider.png)
![BDD_train_val_split](assets/bb_bike.png)
![BDD_train_val_split](assets/bb_train.png)
![BDD_train_val_split](assets/bb_motor.png)
![BDD_train_val_split](assets/bb_bus.png)


 - we can see all at once , it displayes as 
 ![BDD_train_val_split](assets/bb_all.png)
 - On the basis of COCO , defining the area as Small , Medium , Large , below analysis is shown 
 ![BDD_train_val_split](assets/bb_coco.png)

- Aspect Ratio distribution 
 - The following distribution of aspect ratio tells the whole story 
 ![BDD_train_val_split](assets/ar_traffic_light.png)
![BDD_train_val_split](assets/ar_traffic_sign.png)
![BDD_train_val_split](assets/ar_car.png)
![BDD_train_val_split](assets/ar_person.png)
![BDD_train_val_split](assets/ar_truck.png)
![BDD_train_val_split](assets/ar_rider.png)
![BDD_train_val_split](assets/ar_bike.png)
![BDD_train_val_split](assets/ar_train.png)
![BDD_train_val_split](assets/ar_motor.png)
![BDD_train_val_split](assets/ar_bus.png) 

 - Boxplot as part of the summary gives info 
 ![BDD_train_val_split](assets/box_plot_ar.png) 

    from this , it is clearly interpreted , that particularly in car , the y-axis has stretched so far that the actual "boxes" for most categories look like flat lines near zero.
    - The Car at 500: This means the bounding box is 500 times wider than it is tall.
    - A traffic light is usually vertical (Tall). An AR of 100 means it's a super-wide horizontal line. This is likely another data artifact.
    - Person/Rider: Notice these are very "low" and flat. This is correct—humans are taller than they are wide, so their AR should be small (usually around 0.3 to 0.5).


- Some Interesting Images
    - image with most objects
      ![BDD_train_val_split](assets/i_most_obj.png) 
    - image with least objects
      ![BDD_train_val_split](assets/i_least_obj.png) 
    - image with largest objects
      ![BDD_train_val_split](assets/i_large_obj.png) 
    - image with samllest objects
      ![BDD_train_val_split](assets/i_small_obj.png)  
    - image with extreme wide objects
      ![BDD_train_val_split](assets/i_wide_obj.png) 
    - image with extreme tall objects
      ![BDD_train_val_split](assets/i_tall_obj.png) 

      


















