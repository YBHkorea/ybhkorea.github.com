---
layout: post
title: "Unity Camera 캘리브레이션"
date: 2020-09-19 15:52:23 +0900
category: Unity
use_math: true
---
# Contents

유니티에서 캘리브레이션을 수행하여 camera intrinsic parameter를 얻는 방법을 알아봅시다.

---

# Intrinsic Parameter

내부 파라미터의 경우 크게 변수는 3개를 갖는다고 볼 수 있다.

f(focal length), cx, cy

그런데 유니티에서 카메라의 스크린샷을 찍을 경우 해상도는 알 수 있고, 해상도의 절반값 정도는 cx와 cy로 보아도 무방하다.

이미지크기가 1920 x 1080 인 경우,




This formula $f(x) = x^2$ is an example.

$$
\lim_{x\to 0}{\frac{e^x-1}{2x}}
\overset{\left[\frac{0}{0}\right]}{\underset{\mathrm{H}}{=}}
\lim_{x\to 0}{\frac{e^x}{2}}={\frac{1}{2}}
$$
