# Object-Detection

### YOLO "You Only Look Once: Unified, Real-Time Object Detection"

#### Abstract & Introduction
>기존의 Object Detection System(이미지로부터 특정 object를 찾아주는 기술)은 classifying을 재구성해서 구현함
>    > 1.물체의 존재 여부 2.그 물체가 무엇인지 판단 

> YOLO에서는 이러한 2단계를 거치는 것이 아닌 single regression probelm으로 해결할 수 없을까에서 출발

- DPM(Deformable Parts Model): sliding-window기법을 활용해서 이미지의 다양한 location과 scale속에서 classifying을 진행
- R-CNN: region proposal methods를 사용해 이미지 속에서 potential bounding box를 생성한 이후에 이 box에서 classifying을 진행

>3Step![The YOLO Detection System](https://user-images.githubusercontent.com/96530828/167073401-2f9aad79-0d80-44d8-947c-5924e99677d0.jpg)
 

#### Unified Detection
>  
1. input image를 SxS Grid 격자 형태로 동등하게 나눈다.
2. 하나의 Grid격자는 B개의 potential bounding box를 가진다.
3. 하나의 potential bounding box는 x,y,w,h,c의 5가지 요소로 정의할 수 있다.
      - x,y는 potential bounding box의 중앙을 나타내는 요소
      - w,h는 potential bounding box의 너비와 높이를 나타내는 요소
      - c(Confidence)는 potential bounding box가 object를 포함할 가능성 Pr(Class_i|Object), IOU(Intersection Over Union)의 곱 형태로 나타냄
      >    > What is IOU: 


#### reference
[1] Joseph Pedmon, You Only Look Once: Unified, Real-Time Object Detection, 2016
