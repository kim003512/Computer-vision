#### 1. 기본 자료형 클래스

①Point_ 클래스

- 2차원 평면 위에 있는 점의 좌표를 표현하는 템플릿 클래스
- x, y의 두 멤버 변수
- 2차원 정수 좌표계에서 좌표를 표현하는 자료형

```
Point pt1; //pt1=(0,0)
pt1.x = 5; pt1.y = 10; //pt1=(5,10)
Point pt2(10,30); //pt2 = (10,30)
Point pt3 = pt1 + pt2; //pt3=(15,40)
Point pt4 = pt1 + 2; //pt4=(10,20)
int d1 = pt1.dot(pt2); //d1=350
bool b1 = (pt1 == pt2) //b1=false
```



②Size_ 클래스

- 영상 또는 사각형 영역의 크기 표현
- width, height의 두 정수형 멤버 변수

```
Size sz1, sz2(10,20); //sz1 = 0*0, sz2 = 10*20
sz1.width = 5; sz1.height = 10; //sz1 = 5*10
Size sz3 = sz1 + sz2; //sz3 = 15*30
```



③Rect_ 클래스

- 사각형의 위치와 크기 정보를 표현
- 사각형의 좌측 상단 점의 좌표(x, y), 사각형의 가로 및 세로 크기(width, height) 정수형 멤버 변수

```
Rect rc1; //rc1 = (0,0)좌표부터 크기가 0*0
Rect rc2(10,10,60,40) //rc2 = (10,10) 좌표부터 크기가 60*40
Rect rc3 = rc1 + Size(50,40); //rc3 = (0,0)좌표부터 크기가 50*40
Rect rc4 = rc2 + Point(10,10); //rc4 = rc2 사각형의 위치가 (10,10)만큼 이동, 따라서 (20,20)좌표부터 크기가 60*40
Rect rc5 = rc3 & rc4; // &연산 = 두 사각형이 교차하는 사각형 영역 반환
Rect rc6 = rc3 | rc4; // |연산 = 두 사각형을 모두 포함하는 최소 크기의 사각형 반환
```



④RotatedRect 클래스

- center(회전된 사각형의 중심 좌표), size(사각형의 가로 및 세로 크기), angle(회전 각도 정보)의 멤버 변수
- 앞의 클래스와 달리 템플릿 클래스x
- 모든 정보를 float 자료형을 사용하여 표현

```
RotatedRect rr1(Point2f(40,30), Size2f(40,20), 30.f); //중심좌표(40,30), 크기 40*20, 시계방향으로 30도 회전된 사각형
```

- RotatedRect::points() = 회전된 사각형 객체의 네 꼭짓점 좌표를 알고 싶을 때 사용하는 함수

```
Point2f pts[4];
rr1.points(pts); //사각형의 좌측 하단 꼭지점부터 시계 방향으로 꼭지점 좌표 추출
```

- RotatedRect::boundingRect() = 특정 객체를 감싸는 최소 크기의 사각형의 바운딩 박스를 구하는 함수

```
Rect br = rr1.boundingRect();
Rect br = rr1.boundingRect2f(); //바운딩 박스 사각형의 좌표를 실수 단위로 얻고 싶을때 사용
```



⑤Range 클래스

- 범위 또는 구간을 표현하는 클래스
- start(범위의 시작), end(범위의 끝) 두 멤버 변수
- start는 범위에 포함 o, end는 범위에 포함 x

⑥String 클래스

- 문자열을 저장하고 처리

#### 2.Mat 클래스

- 행렬(matrix)을 표현하는 클래스
- 다양한 자료형의 원소를 가질 수 있음
- 채널(channel) = 행렬 원소를 구성하는 각각의 값, 채널은 모두 같은 자료형을 사용해야 함
- 타입(type) = Mat 행렬의 깊이 정보와 채널 수 정보를 합친 것

① 행렬의 생성 & 초기화

