# Image Model

## Understanding Image Data
### Digital Camera & Digital Image
- Digital Camera : 빛을 이산화 신호로 변환하는 장비이며 조리개, 렌즈 및 CCD 센서를 포함한다.
- Digital Image : 연속적인 이미지 f(x,y)를 나타내는 이산화 샘플 f[x,y]를 말함. 각 요소들은 픽셀이라 불림

### Image Sensing Pipeline
![sensing pipeline.jpg](./images/sensing pipeline.jpg)

### Pinhole Camera & Lens
- Pinhole
![pinhole.png](./images/pinhole.png)
- Lens
![Lens.jpg](./images/Lens.jpg)

### 렌즈사용 시 효과(아날로그)
#### Depth of Field(DoF - 심도)
흐림이 허용되는 이미지 평면 간의 거리를 말한다. 특정 영역에 집중하고 뒷 부분 블러처리 가능.
![dof.jpg](./images/dof.jpg)

#### Aperture(조리개) & Shutter Speed
빛이 많아지면 Dof가 내려가고 뒤에 블러처리가 된다. 그래서 빛이 많아서 찰칵을 빨리하고 움직임 캐치가 좋음
![aperture.jpg](./images/aperture.jpg)

#### Field of View(FoV)
카메라로 본 3D 공간의 일부의 각도 측정

#### Vignetting
가장자리가 어두워지는 효과

#### ISO (Gain)
- 카메라에 있는 이미지 센서의 빛에 대한 민감도
- 낮으면 좀 선명하고 ISO 100은 효과 X, ISO 1600은 16배 증폭을 나타냄

### 디지털화
#### Sampling & Quantization
- 샘플링은 공간의 이산화를 나타냄
- 양자화는 강도 값의 이산화에 해당한다.

#### Demosaic
디지털 카메라의 이미지 센서에서 촬영된 베이어 패턴 데이터를 풀컬러 이미지로 변환하는 과정
- 일반적으로 베이어 필터 배열을 사용하여 R, G, B 색상을 감지하고 G 채널이 2배로 더 많다.(인간은 녹색에 가장 민감)
- 원본 센서 데이터는 불완전한 컬러 정보를 포함하고 있으며, 이를 복원하여 풀컬러 이미지를 생성해야함.

#### White Balance
사람이 보는거랑 카메라가 보는게 다름. 흰색이 사람이 보는 흰색이면 좋겠다는 의도로 적용하며 카메라별로 파라미터가 다름

#### Gamma Correction
이미지 또는 영상의 밝기를 보정하는 비선형 변환 기법
- 감마 1 원본 기준으로 1 이하면 밝아지고 이상이면 어두워짐

#### Compression
디지털 이미지 또는 영상을 저장하거나 전송 시 압축하는 과정이며 예시로 JPEG의 압축과정은 아래와 같다.
1) RGB -> YUV 변환(Y 휘도, UV 색상)
   - 사람의 눈은 색 정보보다 밝기 정보(Y)에 더 민감하기에 분리
2) 다운샘플링
   - Y데이터는 유지, U, V는 낮춤
3) DCT 변환
   - 이미지를 8x8 블록 단위로 분할하고 이산 코사인 변환(DCT) 수행
   - 저주파 성분(이미지의 주요 정보)은 유지, 고주파 성분(세부 디테일)은 제거 가능
4) 양자화
   - 사람이 구별하기 어려운 고주파 성분을 삭제하여 데이터 크기를 줄임
   - 양자화 테이블을 사용하여 작은 값들은 0으로 만듦
5) Zig-zag ordering
6) Run-length encoding & Entropy coding
   - Run-length는 0을 묶는다. 0이 5개라면 (5,0)
   - Entropy coding은 데이터의 평균적인 비트 수를 줄이는 무손실 압축 기법
   - 방법 중 하나로 허프만 코딩
![Compression.jpg](./images/Compression.jpg)


#### Color
빛의 바장에 따라 인간의 눈이 인식하는 시각적 감각
- 가산 혼합
  - 빛을 더하면 색상이 밝아짐(전부 합치면 흰색)
  - RGB 구성
- 감산 혼합
  - 색을 더하면 빛이 흡수되어 어두워짐(전부 합치면 검정)
  - CMY 구성
- Trichromatic Theory(삼색 이론)
  - 인간의 눈에는 세 종류의 원추세포가 있으며, 각각 특정 파장대의 빛 감지
    - S: 파란, M: 초록, L: 빨간

## Image Filtering
이미지 픽셀 값을 변형하여 특정한 효과를 주거나, 중요한 특징을 강조하는 과정이다.
필터링을 통해 노이즈 제거(denoise), edge 강조, feature 추출, 초해상도, 보간 등의 작업 수행이 가능하다.

### 목적
1) Low Level Vision
   - 이미지 데이터를 전처리 및 향상하기 위한 목적
   - 노이즈 제거, 샤프닝, 대비 향상 등에 사용됨
2) High Level Vision
   - 이미지에서 특징을 추출하여 분석 및 객체 인식 등에 활용
   - edge 검출, feature 추출, corner 검출 등에 사용

### Linear Filtering
- 선형 필터링은 이미지의 각 픽셀을 주변 픽셀들과 특정 가중치를 적용하여 새로운 값으로 변환하는 과정이다.
- 이 과정은 주로 Convolution 연산을 사용하여 수행됨.
![linear filtering.jpg](./images/linear filtering.jpg)

#### Linear Filter Properties
![Linear Filter.jpg](./images/Linear Filter.jpg)
- 선형성
  - 덧셈법칙: A*(B1 + B2) = (A*B1) + (A*B2)
  - 스케일링 법칙: A*(B1*B2) = B1*(A*B2)
- 변환 불변성(shift-invariant)
  - 입력 이미지가 특정 위치로 이동해도 출력이 동일한 방식으로 이동함.

#### Correlation Filter
상관 필터는 이미지 필터링에서 특정 패턴을 이미지에서 찾는 연산이며 이미지와 커널을 비교하여 유사한 부분을 찾는다.
- 필터를 그대로 사용하여 연산
- 입력 이미지 F(x, y)와 필터 H(u, v)를 그대로 곱하고 더하는 연산(가장 잘 일치 시에 최대)
![Correlation Filter.jpg](./images/Correlation Filter.jpg)

#### Convolution Filter
필터를 180도 뒤집어서 연산하며 상관 연산과의 차이점은 커널을 뒤집느냐 안 뒤집느냐 차이임. 엣지 검출에 주로 사용
![Convolution FIlter.jpg](./images/Convolution FIlter.jpg)

## Image Geometry

## Image Feature

