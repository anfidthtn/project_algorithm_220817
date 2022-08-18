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

###  2. 그렇다면 구면좌표계의 세 점을 잇는 두 선분 사이의 각도를 구해야한다.

###  3. 구면좌표계의 두 선분 사이의 각도를 구하기 위해서는 해당 선분들의 평면좌표계 벡터의 각도를 구하면 된다.

###  4. 위도와 경도로 이루어진 세 점 사이의 평면벡터 두 개를 구하기 위해서는 수치화되어있는 위도와 경도값이 의미하는 실제 거리를 비율을 구해야한다.

###  5. 위도 1도는 지구 어디서나 거의 111km로 일정하다. 하지만 경도 1도는 적도에서 111km이지만 극에서는 0km이다. 
![그린이미지](https://postfiles.pstatic.net/MjAyMjA4MTdfMjg4/MDAxNjYwNzI0NDE3MDc4.-WVFMxreFL-RmiWfu6NFxZ8YQc8IDO6_8IGncb5-ncIg.bz5uJwOTrsA2Im-ZKmCQmmXmxYij0LO2hDp9jaEZ3Ogg.PNG.anfidthtn/image.png?type=w773)

###  6. 위의 그림을 바탕으로 적도에서는 2 * pi * R / 360이 1도당 거리이지만, 위도가 a도인 곳에서는 2 * pi * R * cos(a) / 360이 1도당 거리임을 알 수 있다. 즉 경도는 1도당 cos(위도) * 위도 1도당 거리 이다.

###  7. 식을 정리하면 1위도거리 * cos(위도) = 1경도거리이므로, 위도좌표 변화량에 cos(위도)를 곱해주면 경도좌표 변화량과 같은 거리가 나오게 된다.

###  8. 이를 통해 구면좌표계 상의 두 점 사이의 평면좌표계 상의 벡터값을 구할 수 있다.

###  9. 구한 평면좌표계 벡터를 통해 두 벡터 사이의 각도 cosine값을 구할 수 있다. 공식은 다음과 같다.
<br>

![벡터코사인공식](https://postfiles.pstatic.net/MjAyMjA4MTdfNzIg/MDAxNjYwNzI1NjA4Nzk3.ygywfTd1Xjqd-LLiXSIeml3_RNMtuQmyVUlnPLnTMFMg.HtflVmyDfitbEVG80OfNsTDLOA7kAKR6mNcajXzT8yUg.PNG.anfidthtn/image.png?type=w773) 

### 10. 해당 공식으로 구해진 값과 원하는 제한각도의 cos값을 비교하여 제한각도 이하로 꺾이는 경로라면 거의 직선으로 판정하여 해당 점을 버린다.

### 11. 첫 점과 마지막 점은 무조건 저장한다.



###  💡 추가 최적화 작업

-----------

### 1. 기본적으로 이전 점과 거리가 매우 가깝다면 저장하지 않고 버린다.

### 2. 거리를 50미터 전후로 나워서 가깝다면 각도가 조금 더 많이 꺾여야 등록되게, 멀다면 조금 덜 꺾여도 등록되게 한다.

### 3. 아주 조금씩 꺾여서 직선으로 판정되는 것이 먼 거리에 누적되는 현상을 없애기 위해, 이전에 저장한 좌표와 일정 거리 이상 멀어진다면 각도가 직선에 가깝더라도 저장한다.


###  💡 알고리즘 코드 

-----------

```java
// public (리턴형식) saveCoordinates(기록번호, 입력좌표){
	// (List<CoordinateDto> coordinates : 입력좌표, CoordinateDto : 위도, 경도 Dto
	List<CoordinateDto> saveCoordinate = new ArrayList<>();
	try {
		if (coordinates.size() > 0) {
			// 시작 좌표는 무조건 저장
			saveCoordinate.add(coordinates.get(0));
			// 시작 좌표 다음 좌표부터 검사
			int checkIdx = 1;
			while (checkIdx < coordinates.size()) {
				if (checkIdx == coordinates.size() - 1) {
					// 마지막 좌표는 무조건 저장
					saveCoordinate.add(coordinates.get(checkIdx));
					break;
				}
				// 마지막으로 저장된 점을 불러온다.
				CoordinateDto before = saveCoordinate.get(saveCoordinate.size() - 1);
				// 마지막으로 저장된 지점과 지금 저장해보려는 지점 사이의 거리를 구한다.
				double lastDistance = distance(before, coordinates.get(checkIdx));
				if (lastDistance < 10) {
					// 만약 10미터 미만으로 너무 가까운 점이면 저장하지 않고 넘긴다.
					checkIdx++;
					continue;
				}
				if (lastDistance >= 100) {
					// 만약 100미터 이상으로 너무 먼 점이면 이후 각도 상관없이 무조건 저장한다.
					saveCoordinate.add(coordinates.get(checkIdx++));
					continue;
				}
				// 적당한 거리라면 다음 점과의 각도를 봐서 거의 평면이면 저장하지 않는다.
				// 남한 정도 범위인 위도 36~38도에서는 위도 1도당 111km,
				// 위도 n도에서의 경도 1도당 거리는 111km * cos(n)
				// 세 점은 가깝기 때문에 사실상 아무 한 점 기준 위도를 잡고 위도와 경도 좌표를 평면벡터화 해도 오차가 없다.
				Vector v1 = getVector(before, coordinates.get(checkIdx));
				Vector v2 = getVector(coordinates.get(checkIdx), coordinates.get(checkIdx + 1));

				double cosine = getVectorDotProduct(v1, v2) / (getVectorDistance(v1) * getVectorDistance(v2));
				if (lastDistance < 50) {
					// 거리가 가까울 땐 여유범위 +- 5도
					if (cosine > Math.cos(deg2rad(20))) {
						// 거의 직선일 경우 저장하지 않고 버림
						checkIdx++;
						continue;
					}
				}
				else {
					// 거리가 멀어지면 여유범위 줄이기
					if (cosine > Math.cos(deg2rad(10))) {
						// 거의 직선일 경우 저장하지 않고 버림
						checkIdx++;
						continue;
					}
				}
				// 위의 모든 경우를 뚫고(?) 온 경우 저장함.
				saveCoordinate.add(coordinates.get(checkIdx++));
			}
		}
		coordinates = saveCoordinate;
	}catch (Exception e) {
		// 최적화 로직에 에러가 생기면 최적화로직을 중단하고 최적화되지 않은 좌표로 저장
	}
	// 이 위치에서 좌표 table의 repo에 coordinates를 저장한다.
	(기타 필요로직들)
	// return 리턴값
//}


/**
 * 두 지점간의 거리 계산
 *
 * @param lat1 지점 1 위도
 * @param lon1 지점 1 경도
 * @param lat2 지점 2 위도
 * @param lon2 지점 2 경도
 * @return
 */
private double distance(double lat1, double lon1, double lat2, double lon2) {
	double theta = lon1 - lon2;
	double dist = Math.sin(deg2rad(lat1)) * Math.sin(deg2rad(lat2))
			+ Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) * Math.cos(deg2rad(theta));
	dist = Math.acos(dist);
	dist = rad2deg(dist);
	dist = dist * 60 * 1.1515;
	dist = dist * 1609.344;
	return (dist);
}

private double distance(CoordinateDto point1, CoordinateDto point2) {
	return distance(point1.getLatitude(), point1.getLongitude(), point2.getLatitude(), point2.getLongitude());
}

// This function converts decimal degrees to radians
private double deg2rad(double deg) {
	return (deg * Math.PI / 180.0);
}

// This function converts radians to decimal degrees
private double rad2deg(double rad) {
	return (rad * 180 / Math.PI);
}

private class Vector {
	double x;
	double y;
	public Vector(double x, double y) {
		this.x = x;
		this.y = y;
	}
}

// 두 좌표를 위도에 따른 경도의 거리를 적용해서 최대한 오차없는 평면벡터화 한 것
private Vector getVector(CoordinateDto point1, CoordinateDto point2) {
	return new Vector((point2.getLatitude() - point1.getLatitude()) / Math.cos(deg2rad(point2.getLatitude())),
		point2.getLongitude() - point1.getLongitude());
}

// 벡터의 크기
private double getVectorDistance(Vector v) {
	return Math.sqrt(v.x * v.x + v.y * v.y);
}

// 벡터의 내적
private double getVectorDotProduct(Vector v1, Vector v2) {
	return v1.x * v2.x + v1.y * v2.y;
}
```


###  💡 구현 결과

-----------

![결과](https://postfiles.pstatic.net/MjAyMjA4MTdfNjYg/MDAxNjYwNzM1MzUyNjA1.WcLc0VE32zQqj3GnXgXIbq3i4OgQWFB2jsEvo_vE5UMg.u0SIxY-CsuWFSVvh-QLupGe5qBKahoUvZCBKAwhwypsg.PNG.anfidthtn/image.png?type=w773)

###  💡 정리 PPT

-----------

![ppt1](https://postfiles.pstatic.net/MjAyMjA4MTlfMTI2/MDAxNjYwODQ3NzY4NDg0.2XtEcFgcNFeV3aLYMaXXxOO81mXln_hzLK52qcJxSocg.A7gIYX6kZnrMNVLbChNvV79SwXA8y5QHkTogbo6CYU0g.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-0.png?type=w773)
![ppt2](https://postfiles.pstatic.net/MjAyMjA4MTlfNTEg/MDAxNjYwODQ3NzY2ODA4.H3rSpyeYegoVGZaITpkMSIUCoJ2qDuPSdgLfYSEdkVMg.FW-FVTtpxDc_tCU4FQEXpJlTBtWJ4_103ndbmQXsWs0g.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-1.png?type=w773)
![ppt3](https://postfiles.pstatic.net/MjAyMjA4MTlfMjQ0/MDAxNjYwODQ3NzY1Mzg0.sgpEcd1ksrzM2ha8OlJpoBqcv6lor-0x1huZPovtSZQg.27u59anv3tS-5I0S8BM4tSEDluo0yXLJPbuPuNclkd8g.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-2.png?type=w773)
![ppt4](https://postfiles.pstatic.net/MjAyMjA4MTlfMjk4/MDAxNjYwODQ3NzY1ODM2.AA1ByLCUo1SKGpiCAnvfyYXsJC3ZjZBU17miFvgvN80g.6TfdfVV12u4hx1IrybVU-_pCG_boSVuv4NaRCY1mnNkg.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-3.png?type=w773)
![ppt5](https://postfiles.pstatic.net/MjAyMjA4MTlfNTYg/MDAxNjYwODQ3NzY3NzA0.vBuVVlq6OrbTIoNumaJQNVY014xehkGVhByPV7NshGQg.VXUuWzSHoCjEQOpK33inU2ZLK_CEyaJZe6TnYjWWuUIg.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-4.png?type=w773)
![ppt6](https://postfiles.pstatic.net/MjAyMjA4MTlfMTM4/MDAxNjYwODQ3NzY1OTY2.nbKxdyyFvjdMrW_sypCsuvmOEgjQN0zVgxXwNTNijv8g.ZciBfHQfcFzJPBMbAMutZxuQXvQqkeVbg73zdWYr5ekg.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-5.png?type=w773)
![ppt7](https://postfiles.pstatic.net/MjAyMjA4MTlfNTgg/MDAxNjYwODQ3NzY3MTYx.VexS1qgPZtk3cq4xS0Irb1oLFv9HyUM-0WLYIRfzCdIg.Ouf8UR_bI6pPHUTRd3m14eTLSvOf8OQw1lRmCFvN0uog.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-6.png?type=w773)
![ppt8](https://postfiles.pstatic.net/MjAyMjA4MTlfODkg/MDAxNjYwODQ3NzY3MzYz.rXVQ-zmlCtzanoOX-oAQbDMxJLFwQzGtwCCwZnN11Agg.tYoYqvQcjNgn9qDPcuFnHxb0IMwj2omUtpet1DxHXeIg.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-7.png?type=w773)
![ppt9](https://postfiles.pstatic.net/MjAyMjA4MTlfMTc5/MDAxNjYwODQ3NzY3Nzc4.3T8lN6VETYl5iliQiXAEOpFHnsHkD8Wlf98eUGJ-tbIg.ZRzF5JabOfdI9BeaeICJzflP1le55aEXfEIodT8bvJsg.PNG.anfidthtn/p1gap3mln71n4kd6fovk8n3ld34-8.png?type=w773)
