# 🏡 좌표 최적화 알고리즘

**by 한제규**

<br>

###  💡 개발한 알고리즘 움짤

-----------
![알고이미지](https://postfiles.pstatic.net/MjAyMjA4MTdfMTMy/MDAxNjYwNzIzNzEzNjc4.oJaJCRqCWK6DqsJZb5pOByi1ndyHK4aS5MsXIsl95Mog.5ua-DPVR5GJrp6UeE_IDswf08Br1Z1vTVgwylXZXigIg.GIF.anfidthtn/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EC%9B%80%EC%A7%A4.gif?type=w773)
<br>

###  💡 알고리즘 설명 (생각의 과정)

-----------

###  1. 좌표가 거의 일직선 상의 점이라면 중간의 좌표가 없더라도 거의 비슷한 경로를 그릴 수 있다.

###  2. 그렇다면 구면좌표계의 직선 두 개 사이의 각도를 구해야한다. (세 점 사이의 각도)

###  3. 구면좌표계의 두 직선 사이의 각도를 구하기 위해서는 해당 직선들의 평면좌표계 벡터의 각도를 구하면 된다.

###  4. 위도와 경도로 이루어진 세 점 사이의 평면벡터 두 개를 구하기 위해서는 수치화되어있는 위도와 경도값이 의미하는 실제 거리를 비율을 구해야한다.

###  5. 위도 1도는 지구 어디서나 거의 111km로 일정하다. 하지만 경도 1도는 적도에서 111km이지만 극에서는 0km이다. 
![그린이미지](https://postfiles.pstatic.net/MjAyMjA4MTdfMjg4/MDAxNjYwNzI0NDE3MDc4.-WVFMxreFL-RmiWfu6NFxZ8YQc8IDO6_8IGncb5-ncIg.bz5uJwOTrsA2Im-ZKmCQmmXmxYij0LO2hDp9jaEZ3Ogg.PNG.anfidthtn/image.png?type=w773)

###  6. 위의 그림을 바탕으로 적도에서는 2 * pi * R / 360이 1도당 거리이지만, 위도가 a도인 곳에서는 2 * pi * R * cos(a) / 360이 1도당 거리임을 알 수 있다. 즉 경도는 1도당 cos(위도) * 위도 1도당 거리 이다.

###  7. 식을 정리하면 1위도거리 * cos(위도) = 1경도거리이므로, 위도좌표 변화량에 cos(위도)를 곱해주면 경도좌표 변화량과 같은 거리가 나오게 된다.

###  8. 이를 통해 구면좌표계 상의 두 점 사이의 평면좌표계 상의 벡터값을 구할 수 있다.
