# Model Analysis
Find the model analysis in this [notebook](notebooks/model_analysis.ipynb)

Notable points regarding model selection :
- used model `PekingU/rtdetr_r50vd_coco_o365` pretrained on COCO Object detection dataset. 
- due to missing classes in COCO from BDD like `traffic lights` and `traffic signs` , evaluation on this classes are missing .
- model shows `Precision = 0.93
              Recall = 0.25` which is evident since the model has not been trained on BDD hence the recall is low and precision is high.

- some images 
 - as we can see model missed most of the data 
   ![BDD_train_val_split](assets/ma_missed.png) 
   ![BDD_train_val_split](assets/ma_missed_2.png)  , but whatever captures , its Iou is very high


- Analysis of model output
  - this is how differnet distributions are giving insights
  ![BDD_train_val_split](assets/ma_dist.png)
  - with mAP
  ![BDD_train_val_split](assets/ma_mAP.png)

# Model Evaluation Results

## COCO Evaluation Metrics

### Average Precision (AP)

| Metric | Value |
|------|------|
| AP @[ IoU=0.50:0.95 | area=all | maxDets=100 ] | **0.122** |
| AP @[ IoU=0.50 | area=all | maxDets=100 ] | **0.173** |
| AP @[ IoU=0.75 | area=all | maxDets=100 ] | **0.132** |
| AP @[ IoU=0.50:0.95 | area=small | maxDets=100 ] | **0.014** |
| AP @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] | **0.116** |
| AP @[ IoU=0.50:0.95 | area=large | maxDets=100 ] | **0.320** |

### Average Recall (AR)

| Metric | Value |
|------|------|
| AR @[ IoU=0.50:0.95 | area=all | maxDets=1 ] | **0.098** |
| AR @[ IoU=0.50:0.95 | area=all | maxDets=10 ] | **0.143** |
| AR @[ IoU=0.50:0.95 | area=all | maxDets=100 ] | **0.143** |
| AR @[ IoU=0.50:0.95 | area=small | maxDets=100 ] | **0.013** |
| AR @[ IoU=0.50:0.95 | area=medium | maxDets=100 ] | **0.137** |
| AR @[ IoU=0.50:0.95 | area=large | maxDets=100 ] | **0.370** |

---

## Detection Metrics Summary

| Metric | Score |
|------|------|
| **Precision** | **0.929** |
| **Recall** | **0.2474** |
| **F1 Score** | **0.3907** |

---

## Key Observations

- The model achieves **high precision (0.929)**, indicating that most predicted detections are correct.
- **Recall is relatively low (0.2474)**, suggesting that many objects in the dataset are not detected.
- Detection performance improves significantly for **large objects (AP = 0.320)**.
- Performance on **small objects is very poor (AP = 0.014)**, which is a common challenge in object detection tasks.
- Overall **F1 score of 0.39** indicates a trade-off between precision and recall.

## Confusion matrix 
- this is how confusion matrix looks like
![BDD_train_val_split](assets/ma_confusion.png)


## Error analysis
- Error analysis shows like this
![BDD_train_val_split](assets/ma_error.png)


## Performance of model on diff Scenes and Sizes

- performance on Diff scenes
  - this image needs to be changed
  - ![BDD_train_val_split](assets/ma_p_scenes.png)

- performance on Diff Object sizes
  ![BDD_train_val_split](assets/ma_p_object.png)
  
  - PR Curve for object sizes
    ![BDD_train_val_split](assets/ma_p_object_pr.png)
  - IOU distribution for it
    ![BDD_train_val_split](assets/ma_p_object_iou.png)
  - Positive rate analysis
    ![BDD_train_val_split](assets/ma_p_object_pos_rate.png)
  - Positive Rate grouped by size
    ![BDD_train_val_split](assets/ma_p_object_pos_rate_size.png)

## Model Bias Analysis
  - Class level Bias
    ![BDD_train_val_split](assets/ma_class_level_bias.png)
  - Spatial level Bias
    ![BDD_train_val_split](assets/ma_spatial_level_bias.png)
  - Attribute level Bias
    ![BDD_train_val_split](assets/ma_attribute_level_bias.png)

  - Checking for FP Bias
    ![BDD_train_val_split](assets/ma_fp_analysis.png)

    


