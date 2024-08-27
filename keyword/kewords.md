### 렌더링
3차원으로부터 2차원 영상을 만들어내는 작업


### 그래픽스 API
- animation 재생, rendering, post-processing → 컴퓨터 프로그램이 작업
    - 게임 엔진이 작업
    - 게임 엔진 아래에 graphics Interface
        - Direct3D
        - OpenGL
        - `OpenGL ES` : GPU 가동
            - GPU 하드웨어 : 그래픽 카드
- 게임 엔진, 그래픽스 어플리케이션이 `OpenGL ES`를 호출하고, `OpenGL ES`는 `GPU`를 가동한다.
- `OpenGL ES`는 `GPU`의 소프트웨어 인터페이스다.

| 게임 프로그램 | 게임 |  |
| --- | --- | --- |
| 게임 엔진 | Unity, Unreal |  |
| Graphics Interface | Direct3D, OpenGL, OpenGL ES | GPU의 소프트웨어 인터페이스 |
| GPU | GPU |  |

### 행렬
- 전치 행렬
- 역행렬
- 단위 행렬

### 벡터
- 정규화
- 단위 벡터
- 법선 벡터 
- 기저
- 직교

### 내적
- 정의
    - 두 개의 벡터를 하나의 스칼라양으로 변환하는 연산
    - 두 벡터를 구성하는 각 요소들을 곱한 후 이들을 더해서 스칼라로 만들어내는 연산
- 계산
  - 두 벡터의 곱 
- 기하학적 접근
  - |A||B|cos@
  - 예각 : 양수 값
  - 둔각 : 음수 값
  - 직각 : cos90 = 0
  - cos 0 = 1 / cos180 = -1

### 외적
- 정의
  - 두 개의 벡터에서 한 개의 스칼라를 생성하는 내적과 달리, 3차원 공간에서 두 개의 벡터에서 새로운 한 개의 벡터를 생성하는 연산
- 계산
  - 신발끈 법칙
- 기하학적 접근
    - |AxB| = |A||B|sin@
    - sin 90 = 1
    - sin 0, 180, 360 = 0
    - 0 ~ 180 : 양수 값
    - 180 ~ 360 : 음수 값

### 내적, 외적 활용
- 내적
    - 시야 / 앞뒤 판별 (시야각 기준으로)
    - 반사 벡터
- 외적
  - 투영
  - 앞뒤 / 좌우 판별


### API 및 엔 좌표계
![image](https://github.com/user-attachments/assets/6afe0963-7417-4403-8728-ae7438897496)

- 왼손 : 언리얼(Z-up), 유니티(Y-up), DirectX 3D(Y-up)
- 오른손 : OpenGL


### 선형 보간
- Lerp (A, B, t) -> t는 A와 B 사이의 비율 (0.0 ~ 1.0)

### TriangleMesh
- 꼭짓점 개수의 두 배만큼 삼각형의 개수

### PolygonMesh
- 삼각형 메시의 정점을 VertexArray, IndexArray 로 표현
- VertexArray : 삼각형의 꼭짓점 배열
  - Vertex Position, Vertex normal , ...
- IndexArray : 해당하는 삼각형 VertextArray의 인덱스

### LOD
- 3D 모델 표현의 복잡성(해상도 관련)

### 법선 벡터
- 두 벡터 직교하는 벡터

### 표면 법선
- 표면에 수직인 벡터
- 반시계 방향으로 삼각형의 정점을 계산 (언리얼은 시계 방향)

### 정점 법선
-  정점을 공유하는 주변의 모든 삼각형들의 법선 벡터(표면 법선)를 평균으로 계산한 값  

### 임포트 / 익스포트
- 임포트 : Vertex Array와 Index Array로 부터 obj 파일 생성
- 익스포트 : obj로 부터 Vertex Array와 Index Array가 형성
