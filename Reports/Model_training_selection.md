# Model Training

The model training process can be found in this notebook:  
[Model Training Notebook](../notebooks/model_training.ipynb)


Training is performed only to demonstrate the training loop using **500 images for 5 epochs**, due to the resource constraints of full-scale training on an **RTX 4050 laptop GPU**.

---

# Suggested Improvements

- **Weighted loss** can be used for training on the entire training set since the data shows **category imbalance**.  
- There is also **high spatial variation** for certain categories such as `train`.  
- This indicates that **higher variance is required to capture features**, which may require **more parameters**, and therefore **greater weightage in the loss for that category**.

---

## Training Behavior

Following the same **architecture / loss function / optimizer**:

### Epoch vs Loss

![Epoch vs Loss](assets/mt_e_v_l.png)