```
Mat(int rows, int cols, int type); //영상의 세로크기, 가로크기, 행렬의 타입
Mat(Size size, int type) //행렬의 크기(Size(cols,rows) 또는 Size(width, height), 행렬의 타입
Mat(int rows, int cols, int type, const Scalar& s); //s = 행렬 원소 초깃값(Scalar = 네 개의 실수 값을 저장할 수 있는 클래스)
Mat::zeros(int rows, int cols, int type); // 모든 원소가 0으로 초기화된 행렬을 만드는 함수
Mat::ones(int rows, int cols, int type); // 모든 원소가 1으로 초기화된 행렬을 만드는 함수
Mat::eye(int rows, int cols, int type); // 단위 행렬을 만드는 함수
```



② 행렬 복사

```
//픽셀 데이터 공유x, 메모리 공간을 새로 할당하여 픽셀 데이터 전체 복사
Mat::clone() const; //반환값 = this행렬의 복사본
Mat::copyTo(OutputArray m) const; //m = 복사본이 저장된 행렬, 호출행렬과 복사행렬의 크기 또는 타입이 다를 경우 
```



③ 부분 행렬 추출

- 특정 **사각형** 영역의 부분 행렬을 추출하고 싶을 때 사용
- 메모리 공간을 확보하여 복사 x, 픽셀 데이터를 공유하는 복사 => 부분 영상 추출 후 부분 영상의 픽셀 값을 변경하면 원본 영상의 픽셀 값도 함께 변경!
- 사각형이 아닌 모양을 설정하고 싶을 경우 -> 마스크 연산 응용
- 독립된 메모리 영역을 확보 후 부분 영상 추출 -> clone() 사용

```
Mat::operator()(const Rect& roi) const; //roi = 사각형 관심 영역
Mat::operator()(Range rowRange, Range colRange) const; //관심 행/열 범위
```



④ 행렬의 원소 값 참조

 1) **Mat::at() 함수**

- 행과 열을 나타내는 두 개의 정수를 인자로 받아 -> 해당 위치의 행렬 원소 값을 참조 형식으로 반환
- 임의 좌표 원소에 빈번하게 접근하는 경우 유용

```
template<typename _Tp> _Tp& Mat::at(int y, int x) // y=행 번호, x=열 번호, 반환값=_Tp&으로 형변환된 y번째 행, x번째 열의 원소 값
```



 **2) Mat::ptr() 함수**

- 특정 행의 첫 번째 원소 주소 반환
- 행 단위로 행렬 원소를 참조하는 경우 유용
- Mat::at() 함수보다 빠르게 동작

```
template<typename _Tp>
_Tp* Mat::ptr(int y) //y = 참조할 행 번호, 반환값 = _Tp*형으로 변환된 y번째 행의 시작 주소
```



**3) Matlterator_ 반복자**

- 앞 Mat::at()함수와, Mat::ptr() 함수의 경우 행렬의 크기를 벗어나면 에러 발생 -> 이를 해결하기 위해 반복자 개념을 도입해 행렬 원소를 참조할 수 있는 방법
- 행렬 크기에 상관없이 행렬 전체 원소를 차례대로 참조
- Mat::begin() = 행렬의 첫 번째 원소의 위치를 get, Mat::end() = 마지막 원소 바로 다음 위치 get
- 동작 속도에서 Mat::ptr()함수보다 느리고, Mat::at() 함수처럼 임의의 위치에 자유롭게 접근할 수 없음

#### 3.Vec 클래스 & Scalar 클래스

①Vec 클래스

- 행 벡터 + 열 벡터 = 벡터 or 벡터 행렬
- 벡터 = 같은 자료형을 가진 원소 몇개로 구성된 데이터 형식

②Scalar 클래스

- 4채널 이하의 영상에서 픽셀 값을 표현하는 용도

#### 4.InputArray & OutputArray 클래스

①InputArray 클래스

```
Mat _InputArray::getMat(int idx=-1) const; //idx = 참조할 행 번호(<0일 경우 행렬 전체 참조), 반환값 = Mat 행렬 객체
```

②OutputArray 클래스