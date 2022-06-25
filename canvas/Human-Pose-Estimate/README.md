### Bottom Up & Top Down

- **Bottom-up methods** estimate each body joint first and then group them to form a unique pose. 
Bottom-up methods were pioneered with DeepCut (a method we will cover later in more detail).
- **Top-down methods** run a person detector first and estimate body joints within the detected bounding boxes.

### [The Most popular Pose Estimation methods](https://viso.ai/deep-learning/pose-estimation-ultimate-overview/)

- Method #1 : OpenPose
- Method #2 : High-Resolutiono Net (HRNet)
- Method #3 : DeepCut
- Method #4 : Regional Multi-Person Pose Estimation (AlphaPose)
- Method #5 : DeepPose
- Method #6 : PoseNet
- Method #7 : DensePose

### [Deep Learning based Pose Detection methods](https://viso.ai/deep-learning/pose-estimation-ultimate-overview/)
- **OpenPose**
    - Bottom-up approach for multi-person human pose estimation
    - Open-sourced Real-time with high accuracy
    - API --- Flexibility oof selecting source images from camera fields, webcams, and others, more importantly for embedded system applications (ex, CCTV)
    - Lightweight version is enough for Edge inference applications with on-device processing

- **Regional Multi-Person Pose Estimation (AlphaPose)**
    - Top-down method.
    - It is useful for detecting in the presence of inaccurate human bounding boxes
    - Optimal architecture for estimating human poses via optimally detected bounding boxes
    - AlphaPose architecture is applicable for detecting both single and multi-person poses in images or video fields

- **DensePose → Detectron**
    - Mapping all human pixels of **an RGB image to the 3D surface** of the human body
    - Single pose, Multi pose


