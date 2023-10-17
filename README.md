# **UESTC-PS**： The First and Only Person Search Benchmark Dataset



## 1. Introduction

​		**UESTC-PS** is collected from the Shenzhen Institute for Advanced Study of the University of Electronic Science and Technology of China. The original data is collected from seven cameras, which are successively arranged in the student's dormitory on the way to the classroom. The seven videos are a whole, which forms a complete trajectory. **Figure 1** shows the data set collection scenario.

![avatar](imgs/Collection%20scenario%20of%20UESTC-PS.png)

<p align='center'>Figure 1: Collection scenario of UESTC-PS.</p>

​		Due to the different location of the camera, it also contains many difficulties in PS, such as occlusion, small target samples, and feature differences caused by changes in perspective and attitude. Therefore, UESTC-PS is very challenging to the performance of existing models. In addition, in the collection process of most data sets, multiple cameras are used to shoot at the same scene, and then pedestrian images under different gestures are extracted. The background of the environment where the pedestrian lives changes a little, which is not in line with the changeable camera angles in the actual scene. Therefore, we adopt multiple pedestrian gestures under one track. The collection process of UESTC-PS meet the actual application scenarios perfectly, which is not available in other public data sets at present. Therefore, UESTC-PS can be used for video person search, image person search, single camera tracking, cross-camera tracking and other tasks. As shown in **Figure 2**, an example of the images collected by 7 cameras is given. 

![avatar](imgs/Example%20of%20UESTC-PS.png)

<p align='center'>Figure 2: Example of UESTC-PS.</p>

## 2. Labeling process

​		In the process of labeling, we select parts from 164074 video frames to divide into video segments in order to obtain higher quality video segments. They are divided into 588 segments, which including 24203 images and 533 pedestrian IDs. And then we divide these segments for training and test. After statistics, the number of duplicate IDs is 127. It's worth noting that the duplicate ID refers to that it appears in two different cameras at least. The division of training set and test set should not only consider the division of training segments and test segments, but also consider the division of training IDs and test IDs. Considering that the training IDs can only appear in the training set and the test ID can only appear in the test set, the result is that the training IDs has 74 duplicate IDs and test ID has 53. The number of training segments is 295 and 293 for test, the number of training IDs is 277 and the test is 256.

​	In the process of high-quality segment selection and pedestrian bounding box annotation processing, we discard the video segments with fuzzy and stuttering and clip some long segments into multiple short ones. And we discard the pedestrian bounding boxes with more than 50% occlusion area.

​	The interference of occlusion is introduced in the division of query and gallery. This method is that we select the frame in which the ID appears for the first time, and the pedestrian ID in this frame is basically under the interference of occlusion. The specific division strategy is as follows：

1. Find out the segments with duplicate IDs in test sets.

2. For each duplicate ID, classify segments of test set in which they appear by different cameras, and find out the first frame of each segment that contains duplicate ID.

3. The segment under one camera is regarded as the query segment, and the segment under the other camera is regarded as the gallery segment. If there is more than one query segment, one is taken as query segment respectively, and the gallery segments remain unchanged. The first frame of the query segment that contains duplicate ID is treated as query, and the first frame of gallery segment that contains duplicate ID is treated as gallery.

4. Replace the next duplicate ID in the test set, and repeat the process of 2-4 until the duplicate IDs in test set are traversed, then done.

​	For example, there is an instance in **Figure 3**. In step one, we get that there are three segments in test sets which ID field contains id1, and c1tXXsXX is taken by camera1, c2tXXsXX and c2tYYsYY are taken by camera2. In step two, the frames in which id1 appears for the first time in these segments are frame1xx_i, frame2xx_i, frame2yy_i, respectively. In step three, these segments are grouped according to camera and the different groups are traversed in turn. In each group, segments captured by the camera of this group are treated as queries, and segments not captured by the camera of this group are treated as galleries for these queries. In step four, if duplicate IDs are not traversed, continue to traverse the next ID, otherwise done.

![avatar](imgs/Partitioning%20strategies%20for%20query%20and%20gallery.png)

<p align='center'>Figure 3: Partitioning strategies for query and gallery.</p>

