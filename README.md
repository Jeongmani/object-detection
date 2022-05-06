# Object-Detection

### YOLO "You Only Look Once: Unified, Real-Time Object Detection"

#### Abstract & Introduction
>기존의 Object Detection System(이미지로부터 특정 object를 찾아주는 기술)은 classifying을 재구성해서 구현함
>    > 1.물체의 존재 여부 2.그 물체가 무엇인지 판단 

> YOLO에서는 이러한 2단계를 거치는 것이 아닌 single regression probelm으로 해결할 수 없을까에서 출발

- DPM(Deformable Parts Model): sliding-window기법을 활용해서 이미지의 다양한 location과 scale속에서 classifying을 진행
- R-CNN: region proposal methods를 사용해 이미지 속에서 potential bounding box를 생성한 이후에 이 box에서 classifying을 진행

>
####
