# 🐱🐶 Cat vs Dog Classification using CNN (From Scratch)

## 📌 Project Overview
This project demonstrates my first end-to-end implementation of a Convolutional Neural Network (CNN) for binary image classification (Cat vs Dog).
- Total Images: 10,031
- Classes: 2 (Cat, Dog)
- Image Size: 64×64
- Framework: TensorFlow / Keras
- Training Epochs: 100
The objective was to understand the complete deep learning pipeline:
- Image preprocessing
- CNN architecture design
- Model training with callbacks
- Performance evaluation
- Diagnosing non-convergence
---
## 🗂 Dataset Handling
Images were loaded using ImageDataGenerator with:
- Rescaling (1./255)
- Shear augmentation
- Zoom augmentation
- Horizontal flipping
- Batch size: 30
- Target size: (64, 64)
- Class mode: Binary
  
Training and validation were handled using directory-based generators.

---
## 🧠 Model Architecture
Model Type: Sequential CNN
Structure:
- Conv2D (32 filters, ReLU)
- MaxPooling2D
- Conv2D (32 filters, ReLU)
- MaxPooling2D
- Flatten
- Dense (128)
- Dense (64)
- Dense (32)
- Dense (16)
- Dense (8)
- Dense (4)
- Dense (1, Sigmoid)
  
Total Parameters:
824,129 (All trainable)
> Majority of parameters (~800K) are in the first Dense layer after flattening.

---
## ⚙️ Training Configuration
- Loss Function: Binary Crossentropy
- Optimizer: Adam
- Initial Learning Rate: 2.7e-05
- Callbacks Used:
  - EarlyStopping (patience=8, restore_best_weights=True)
  - ReduceLROnPlateau (factor=0.3, patience=4)
  ---
  ## 📊 Training Results
| Metric              | Value  |
| ------------------- | ------ |
| Training Accuracy   | 49.30% |
| Validation Accuracy | 50.02% |
| Training Loss       | 0.6932 |
| Validation Loss     | 0.6931 |
---
## 🔎 Performance Analysis
The model achieved approximately 50% accuracy with loss ≈ 0.693.

For binary classification:

- 50% accuracy ≈ random guessing
- Log-loss ≈ 0.693 indicates no effective learning
This indicates the model failed to extract discriminative features between cats and dogs.

Rather than ignoring this result, this project focuses on diagnosing why learning did not occur.

---
## 🧠 Technical Diagnosis
Based on architecture and training configuration:
1. The learning rate (2.7e-05) is extremely low for training from scratch.
2. Convolution depth is shallow (only 2 Conv layers).
3. Most parameters are concentrated in dense layers instead of convolution layers.
4. Flattening early limits hierarchical feature extraction.
5. Validation data was directly passed from test generator.
6. Scheduler configuration may prevent proper learning rate adjustments.
This analysis demonstrates understanding of CNN training dynamics and failure modes.

---
## 📈 Suggested Improvements
| Area                    | Current Implementation                              | Improvement                                    | Why It Matters                                                                     |
| ----------------------- | --------------------------------------------------- | ---------------------------------------------- | ---------------------------------------------------------------------------------- |
| Learning Rate Scheduler | `ReduceLROnPlateau(monitor='val_loss', mode='max')` | Change `mode='min'`                            | `val_loss` should decrease. Using `mode='max'` prevents proper LR reduction logic. |
| Convolution Depth       | Only 2 Conv2D layers (32 filters each)              | Increase filters progressively (32 → 64 → 128) | Deeper feature extraction improves pattern learning.                               |
| Dense Layers            | 6 Dense layers after Flatten                        | Reduce dense layers (e.g., 128 → 64 → 1)       | Too many dense layers increase parameters without improving feature extraction.    |
| Feature Extraction      | Early flattening (14×14×32 → 6272)                  | Add more Conv + Pool blocks before Flatten     | CNNs should extract deeper spatial features before classification.                 |
| Batch Normalization     | Not used                                            | Add `BatchNormalization()` after Conv layers   | Stabilizes training and speeds convergence.                                        |
| Model Capacity          | ~824K parameters mostly in dense layer              | Shift parameters to convolution layers         | CNN strength is spatial learning, not dense memorization.                          |
| Validation Strategy     | Using test set as validation_data                   | Create separate validation split               | Prevents data leakage and gives reliable evaluation.                               |
| Image Size              | 64×64                                               | Try 128×128                                    | Higher resolution may capture more distinguishing features.                        |
| Epoch Strategy          | 100 epochs with EarlyStopping (patience=8)          | Keep EarlyStopping but monitor learning curves | Prevent under-training or premature stopping.                                      |
| Transfer Learning       | Not used                                            | Try pretrained model (e.g., MobileNetV2)       | Will likely push accuracy >85–90%.                                                 |
---
## 🚀 Future Roadmap
Next phase of this project:
- Redesign architecture with deeper convolution blocks
- Proper hyperparameter tuning
- Compare baseline CNN vs improved CNN
- Implement transfer learning
- Evaluate performance improvement quantitatively
---
## 🧠 What This Project Proves
This project demonstrates:
- Ability to build CNN from scratch
- Understanding of binary classification workflow
- Knowledge of training callbacks 
- Ability to interpret loss behavior
- Capability to diagnose non-learning models
- Awareness of architectural design flaws
This marks my transition from theoretical deep learning knowledge to practical implementation and model analysis.