## 3. Data Organization

  The dataset package is provided on Baidu Cloud below.
  
  [UESTC-PS on Baidu](https://pan.baidu.com/s/1hxP76j3vFbLNLW_6OZph6g?pwd=2n6k).

​		For UESTC-PS, its organizational structure is as follows:

- annotations: Notes on training and test sets.

  - test: A JSON file, which contains the file path for each test image (relative to the test folder), the test pedestrian ID, the serial number frame_id in the current segment, adjacent image id and image length and width.
  - train: A JSON file, which contains the file path for each test image (relative to the train folder), the train pedestrian ID, the serial number frame_id in the current segment, adjacent image id and image length and width.
  
- test: A total of 293 video segments are used as test set.
  
  - video segment 1: A video segment of the test set.  Each video segment is named by cntm[sp], where n∈[1,7] and means the camera number. m represents the sequence number of videos and consists of two digits. Considering the length of them, for some sequences, we divide them into several segments and delete some of the frames between them, p represents the number of segments, which is also composed of two digits. For example, the sixth sequence taken by camera 1 is c1t06, the 12th sequence from camera 5 is divided into three segments, c5t12s01, c5t12s02 and c5t12s03.
  
    - gt: Each line represents the bounding box coordinate of the corresponding image.
    - imgs: A image sequence, which is a continuous scene captured by a camera. Each image is named by a six-digit sequence, such as 000001.jpg
  
  - video segment 2
  
    ...
  
  - video segment 293
  
- train: A total of 295 video segments were used as training set.
  
  - video segment 1: A video segment of the test set. Each video segment is named by cntm[sp], where n∈[1,7] and means the camera number. m represents the sequence number of videos and consists of two digits. Considering the length of them, for some segments, we divide them into several segments and delete some of the frames between them, p represents the number of segments, which is also composed of two digits.
  
    - gt: Each line represents the bounding box coordinate of the corresponding image.
    - imgs: A image sequence, which is a continuous scene captured by a camera. Each image is named by a six-digit sequence, such as 000001.jpg
  
  - video segment 2
  
    ...
  
  - video segment 295
  
- query_gallery.mat
  

UESTC-PS annotations are saved in JSON format. We used the same import method as the coco. Import the corresponding annotation file and read the image and labels.

## 4. Compare with Others [PRW/SYSU]

​		According to the data statistics in **Table 1**, it can be seen that the PRW provide 6 videos, each of which is shot in the same scene. The only difference is that the shooting position and angle, which basically conforms to daily surveillance scenes. The CUHK-SYSU provide numerous follow-up videos, each of which has different shooting scenes and is supplemented by film clips, providing numerous rich scenes, which is not consistent with daily monitoring scenes. UESTC-PS provide by 7 videos. Each video has different shooting scenes, location, and viewing angle, which fully conforms to daily surveillance scenarios. Therefore, UESTC-PS is better than PRW in terms of cross-domain capability, but not better than CUHK-SYSU with a large number of different scenarios.

<p align='center'>Table 1: Comparison of data sets.</p>

|                      | PRW            | SYSU           | UESTC-PS       |
| -------------------- | -------------- | -------------- | -------------- |
| Frame                | 11816          | 18184          | 24203          |
| ID                   | 932            | 8432           | 533            |
| Annotated box        | 34304          | 99809          | 59928          |
| Box/ID               | 36.8           | 11.8           | 112.4          |
| Train tracklet       | 0              | 0              | 295            |
| Test tracklet        | 0              | 0              | 293            |
| Train ID             | 482            | 5532           | 277            |
| Test ID              | 450            | 2900           | 256            |
| Camera               | 6              | -              | 7              |
| Type                 | Image          | Image          | Image + Video  |
| Monitoring scene     | Single         | Multi          | Multi          |
| Background variation | Small          | Big            | Big            |
| Occlusion condition  | Normal         | Normal         | Seriousness    |
| Shooting angle       | Smooth inspect | Smooth inspect | Overlook       |
| Trajectory           | No             | No             | Yes            |
| Size                 | Big            | Big            | Big + Small    |
| Scene                | Outdoor        | Outdoor        | Indoor+Outdoor |



## 5. Citation
If you use the UESTC-PS in your work, please cite it as:
```

```



## 6. Important Notice

- UESTC-PS is collected in a public area which is clearly marked with a video capture area. UESTC-PS respects and protects the privacy and personal interests of the people involved in it.


- UESTC-PS is for testing and related use only for academic purposes, please do not use it for any commercial or other purposes without permission, and please do not use the text or images provided by the dataset to infringe the interests of others.

- UESTC-PS is in the trial stage, there are incompleteness, and we are not responsible for the results produced by this dataset.

If you have any suggestions or comments, please email me at [UESTCsecurity@163.com](UESTCsecurity@163.com).