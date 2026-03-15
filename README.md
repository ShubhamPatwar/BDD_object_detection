# Install Environment

```bash
pip install -r requirements.txt
```

# Data Analysis
Find data analysis in this [notebook](notebooks/data_analysis.ipynb)


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








# Model Training
Find the model training in this [notebook](notebooks/model_training.ipynb)

Training is just for demonstration of the training loop using 500 images for 5 Epoches due to resource constraint of full scale training using 4050 RTX on a Laptop

# suggested improvements
- weighted loss can be used for training on the entire training  set since data shows imbalance in category present as well as the spatial variation is very high for certain categories like `train` , which means high variance is needed to capture the features which means more parameters use , hence more weightage to the loss for the category

- 



