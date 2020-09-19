---
layout: post
title: "Unity Camera 캘리브레이션"
date: 2020-09-20 15:52:23 +0900
category: Unity
use_math: true
---
# Contents

유니티에서 캘리브레이션을 수행하여 camera intrinsic parameter를 얻는 방법을 알아봅시다.

---

# Intrinsic Parameter

내부 파라미터의 경우 크게 변수는 3개를 갖는다고 볼 수 있다.


$f$(focal length), $c_x$, $c_y$


그런데 유니티에서 카메라의 스크린샷을 찍을 경우 해상도는 알 수 있고, 해상도의 절반값 정도는 $c_x$와 $c_y$로 보아도 무방하다.

이미지크기가 1920 x 1080 인 경우, $c_x$와 $c_y$는 다음과 같이 설정한다.


$c_x = (Image Width - 1) / 2 $

$c_y = (Image Height - 1) / 2 $


그럼 이제 남은 변수는 $f$ 하나이고, 이는 간단한 계산으로 얻을 수 있다.


$\begin{pmatrix}x'\\y'\\ z'\end{pmatrix}=\begin{pmatrix}f & 0 & c_x \\ 0 & f & c_y\\ 0 & 0 & 1\end{pmatrix}\begin{pmatrix}x\\ y\\ z\end{pmatrix}$


이므로 위 식에서 $z'$을 1로 바꾸기 위해 식을 살짝 수정해보자.


$ \begin{pmatrix}x'/z'\\y'/z'\\ 1\end{pmatrix}=\begin{pmatrix}f & 0 & c_x \\ 0 & f & c_y\\ 0 & 0 & 1\end{pmatrix}\begin{pmatrix}x/z\\ y/z\\ 1\end{pmatrix} $


그리고 여기서 $x'/z'$를 $\tilde{x}$로 치환하고 식을 정리하면,


$\tilde{x} = f (x/z) + c_x$

$\tilde{y} = f (y/z) + c_y$


미지수가 $f$이므로 $f$에 대해 정리하면,


$f =(\tilde{x} - c_x) (z/x)$

$f =(\tilde{y} - c_y) (z/y)$


이렇게 정리하고나니 미지수는 1개인데 식은 2개이다. 그러므로 식 1개만 써서 값을 구해도 된다.

# Unity

그럼 유니티에서 위 식을 구해서 어떻게 구할 수 있을까?

답은 이미 아는 3차원 지점과 2차원 지점을 이용하는 것이다.

![image1](/Assets/2020-09-20/1.png?raw=true)

이렇게 유니티에서 카메라와 오브젝트를 세팅해준다.

여기서 굳이 패턴을 사용할 필요는 없다.

그냥 개인적으로 패턴에서 얻어지는 코너점들이 실제로 계산하기 좀 편해서 이렇게 했을뿐, 3차원과 2차원에서의 연관성(correspondences)를 본인이 정확하게 만들수만 있으면 아무거나 이용해도 된다.

세팅이 끝나면, 카메라 기준의 오브젝트 위치를 선정한다($x, y, z$이 여기서 얻어진다.)

![image2](/Assets/2020-09-20/2020-09-18_14-30-12.png?raw=true)

그리고나서 카메라에서 스크린샷을 촬영하여, 위에서 찾은 3차원점에 대응하는 2차원 위치를 얻어낸다.($\tilde{x},\tilde{y}$가 여기서 얻어진다.)

그렇게되면 cx와 cy도 알고, f도 계산으로 얻어낼 수 있으니, 카메라의 내부 파라미터는 모두 알게 된 셈이 된다.

# Verification

내부 파라미터를 구하긴 했는데, 실제로 잘 구해진건지는 잘 모른다. 그러니 확인하는 과정이 필요하다.

![image1](/Assets/2020-09-20/2.png?raw=true)

확인을 위해 포인트 클라우드에서 패턴영역만 추출했다.

![image1](/Assets/2020-09-20/3.png?raw=true)

그러면 이런 포인트 클라우드가 나오고, 이 포인트 클라우드를 이미지로 투영해보자.

![image1](/Assets/2020-09-20/4.png?raw=true)

포인트 클라우드에서 패턴을 나타내는 점들이 이미지상의 패턴 위로 잘 투영되는 것을 확인할 수 있다.

참고로 여기서 카메라와 라이다의 중심은 같은 위치에 놓여져있다.
