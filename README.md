# Object-Detection

### YOLO "You Only Look Once: Unified, Real-Time Object Detection"

#### Abstract & Introduction
>기존의 Object Detection System(이미지로부터 특정 object를 찾아주는 기술)은 classifying을 재구성해서 구현함
>    > 1.물체의 존재 여부 2.그 물체가 무엇인지 판단 

> YOLO에서는 이러한 2단계를 거치는 것이 아닌 single regression probelm으로 해결할 수 없을까에서 출발

- DPM(Deformable Parts Model): sliding-window기법을 활용해서 이미지의 다양한 location과 scale속에서 classifying을 진행
- R-CNN: region proposal methods를 사용해 이미지 속에서 potential bounding box를 생성한 이후에 이 box에서 classifying을 진행

3Step<img src = "https://user-images.githubusercontent.com/96530828/167073401-2f9aad79-0d80-44d8-947c-5924e99677d0.jpg">
 

#### Unified Detection
> <img src = "https://user-images.githubusercontent.com/96530828/167075599-e1e29044-b50b-496f-b7b3-92bb60112f35.jpg" width="70%" height="70%">

1. input image를 SxS Grid 격자 형태로 동등하게 나눈다.
2. 하나의 Grid격자는 B개의 potential bounding box를 가진다.
3. 하나의 potential bounding box는 x,y,w,h,c의 5가지 요소로 정의할 수 있다.
      - x,y는 potential bounding box의 중앙을 나타내는 요소
      - w,h는 potential bounding box의 너비와 높이를 나타내는 요소
      - c(Confidence)는 potential bounding box가 object를 포함할 가능성 Pr(Class_i|Object), IoU(Intersection over Union)의 곱 형태로 나타냄
      > <img src= "https://user-images.githubusercontent.com/96530828/167075593-fcf49eac-9806-4a9f-8fd8-77d36873d99c.png" width="50%" height="10%">
4. 각각의 Grid는 C개의 potential conditional probabilities Pr(Class_i|Object)를 가진다. 

#### Network Design
<img src= "https://user-images.githubusercontent.com/96530828/167241672-0e9df248-4346-4e18-b98e-76ddb27f6163.jpg">
GoogLeNet을 기반으로 네트워크를 정의했고, 24개의 convolution layers와 2개의 fully connected layers를 사용(GoogLeNet에서 사용한 inception module 대신 1x1 reduction layers와 3x3 convolution layers를 사용함)

#### Training
> imformation
1. mageNet 1000-class competition dataset으로 pretrain을 진행함
2. Ren et al에서 pretrain model에 convolutional layer와 fully connected layer를 추가하면 성능이 좋아진다는 부분에서 착안하여 진행
3. Bunding box의 width와 height를 normalize함
4. 마지막 layer에 leaky ReLU를 설치함

##### mAP
> object detection에서 평가 요소로 많이 사용됨
>    - mAP를 설명하기 위해서는 AP를 이해해야함
>    - precesion(정확도) , Recall(재현율)
>    - 옳고 그름을 판단하는 작업 A에 대한 결과는 4가지 
>    > 1. 옳다고 판단했는데 옳은 경우
>    > 2. 옳다고 판단했는데 틀린 경우
>    > 3. 틀리다고 판단했는데 옳은 경우
>    > 4. 틀리다고 판단했는데 틀린 경우

> 각 확률을 P_i(A)라고 할때
> - Precesion은 P_1(A)/(P_1(A)+P_2(A))로 계산됨 : 옳다고 판단한것 중 실제로 옳은 비율
> - Recall은 P_1(A)/(P_1(A)+P_3(A))로 계산됨 : 옳은 것 중 옳다고 판단한 비율

<img src = "https://user-images.githubusercontent.com/96530828/167242556-f1af370f-31e3-4aa7-b041-4f2abc912262.jpg" width="50%" height="50%">
 
> Precesion, Recall 모두 크면 더 완벽한 판단을 했다는 의미, 하지만 실제로는 하나가 커지면 하나가 작아지는 경우가 많음
> * AP에 대한 개념: Precesion, Recall 모두 커서 완벽한 판단을 하는 것이 좋게 평가하자는 의미에서 정의된 evaluation metric으로 Precision - Recall Curve로 감싸져 있는 영역의 너비 의미
> * mAP는 여기에 mean을 추가해 판단이 한 번이 아니라 여러 번 나타날 때 mean을 계산해 산출한 지표

#### Loss Function
> 1. Sum-Squared Error를 사용하여 loss를 계산한다.
>    > 최적화가 용이하지만 loss의 최적화와 mAP의 증가가 완벽하게 맞지 않았다. 특히 localization error와 classification error를 동일한 가중치로 계산하다보니 많은 이미지 Grid가 object를 포함하지 않는 결과를 산출했다.
>    > * 이러한 문제를 해결하기 위해서 bounding box coordinate의 loss를 늘리고 object가 존재하지 않는 경우에 confidence prediction의 loss를 줄이는 factor를 도입했다.

> 2. Sum-Squared Error는 large bounding box와 small bounding box가 loss에 미치는 영향을 동일하게 가중
> * 하지만 이는 IoU를 계산함에 있어서 large bounding box의 민감도, 즉 deviation이 작기 때문
>    > 이를 해결하기 위해서 w,h의 scale을 square root를 사용해 전반적으로 줄여서 large bounding box와 small bounding box에서 deviation 차이를 줄이는 방식을 선택

> 3. Object당 하나의 bounding box를 가지게 하기 위해서 NMS(Non-Maximum Suppresion)을 사용
> * maximum이 아닌 confidence를 가진 bounding box를 억제하는 방법

<img src = "https://user-images.githubusercontent.com/96530828/167285514-d68e18ab-de7d-4cbe-9709-d6fe0e33bc6d.jpg" width="50%" height="50%">


#### reference
[1] Joseph Pedmon, You Only Look Once: Unified, Real-Time Object Detection, 2016
