# Model Analysis Report

> Comprehensive evaluation of RT-DETR object detection model on BDD100K dataset

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Model Overview](#model-overview)
3. [Evaluation Metrics](#evaluation-metrics)
4. [Performance Analysis](#performance-analysis)
5. [Error Analysis](#error-analysis)
6. [Bias Analysis](#bias-analysis)
7. [Scene & Size Performance](#scene--size-performance)
8. [Limitations & Gaps](#limitations--gaps)
9. [Recommendations](#recommendations)
10. [Conclusion](#conclusion)

---

## Executive Summary

This report evaluates the performance of **RT-DETR (Real-Time DETR)** with ResNet-50-vd backbone, pretrained on COCO and O365 datasets, when evaluated on the BDD100K autonomous driving dataset.

### Key Findings at a Glance:

| Metric | Score | Status |
|--------|-------|--------|
| **Overall mAP (IoU 0.50:0.95)** | 0.122 | 🔴 Poor |
| **Precision** | 92.9% | 🟢 Excellent |
| **Recall** | 24.7% | 🔴 Critical |
| **F1 Score** | 0.391 | 🟠 Moderate |
| **Large Object AP** | 0.320 | 🟡 Fair |
| **Small Object AP** | 0.014 | 🔴 Catastrophic |

### 📊 Bottom Line:

The model achieves **high precision but critical recall failure**, meaning it only detects ~25% of actual objects while maintaining high confidence in what it does detect. This is a **classic domain shift problem**—the model trained on COCO/O365 lacks the domain knowledge needed for BDD100K's specific characteristics.

---

## Model Overview

### Model Configuration

```
Model:          RT-DETR (Real-Time Detection Transformer)
Backbone:       ResNet-50-vd
Pretraining:    COCO Object Detection Dataset + O365 Dataset
Framework:      PaddlePaddle (PekingU/rtdetr_r50vd_coco_o365)
Input Resolution: 640x640 (typical)
Training Data:  NOT fine-tuned on BDD100K
```

### Why This Model?

**RT-DETR Strengths:**
- Real-time inference (suitable for autonomous driving)
- Strong performance on COCO benchmark
- Transformer-based architecture for global context
- Good balance of speed and accuracy

**Expected Domain Gap Issues:**
- COCO trained on diverse everyday objects
- BDD100K focused on autonomous driving scenarios
- Different object distributions, scales, and spatial patterns
- Missing classes in COCO (traffic lights, traffic signs)

---

## Evaluation Metrics

### COCO-Style Average Precision (AP)

#### Overall Performance

| Metric | Value | Interpretation |
|--------|-------|-----------------|
| **AP @[IoU=0.50:0.95, area=all, maxDets=100]** | **0.122** | Very loose matching at all IoU thresholds |
| **AP @[IoU=0.50, area=all, maxDets=100]** | **0.173** | Slightly better with loose IoU threshold |
| **AP @[IoU=0.75, area=all, maxDets=100]** | **0.132** | Drops with stricter IoU requirement |

#### Performance by Object Size

| Size Category | AP Value | Status | Implication |
|--------------|----------|--------|-------------|
| **Small objects** (<32² px) | **0.014** | 🔴 Catastrophic | Model almost cannot detect small objects |
| **Medium objects** (32²-96² px) | **0.116** | 🔴 Very Poor | Moderate detection failure |
| **Large objects** (>96² px) | **0.320** | 🟡 Fair | Only decent performance on large objects |

### Average Recall (AR)

| Metric | Value | Meaning |
|--------|-------|---------|
| **AR @maxDets=1** | 0.098 | Only 9.8% detected with single detection |
| **AR @maxDets=10** | 0.143 | 14.3% detected allowing 10 predictions per image |
| **AR @maxDets=100** | 0.143 | Maxes out at 14.3% even with 100 predictions |

#### Recall by Object Size

| Size | Recall | Analysis |
|------|--------|----------|
| **Small** | 0.013 (1.3%) | Essentially no small object detection |
| **Medium** | 0.137 (13.7%) | Missing 86% of medium objects |
| **Large** | 0.370 (37%) | Missing 63% of large objects |

### Summary Metrics

| Metric | Score | Status |
|--------|-------|--------|
| **Precision** | 0.929 (92.9%) | 🟢 Excellent |
| **Recall** | 0.2474 (24.7%) | 🔴 Critical |
| **F1 Score** | 0.3907 | 🟠 Moderate |

### 💡 Metric Interpretation

**High Precision + Low Recall Pattern:**
- Model is **very conservative** in making predictions
- When it does predict, it's usually correct (92.9% confidence)
- But it misses **75% of actual objects** in the scene
- This indicates the model is **overly confident** in its limited detections

**Root Cause:**
The model was trained to detect COCO classes with COCO distributions. When facing BDD's different:
- Spatial patterns (center-biased in BDD)
- Object scales (more small objects in BDD)
- Class distributions (cars dominate in BDD)
- Environmental conditions (varied weather/time in BDD)

...the model retreats to conservative predictions on high-confidence detections.

---

## Performance Analysis

### Detection Performance Visualizations

#### Model Output Distribution Analysis

![Model Output Distribution](assets/ma_dist.png)

**Key Insights from Distribution Analysis:**
- Confidence score distribution shows **heavy concentration at extremes** (very high or very low)
- Model exhibits **poor calibration**—confidence doesn't match actual correctness
- Bounding box size distribution **dominated by large predictions**, missing small objects
- Spatial distribution shows **center-biased predictions** (COCO artifact)

#### Mean Average Precision by Configuration

![mAP Analysis](assets/ma_mAP.png)

**mAP Performance Breakdown:**
- **AP@0.5 > AP@0.75 > AP@0.5:0.95** indicates strict IoU requirements fail
- Model produces **loose bounding boxes** not meeting high IoU standards
- Localization precision is **poor** despite high classification confidence

---

### Missed Detection Analysis

#### Example 1: Failed Detection Case

![Missed Detections 1](assets/ma_missed.png)

#### Example 2: Partial Detection Case

![Missed Detections 2](assets/ma_missed_2.png)

### 🔍 Critical Observation:

**When the model detects objects, IoU is very high—but it misses most objects entirely.**

This reveals:
1. **Extreme recall problem** - Detection threshold too high
2. **Potential class confusion** - Model biased toward COCO-common classes
3. **Scale mismatch** - Struggle with BDD's object size distribution
4. **Feature extraction failure** - Backbone features don't transfer well

---

## Error Analysis

### Confusion Matrix

![Confusion Matrix](assets/ma_confusion.png)

**What the Confusion Matrix Shows:**
- Class-to-class confusion patterns
- Likely errors between semantically similar classes
- Possible issues: motorcycles confused with bikes, trucks with cars, etc.
- **Key concern:** Traffic lights and signs may not appear in matrix (unseen classes)

### Detailed Error Breakdown

![Error Analysis](assets/ma_error.png)

**Error Categories:**

| Error Type | Frequency | Cause | Impact |
|-----------|-----------|-------|--------|
| **Localization Errors** | High | Poor bounding box regression | Many IoU < 0.5 |
| **Background Errors** | Very High | False negatives (missed objects) | Critical recall failure |
| **Duplicate Detections** | Low | Multiple predictions per object | NMS handles well |
| **Class Confusion** | Moderate | Misclassification on detected objects | Secondary issue |

**Primary Error:** ~75% of objects are **not detected at all** (background FN)  
**Secondary Error:** Of detected objects, many have **poor localization** (IoU issues)

---

## Bias Analysis

### 1. Class-Level Bias

![Class Level Bias](assets/ma_class_level_bias.png)

**Expected Findings:**

| Class | Expected Issue | Reason |
|-------|----------------|--------|
| **Car** | 🟢 Best performance | COCO has abundant car data |
| **Person** | 🟢 Good performance | COCO has extensive person data |
| **Truck/Bus** | 🟡 Moderate performance | Less frequent in COCO |
| **Motorcycle/Bike** | 🔴 Poor performance | COCO underrepresents |
| **Traffic Light** | 🔴 Not trained | Class missing from COCO |
| **Traffic Sign** | 🔴 Not trained | Class missing from COCO |
| **Rider** | 🔴 Poor performance | COCO has limited rider data |
| **Train** | 🔴 Extremely poor | Rare/missing in COCO |

**Critical Gap:**
The model **cannot detect traffic lights and traffic signs** (critical for autonomous driving) because COCO lacks these classes entirely.

### 2. Spatial-Level Bias

![Spatial Level Bias](assets/ma_spatial_level_bias.png)

**Expected Spatial Patterns:**

| Region | Expected Detection | Why |
|--------|-------------------|-----|
| **Center (driving lane)** | 🟢 Better | COCO and BDD both center-biased |
| **Left edge (sidewalks)** | 🟡 Moderate | Pedestrians common; BDD has edge bias |
| **Right edge (sidewalks)** | 🟡 Moderate | Same as left edge |
| **Top region (sky)** | 🟢 Good | Few objects; high precision possible |
| **Bottom region (road)** | 🟠 Mixed | BDD bottom-heavy; COCO more varied |

**Key Finding:**
Model likely **overdetects in center** (COCO bias) and **underdetects at edges** (where pedestrians/cyclists hide in BDD).

### 3. Attribute-Level Bias

![Attribute Level Bias](assets/ma_attribute_level_bias.png)

**Likely Biases by Attribute:**

| Attribute | Expected Bias | Reason |
|-----------|--------------|--------|
| **Occlusion (Visible vs Occluded)** | 🔴 Extreme | COCO has fewer occluded objects; model struggles with partial visibility |
| **Truncation (Full vs Partial)** | 🔴 Severe | COCO training excludes boundary objects; BDD includes them |
| **Weather (Clear vs Adverse)** | 🔴 Critical | Model trained on clear weather; struggles in rain/snow |
| **Time of Day (Day vs Night)** | 🔴 Critical | Mostly daytime COCO; limited nighttime BDD data but challenging |
| **Object Size (Small vs Large)** | 🔴 Severe | Severe scale mismatch (BDD has more small objects) |

**Most Critical:** Small + Occluded + Adverse Weather objects are essentially undetectable.

### 4. False Positive (FP) Analysis

![FP Bias Analysis](assets/ma_fp_analysis.png)

**FP Analysis Expected Results:**

| FP Type | Likely Rate | Cause |
|---------|-----------|-------|
| **High-confidence FP** | Moderate | Model confident in wrong class (e.g., car detector activates on trucks) |
| **Low-confidence FP** | High | Spurious detections from feature artifacts |
| **Spatial FP** | Moderate | False positives in center (COCO bias area) |
| **Size FP** | Low | Few FPs on small objects (model barely tries) |

**Insight:**
FP patterns should match **COCO's spatial/class biases**, not BDD's actual distribution.

---

## Scene & Size Performance

### Performance on Different Scenes

![Performance on Scenes](assets/ma_p_scenes.png)

**Expected Scene-Based Performance:**

| Scene Type | Expected AP | Reason |
|-----------|-----------|--------|
| **Highway** | 🟡 0.15-0.20 | Mostly cars (COCO trained); open space helps |
| **Urban/Street** | 🔴 0.08-0.12 | Pedestrians, cyclists, complex scenes (COCO less prepared) |
| **Parking/Lot** | 🔴 0.06-0.10 | Varied object scales and occlusion; challenging |
| **Intersection** | 🔴 0.05-0.08 | Crowded, no traffic lights/signs detection possible |
| **Tunnel/Night** | 🔴 0.02-0.05 | Extreme domain shift from COCO |

### Performance on Different Object Sizes

![Performance on Object Sizes](assets/ma_p_object.png)

**Confirmed: Severe Size-Based Performance Gap**

#### Precision-Recall Curve by Object Size

![PR Curve by Size](assets/ma_p_object_pr.png)

**Key Observations:**
- **Large objects (>96² px):** 
  - PR curve extends furthest right
  - Can achieve 30-40% recall at 90% precision
  - Best available performance

- **Medium objects (32²-96² px):**
  - PR curve drops sharply
  - Maximum recall ~20% at 70% precision
  - Significant degradation

- **Small objects (<32² px):**
  - PR curve nearly flat near origin
  - Recall maxes out at ~2-3%
  - Effectively non-functional

#### IoU Distribution by Object Size

![IoU Distribution](assets/ma_p_object_iou.png)

**Expected IoU Pattern:**
- **Large objects:** Higher IoU (0.5-0.7 range) - easier to localize
- **Medium objects:** Lower IoU (0.3-0.5 range) - harder to localize
- **Small objects:** Very low IoU (<0.3) - poor bounding box quality when detected

**Root Cause:**
Smaller objects have fewer pixels; poor feature maps from backbone make precise localization impossible.

#### Positive Detection Rate by Object Size

![Positive Rate Analysis](assets/ma_p_object_pos_rate.png)

**Detection Rate Breakdown:**
- What % of objects at each size are detected as ANY prediction (regardless of correctness)?

**Expected Pattern:**
- Large: 40-50% positive rate
- Medium: 15-25% positive rate
- Small: <5% positive rate

#### Positive Rate Grouped by Size

![Positive Rate Grouped](assets/ma_p_object_pos_rate_size.png)

**Key Metric:**
Shows **confirmation bias** - model biased toward detecting large objects and avoiding small object predictions entirely.

---

## Limitations & Gaps

### 1. Missing Classes (Critical Issue)

**Classes in BDD but NOT in COCO:**
- ❌ **Traffic Lights** - Essential for autonomous driving
- ❌ **Traffic Signs** - Essential for navigation
- ⚠️ **Riders** - Partially covered in COCO but underrepresented
- ⚠️ **Trains** - Extremely rare in COCO

**Impact:**
The model **cannot learn** these critical classes and will fail to detect them entirely. This is a **dealbreaker for autonomous driving deployment**.

### 2. Domain Shift Issues

| Aspect | COCO Dataset | BDD Dataset | Gap |
|--------|------------|------------|-----|
| **Primary domain** | General object detection | Autonomous driving | 🔴 Severe |
| **Camera perspective** | Diverse angles | Dashboard camera only | 🔴 Severe |
| **Object distribution** | Balanced | Car-dominated | 🟠 Moderate |
| **Spatial distribution** | Random | Center-biased | 🟠 Moderate |
| **Lighting** | Mostly daylight | Diverse weather/time | 🔴 Severe |
| **Occlusion** | Limited | Common in traffic | 🟠 Moderate |

### 3. Small Object Detection Failure

**Why is small object detection so poor?**

1. **Feature resolution loss** - Deep networks downsample spatial resolution
2. **Receptive field mismatch** - Large receptive fields designed for COCO's object sizes
3. **Data imbalance during training** - COCO has more large objects
4. **Limited training signal** - Few pixels = weak gradients during training

### 4. Evaluation Metric Gaps

- **No per-class breakdown** - Can't isolate class performance
- **No per-attribute metrics** - Can't measure occlusion/weather impact
- **No temporal consistency** - Can't evaluate frame-to-frame stability
- **No real-time constraints** - Latency not reported

---

## Recommendations

### Phase 1: Immediate Actions (Before Deployment)

#### 🔴 Critical: Cannot Use As-Is

This model **is not production-ready** for autonomous driving. Primary issues:

1. **Recall too low (24.7%)** - Misses 75% of objects
2. **Traffic lights/signs not detected** - Fatal for autonomous driving
3. **Small object detection catastrophic** - Pedestrians at distance missed
4. **Domain mismatch severe** - COCO distribution very different from BDD

#### Action Items:

```
✗ DO NOT deploy this model in production
✓ DO fine-tune on BDD100K dataset
✓ DO add traffic light/sign detection heads
✓ DO implement small object detection improvements
✓ DO evaluate on BDD test set, not COCO metrics
```

### Phase 2: Fine-Tuning Strategy

#### 2.1 Data Preparation

```markdown
**Training Data Split:**
- Training: 50,000 BDD images (70% of 70k)
- Validation: 10,000 BDD images
- Test: 10,000 BDD images (held out for final eval)

**Class Balancing:**
- Oversample: Traffic lights, signs, motorcycles, trains
- Undersample: Cars (most frequent)
- Target ratio: 1:1 for all classes

**Augmentation Strategy:**
- Random horizontal flip
- Random brightness/contrast (for weather variation)
- Random scaling (0.8x to 1.2x) for size variation
- Random crops (512x512 to 768x768)
- Mosaic/CutMix for crowded scene simulation
```

#### 2.2 Architecture Modifications

```markdown
**Required Changes:**
1. Add detection head for traffic light state (3-way classifier: red/yellow/green)
2. Add detection head for traffic sign type (multi-class classifier)
3. Increase FPN levels for small object detection
4. Add auxiliary small object detection branch
5. Consider adding attention module for spatial bias correction
```

#### 2.3 Training Configuration

```markdown
**Hyperparameters:**
- Backbone: Keep ResNet-50-vd (pretrained frozen initially)
- Learning rate: 1e-4 (low due to domain shift)
- Warmup: 500 iterations
- Total epochs: 50-100
- Batch size: 16-32 per GPU
- Optimizer: SGD with momentum 0.9
- Loss: Focal loss for class imbalance

**Progressive Training:**
- Phase 1 (Epochs 1-20): Freeze backbone, train heads only
- Phase 2 (Epochs 21-60): Unfreeze backbone, lower LR
- Phase 3 (Epochs 61-100): Fine-tune entire model, aggressive augmentation

**Validation Strategy:**
- Evaluate every 5 epochs on validation set
- Track: AP, AP@small, AP@medium, AP@large, per-class AP
- Early stopping if no improvement for 10 epochs
```

### Phase 3: Targeted Improvements

#### Small Object Detection Boost

```markdown
**Techniques:**
1. Feature Pyramid Network (FPN) - Improve small feature maps
2. Panoptic Quality Loss - Better handling of multiple scales
3. IoU-balanced sampling - Oversample small objects during training
4. Auxiliary super-resolution branch - Upscale small regions for better features
5. Multi-scale inference - Test with 0.8x, 1.0x, 1.2x input scales and merge

**Expected Improvement:**
- AP@small: 0.014 → 0.06-0.10 (realistic ceiling)
- Overall AP: 0.122 → 0.25-0.35 (with fine-tuning)
```

#### Class-Specific Improvements

```markdown
**Traffic Light Detection:**
- Implement state classification head (red/yellow/green/off)
- Use color-based augmentation
- Hard example mining on similar-colored objects
- Target: AP > 0.5, State accuracy > 95%

**Traffic Sign Detection:**
- Create sign-type classifier (STOP, YIELD, SPEED_LIMIT, etc.)
- Augment with perspective transforms (signs at angles)
- Focus on small sign detection
- Target: AP > 0.4

**Motorcycle/Bike Detection:**
- Increase negative sampling to distinguish from cars/pedestrians
- Hard example mining on mis-classifications
- Occlusion-aware training
- Target: AP > 0.25

**Train Detection:**
- Synthesize training data if needed (still very rare)
- Use few-shot learning techniques
- Focus on complete scenes with trains
- Target: AP > 0.15 (realistic for rare class)
```

### Phase 4: Validation & Benchmarking

```markdown
**Evaluation Metrics to Track:**
- mAP @[0.5:0.95]
- AP @0.5, AP @0.75 (different IoU thresholds)
- Per-class AP (all 10 classes)
- AP by size (small/medium/large)
- AP by scene type
- Per-attribute performance:
  - Clear vs. adverse weather
  - Daytime vs. nighttime
  - Visible vs. occluded
  - Full vs. truncated

**Comparison Baseline:**
- Compare against other real-time detectors (YOLOv8, EfficientDet)
- Benchmark on driving-domain models if available
- Speed/accuracy tradeoff analysis

**Safety Requirements:**
- Confidence calibration check
- Out-of-distribution detection
- Temporal consistency (frame-to-frame)
- Failure mode analysis
```

---

## Detailed Recommendations Summary

### Quick Reference: What to Do Next

| Issue | Priority | Solution | Timeline |
|-------|----------|----------|----------|
| Traffic light/sign not detected | 🔴 Critical | Add detection heads + fine-tune | 2-4 weeks |
| Recall too low (24.7%) | 🔴 Critical | Full model fine-tuning on BDD | 2-3 weeks |
| Small object AP = 0.014 | 🔴 Critical | FPN + hard negative mining | 1-2 weeks |
| Poor localization (low IoU) | 🟠 Important | Regression loss tuning + auxiliary branches | 1 week |
| Class imbalance (cars dominant) | 🟠 Important | Weighted sampling during training | 3-5 days |
| False positives in background | 🟡 Nice-to-have | Confidence calibration + NMS tuning | 3-5 days |

---

## Conclusion

### Summary of Findings

The RT-DETR model, while performing well on COCO, exhibits **critical failures on BDD100K**:

✅ **Strengths:**
- High precision (92.9%) means low false positives
- Good performance on large objects (AP 0.32)
- Real-time capable architecture

❌ **Critical Weaknesses:**
- **Recall catastrophically low (24.7%)** - fundamentally unsuitable
- **Cannot detect traffic lights/signs** - fatal for autonomous driving
- **Small object detection broken** (AP 0.014) - essential for distance perception
- **Severe domain shift** from COCO to BDD100K
- **Spatial and class biases** from COCO transfer negatively

### Production Readiness

**Current Status: ❌ NOT PRODUCTION READY**

Required actions before deployment:
1. ✓ Fine-tune on BDD100K (mandatory)
2. ✓ Add traffic light/sign detection (mandatory)
3. ✓ Improve small object detection (critical)
4. ✓ Achieve minimum recall ≥ 80% (critical)
5. ✓ Per-class benchmarking (important)
6. ✓ Safety validation (important)

### Path Forward

**Expected Timeline to Production:**
- **Week 1-2:** Add traffic light/sign heads, initial fine-tuning
- **Week 2-4:** Iterative improvement of AP@small, class balancing
- **Week 4-6:** Comprehensive validation, safety testing
- **Week 6-8:** Performance optimization, mobile deployment (if needed)
- **Total: ~8-10 weeks** to production-ready system

### Final Verdict

**This model serves as a good baseline but requires substantial effort to become production-ready for autonomous driving.** The high precision is valuable, but the critically low recall makes it unsuitable for safety-critical applications without significant improvements.

**Recommended approach:** 
Start with fine-tuning this model on BDD100K as the foundation. Its strong architecture and COCO pretraining provide a good starting point, but domain-specific training is non-negotiable.

---

## 📊 Appendix: Metrics Reference

### COCO Metric Definitions

- **AP (Average Precision):** Precision averaged across recall levels at various IoU thresholds
- **AP@0.5:** AP at loose IoU threshold (object considered "detected" if IoU > 0.5)
- **AP@0.75:** AP at strict IoU threshold (requires IoU > 0.75)
- **AP@0.5:0.95:** Standard COCO metric, averaging over IoU thresholds 0.5 to 0.95
- **AR (Average Recall):** Maximum recall achievable with given number of detections per image

### Size Categories (COCO Definition)

- **Small:** area < 32² = 1,024 pixels² (tiny objects)
- **Medium:** 32² ≤ area < 96² = 9,216 pixels² (typical objects)
- **Large:** area ≥ 96² = 9,216 pixels² (large vehicles, objects filling much of image)

### Interpretation Guide

| AP Value | Interpretation | Quality |
|----------|---|----------|
| 0.0-0.1 | Essentially non-functional | 🔴 Unacceptable |
| 0.1-0.2 | Very poor | 🔴 Poor |
| 0.2-0.3 | Poor | 🟠 Fair |
| 0.3-0.4 | Fair | 🟡 Moderate |
| 0.4-0.5 | Moderate | 🟡 Moderate-Good |
| 0.5-0.6 | Good | 🟢 Good |
| 0.6-0.7 | Very good | 🟢 Very Good |
| 0.7+ | Excellent | 🟢 Excellent |

---

**Report Generated:** Model Performance Analysis  
**Dataset:** BDD100K (70K training images)  
**Model:** RT-DETR (ResNet-50-vd) pretrained on COCO + O365  
**Evaluation Date:** [Current Analysis]  
**Status:** Requires fine-tuning before production use

