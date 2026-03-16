

# Model Analysis
Find the model analysis in this [notebook](notebooks/model_analysis.ipynb)

Notable points regarding model selection :
- used model `PekingU/rtdetr_r50vd_coco_o365` pretrained on COCO Object detection dataset. 
- due to missing classes in COCO from BDD like `traffic lights` and `traffic signs` , evaluation on this classes are missing .
- model shows `Precision = 0.93
              Recall = 0.25` which is evident since the model has not been trained on BDD hence the recall is low and precision is high.

- performance on diff scenes/sizes/ 
- check for biasness
- mAP [0.5: 0.95]
