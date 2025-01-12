# Introduction
The rapid expansion of computer, communication and consumer sectors has propelled the production of electronic information devices into a new phase of development. Consequently, the market is seeing a rise in demand for PCBs. PCB defect detection demands high precision and efficiency to identify critical flaws such as spurious copper, mousebites, pinholes, spurs, open circuits and short circuits and play an important role in manufacturing. This project evaluates YOLOv11’s performance in detecting defects on Printed Circuit Boards (PCBs) and compares it with YOLOv8 and YOLOv10.

# Dataset
We utilised the publicly available DeepPCB dataset which comprises 1,500 image pairs of defect-free template images and corresponding defective tested images annotated for six PCB defect types: open, short, mousebite, spur, copper, and pinhole.
![image](https://github.com/user-attachments/assets/fea17193-16c2-4bc6-85ba-2f5ea6d697b9)

Fig 1: This figure shows the pair of (a) defect free template image and (b) a corresponding tested annotated image highlighting six defect classes: open, short, mousebite, spur, copper, and pinhole in the DeepPCB dataset
In this research, only the defective tested images were used for our experiments. High-resolution images (16,000 × 16,000 pixels) were divided into 640 × 640 sections, with annotations stored in text files (x1, y1, x2, y2, class_id). We used a Python script to convert these annotations into YOLO format with normalized bounding box coordinates (class_id, center_x, center_y, width, and height). The dataset was expanded to 2,557 images through augmentation techniques, including Gaussian Blur, Median Blur, Grayscale Conversion, CLAHE, Horizontal and Vertical Flip, Saturation Adjustment, Brightness Adjustment, and Mosaic Augmentation. This resulted in 2,106 images for training, 301 for validation, and 150 for testing. Each image contains 3–12 annotated defects, ensuring diverse defect patterns. These augmentations were designed to simulate real-world conditions and enhance the YOLOv11 model's generalization for PCB defect detection. 

# Methodology
![image](https://github.com/user-attachments/assets/6f225e97-5f05-4429-b278-f7c4e0ca8702)

Fig 2. YOLOv11 Architecture [26] showcasing the new C3k2 blocks and the C2PSA module

# Training and Validation Setup
The YOLOv11 model was trained for 150 epochs with 640×640 image size and a batch size of 32. The AdamW optimizer was used with an initial learning rate of 0.01 (cosine annealing), momentum of 0.9, and selective weight decay of 0.0005. A dynamic learning rate schedule ensured smooth convergence, while biases were excluded from regularization. The hardware setup included an NVIDIA Tesla T4 GPU in Google Colab, running CUDA 12.2 with 15 GB GPU memory.

# Performance Evaluation
The YOLOv11 model was assessed using metrics that evaluate accuracy, robustness, and efficiency. Precision measures correct detections among all outputs, while recall quantifies the detection of all relevant instances. Mean Average Precision (mAP) evaluates localization and classification accuracy, with mAP@0.5 and mAP@0.5:0.95 providing insights at varying IoU thresholds. Inference time, measured in FPS, assesses the model’s real-time suitability. These metrics ensure a comprehensive evaluation of YOLOv11 for PCB defect detection. 
    As summarized in Table 1, The YOLOv11 model achieved an impressive overall precision of 0.989, indicating that 98.9% of the detections were true positives. This high precision reflects the model’s reliability in correctly identifying defect instances while minimizing false positives, a critical requirement in automated defect detection tasks. Furthermore, the mean Average Precision at an IoU threshold of 0.5 (mAP50) reached 0.947, showcasing the model’s excellent localization and classification capabilities across all defect classes. The confusion matrix in Fig. 2. provides a visual representation of the class-wise results. Breaking down the results into individual defect classes highlighting the model's consistency across diverse categories.


Table 1: Evaluation results of mAP, precision and recall on DeepPCB dataset
![image](https://github.com/user-attachments/assets/e94bd50b-b671-45c1-8933-130aa31e6a6e)


![image](https://github.com/user-attachments/assets/491b8c4c-e928-4abb-8df6-cd03e1cd5059)

Fig. 3: Confusion matrix for YOLOv11 PCB defect detection.

The training-validation loss curves (Fig 3.) for YOLOv11 on the DeepPCB dataset show a clear downward trend, indicating effective optimization and convergence. The train/box_loss, train/cls_loss, and train/dfl_loss decrease steadily across 150 epochs, reflecting improvements in bounding box regression, classification, and distribution focus loss. Similarly, the validation losses (validation/box loss, validation/class loss, and validation/distribution focal loss) also decline, though with slight fluctuations, which are expected due to the validation set's variability. The metrics for precision, recall, mAP@0.5, and mAP@0.5:0.95 show consistent improvement during training, stabilizing at high values by the end of 150 epochs. This indicates the model’s strong ability to generalize to unseen data, with precision and localization accuracy reaching optimal levels. Overall, the curves highlight smooth convergence and robust model performance.

![image](https://github.com/user-attachments/assets/e83dbc18-b296-406c-9004-5500c7988ed0)

Fig 4: Training and validation loss curves of YOLOv11 on DeepPCB dataset.

Benchmarking YOLOv11 against previous iterations, as summarized in Table 2, YOLOv11 achieves the highest mAP@0.5:0.95 at 79.8%, outperforming YOLOv10 78.8% and YOLOv8 76.1%, showcasing improved robustness and accuracy in detecting small or occluded objects. Precision is tied between YOLOv11 and YOLOv8 at 98.9%, highlighting its reliability with minimal false positives. However, YOLOv11 has a slightly lower recall 92.5% compared to YOLOv8 (92.8%) and YOLOv10 (92.7%), indicating it failed to detect some true positives. All three models achieve equivalent mAP@0.5 at 94.7%, reflecting strong object detection accuracy at moderate IoU thresholds. YOLOv11 excels in precision and challenging localization tasks, making it well-suited for real-world defect detection.

Table 2: Comparison of mAP, precision, recall and Inference Speed for YOLOv8, YOLOv10 and YOLOv11

![image](https://github.com/user-attachments/assets/446ccb29-bfd2-4b8d-acfe-fc6584111a08)

The inference speed comparison highlights a trade-off between latency, complexity, and accuracy. YOLOv10 is the fastest at 270 FPS due to its Non-Maximum Suppression free design and lightweight architecture [27], ideal for real-time applications. YOLOv11 balances speed and accuracy, achieving 121 FPS, outperforming YOLOv8’s 78 FPS, while maintaining superior mAP@0.5:0.95. YOLOv11’s enhanced architecture and its use of Non-Maximum Suppression (NMS) [26] for refining predictions slightly increased post-processing latency compared to YOLOv10. Despite being the slowest, YOLOv8 offers competitive precision and recall, suitable for non-time-sensitive tasks. YOLOv11 is versatile, offering an optimal balance of speed and detection performance. 

![image](https://github.com/user-attachments/assets/24c990b3-1464-48f3-8356-afbd251361bd)

Fig 5: Images (a) and (b) are results of YOLOv11 predictions on defective pcb samples with class annotations and confidence scores.  

# Conclusion
This paper evaluated YOLOv11's advancements and compared its performance with YOLOv8 and YOLOv10 for PCB defect detection. YOLOv11 achieves a balance between accuracy, efficiency, and versatility, making it a robust choice for real-world manufacturing applications. While YOLOv10 excels in speed, YOLOv11's refined architecture and adaptability offer superior detection capabilities especially in detecting small or occluded objects and accurately localizing them, making it more versatile in challenging conditions. 










