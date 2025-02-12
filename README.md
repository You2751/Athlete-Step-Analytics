# **Depth-Aware Foot Distance Measurement for Sports Analytics**

---

## **Project Overview**

This project aims to measure the **distance between athletes' feet** in sports videos where drills are performed at varying distances from the camera. The challenge lies in ensuring these measurements remain **consistent** despite changes in depth and perspective.

The goal is to develop a **depth-invariant measurement system** using various pose estimation and depth estimation techniques. This README outlines the journey, methods, successes, failures, and key lessons learned.

---

## 💡 **Approach & Pipeline**

### 🎮 **Initial Idea: VideoPose3D for 3D Pose Estimation**

I initially considered using **[VideoPose3D](https://github.com/facebookresearch/VideoPose3D)** to predict **3D poses from 2D keypoints**. This approach eliminates the need for explicit depth models by directly learning the relationship between 2D and 3D poses.

- **Dataset Requirement:** Planned to use the **SportsPose** dataset, which aligns perfectly with the sports analytics use case. However, accessing this dataset required a special request, and **I didn't receive permission** in time.

- **Why This Approach:**
  - 💲 **Cost-Effective:** No need for complex depth models.
  - 🏋️‍♂️ **Sports-Specific:** The SportsPose dataset is tailored for sports scenarios.

- **Outcome:** Unable to obtain the dataset, so I pivoted to other methods.

---

### 🔢 **Method 1: BlazePose with MiDaS**

**[BlazePose](https://github.com/geaxgx/depthai_blazepose)** is a lightweight pose estimation model, and **[MiDaS](https://github.com/isl-org/MiDaS)** is a monocular depth estimation model. Integrating both allowed for accurate foot distance calculations across varying depths.

- **Workflow:**
  1. **BlazePose** detects 33 keypoints, providing 2D coordinates.
  2. **MiDaS** generates a depth map for the frame.
  3. 2D key points are mapped to the depth map to extract **3D coordinates**.
  4. The **Euclidean distance** between the feet is calculated using these 3D coordinates.

- **Pros:**
  - 🔄 **Accurate for Forward/Backward Movements**: Handles movements towards/away from the camera effectively.
  - 🚀 **Lightweight:** Runs efficiently on most hardware.

- **Cons:**
  - 🚨 **Struggles with Side Movements:** Less accurate when athletes move laterally.
  - 🌀 **Edge Depth Inconsistencies:** MiDaS can misestimate depth at image boundaries.

---

### 💥 **Method 2: MediaPipe with Deep-Anything-V2.ipynb**

This approach combined **MediaPipe's pose estimation** with a generalized deep learning model named **Deep-Anything-V2**.

- **Workflow:**
  1. MediaPipe was used for detecting body key points.
  2. Deep-Anything-V2 was applied to enhance pose detection.

- **Challenges & Failures:**
  - ❌ **Model Incompatibility:** Deep-Anything-V2 isn’t optimized for depth estimation or sports-specific analysis.
  - ❌ **Pose Inaccuracy:** Struggled with maintaining consistent key points during backward/forward movements.
  - ❌ **Overfitting Issues:** Despite parameter tuning, overfitting occurred on specific frames.

- **Outcome:** This approach didn’t yield reliable results.

---

### 🤖 **Method 3: Meta's Sapiens Pose and Depth Estimation**

**Meta's [Sapiens models](https://huggingface.co/facebook/sapiens-depth-1b-torchscript)** offer state-of-the-art pose and depth estimation capabilities.

- **Workflow:**
  1. Loaded Sapiens Pose and Depth models.
  2. Processed frames to extract 3D coordinates.

- **Challenges:**
  - 🚧 **Computational Constraints:** The models are resource-intensive and **failed to run on available hardware**.
  - ⚡ **Optimization Attempts:**
    - Processed only **one frame at the beginning**, **two in the middle**, and **one at the end** to reduce computational load.
    - Despite optimizations, the models didn’t run due to hardware limitations.

- **Outcome:** Strong in theory, infeasible in practice without better resources.

---

## 💡 **Key Takeaways & Learnings**

- **🌟 Success:** The integration of **BlazePose with MiDaS** provided a reliable, depth-aware foot distance measurement system for forward and backward movements.

- **🚫 Failures:**
  - **Deep-Anything-V2** lacked the capability for depth-invariant analysis.
  - **Meta's Sapiens models** were computationally too demanding.

- **📆 Future Improvements:**
  - **Access Specialized Datasets:** Datasets like **SportsPose** could significantly improve performance.
  - **Hardware Upgrades:** Deploying resource-heavy models like Sapiens requires more powerful computational resources.
  - **Hybrid Approaches:** Combining lightweight pose estimation with approximate depth models for balance.

---
