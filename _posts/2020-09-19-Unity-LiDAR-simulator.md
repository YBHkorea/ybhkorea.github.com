---
layout: post
title: "Unity LiDAR 시뮬레이션"
date: 2020-09-19 15:52:23 +0900
category: Unity
---
# Contents

유니티에서 LiDAR(VLP-64) 시뮬레이션을 돌리는 방법을 알아봅시다.

시뮬레이터와 관련된 논문의 제목은 "Virtual Generation of Lidar Data for
Autonomous Vehicles"이다.
관심있는 사람들은 찾아보도록 하자.

---

# Link

[https://github.com/ptibom/Lidar-Simulator](https://github.com/ptibom/Lidar-Simulator)


위 깃헙 페이지에서 Unity project를 다운로드 받을 수 있다.

build 폴더는 Unity의 빌드 결과물이므로, 볼 필요는 딱히 없고, Unity에서 unityproject 폴더를 열어서 사용하면 된다.

![image1](/Assets/images/2020-09-19/i1.png?raw=true)

# Project Open

![image2](/Assets/images/2020-09-19/i2.png?raw=true)

유니티 Hub에서 추가를 눌러준다. 이번 포스트에서는 Unity 2019.4.9f1 버전을 사용하였다.

![image3](/Assets/images/2020-09-19/3.png?raw=true)

열어주는 폴더는 unityproject/LidarSimulator 폴더이다.

![image4](/Assets/images/2020-09-19/4.png?raw=true)

추가하고나서 버전을 선택하고 실행한다.

![image5](/Assets/images/2020-09-19/5.png?raw=true)

추가하고나면 업그레이드를 실행할 것인지 묻는데, 실행해준다.

아무래도 2017년도에 만들어진것이다 보니 현재 유니티버전에 맞게 업그레이드 할 필요가 있다.

# How to Use

![image6](/Assets/images/2020-09-19/6.png?raw=true)

프로젝트를 실행하고나서, Scene 폴더로 들어가면 FinalScene이 있다.

에디터에서 실험을 하면서 사용할때에는 FinalScene에서만 작업하면 된다.

build를 하고나서 실행하면 타이틀화면도 있고, 여러 화면들이 함께 빌드가 되는데, 그렇게 사용해도 된다.

자기가 편한대로 사용하자.

![image7](/Assets/images/2020-09-19/7.png?raw=true)

그렇게 씬에 들어가면 이런 화면이 나온다.

에디터에서 play를 시작하면 적절한 LiDAR 파라미터들이 세팅되며, 해당 파라미터들은 VLP-64 모델을 모방하여 만들어 진듯 하다.

wasd 키를 눌러서 파란색 자동차를 움직일 수 있다.

![image8](/Assets/images/2020-09-19/8.png?raw=true)

위 버튼중 플레이 버튼을 눌러서 가상의 라이다를 동작시키고, 폴더모양의 버튼을 눌러서 획득된 포인트 클라우드를 저장한다.

![image9](/Assets/images/2020-09-19/9.png?raw=true)

폴더버튼을 누르면 위와같은 화면이 나오고, 기본 경로는 바탕화면으로 되어있다.

적절한 이름으로 저장한다.

# Lighting

필수적인 단게는 아니나, 해당 유니티 프로젝트에는 Lighting이 미적용되어있는듯 하다.

유니티 프로젝트의 window -> renderring -> light setting 을 들어가서 자동 생성을 체크해주면 색감이 좀더 좋아지는 것을 볼 수 있다.

# Data Structure

논문을 발견하기는 했지만, 다 읽어보지는 않았기 때문에, 출력된 데이터가 어떻게 되었는지는 정확히는 모르나, 여러 시도를 해봐서 x y z 좌표는 읽는데 성공했다.

출력으로 얻어진 txt파일을 열어보면 다음과 같은 구조로 되어있다.

```
sep=
0 -29.86643 10.69307 0.890214 2.354466 2.354466 0.01570796 0
0 -29.86564 10.74299 0.890214 2.399882 2.399882 0.01570796 1
0 -29.86483 10.79488 0.890214 2.447274 2.447274 0.01570796 2
0 -29.86398 10.84887 0.890214 2.496769 2.496769 0.01570796 3
...
```

맨 앞 0 은 무엇을 위해 만든건지 잘 모르겠다. 유추하기로는 시간을 나타내는 것 아닌가 싶다.

시간이라고 가정하면 데이터구조는 [ 시간 / x / y / z / 각도 / 각도 / 각도 / 채널번호 ]로 되어있는듯 하다.

뭐가됬든, 내가 필요한 것은 x y z 좌표들이므로, 2:4번째 열 데이터를 쭉 읽어들이면 된다.

따라서, 실제 MATLAB에서 읽어들이기 위에서 sep= 줄을 지워주었다.

그리고나서 아래와 같은 코드로 데이터를 읽어들였다.

```python
fileID = fopen('test.txt', 'r');
formatSpec = '%f %f %f %f %f %f %f %f';
sizeA = [8 Inf];

A = fscanf(fileID, formatSpec, sizeA);
A = A';

pc = A(:, 2:4);

figure, pcshow(pc');
xlabel('x'), ylabel('y'), zlabel('z');
```

![image10](/Assets/images/2020-09-19/10.png?raw=true)

실제 읽어들이면 이렇게 읽힌다.

위 환경은 내가 임의로 구성한 환경이라 시뮬레이터에서 구성된 환경에서 얻은 데이터는 아니다.

그리고 위 포인트 클라우드는 라이다 중심쪽으로 맞게 회전과 이동을 넣어준것으로, 실제 사용하려면 이부분을 고려해야한다.(라이다가 포인트 클라우드의 중심이 되려면..)
