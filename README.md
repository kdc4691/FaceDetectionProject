# 스마트 출석 카메라 (FaceDetectionProject)
- 201644039 강동찬
- 201644064 임태현
- 201644092 장태영
- 201344056 윤종섭

# 목차
+ 프로젝트 목적
+ 프로젝트 목표 와 기대효과
+ 프로젝트 주요내용
	- 사용프로그램 및 모듈
	- 구현방법 
	- 프로그램 코드 및 설명
	- 프로그램 동작원리요약
+ 프로젝트 결과물 


# 프로젝트 목적
+ 라즈베리파이 및 OpenCV 기반으로 하는 software및 hardware 개발 프로젝트로서 
각 사용자들의 얼굴 인식을 하여 사용자들 사이에서 이름, 유사도 정보를 공유할 수 있게 의사 소통하는 시스템을 구현합니다

# 프로젝트 목표 및 기대효과
### 목표
+ 화상회의 참여자들의 얼굴을 촬영. 이를 학습시켜 각 참여자들의 신분을 나타낸다.

### 기대효과
+ 출석에 활용하는 시간 감소 
+ 실시간 촬영을 통한 회의 참여 유도 및 효율향상
+ 원격회의 서비스 활용도 증가

# 프로젝트 주요내용 
## 1. 사용프로그램 및 모듈
### 1) OpenCv
![opencv](https://user-images.githubusercontent.com/71091406/101994107-34810900-3d03-11eb-9bd7-680510086ecf.png)


1. OpenSource Computer Vision의 약자로 
    다양한 영상/동영상 처리에 사용할 수 있는 오픈소스 라이브러리 

2. C++, C, Python 및 Java와 같은 다양한 인터페이스를 지원
    Windows, Linux, Mac OS, iOS 및 Android같은 다양한 OS를 지원

3. OpenCV는 멀티 코어 프로세싱을 지원하기 때문에 다양한 상황에 응용이 가능
   (예를 들어 윤곽선 검출,  노이즈 제거,  이미지 스티칭을 이용한 파노라믹 사진제작)

4. 실시간 이미지 프로세싱에 중점을 둔 라이브러리


### 2) Haar Cascade

1. 머신 러닝기반의 오브젝트 검출 알고리즘

2. 비디오 또는 이미지에서 오브젝트를 검출하기 위해 사용, 직사각형 영역으로 구성되는 특징을 사용기 때문에 픽셀을 직접 사용할 때 보다 동작 속도가 빠름

3. 사람의 얼굴을 인식하기위한 Haar Cascade 방식의 알고리즘은 머신 러닝의 컨볼루션 신경망 분석 기법과 유사

4. Haarcascade 라이브러리를 사용하여 인식할 수 있는 오브젝트는 사람의 정면 얼굴, 얼굴 안의 눈, 고양이 얼굴, 사람의 몸 각 부분들, 컬러 및 차량을 포함

#### Haar Cascade 알고리즘 4단계

1. Haar Feature Selection 
    -  이미지 전체 스캔하고  하르특징 계산하여 영역내 픽셀 합의 차이 이용
2. Creating  Integral Images 
    - 픽셀의 합을 구하기는 것을 빠르게 하기 위해 적분 이미지 사용
3. Adaboost Training
   - 얼굴 검출을 하는데 도움이 되는 의미 있는 특징계산
4. Cascading Classifiers 
   -  현재 윈도우가 있는 영역이 얼굴 영역인지를 단계별로  체크

## 2. 구현방법
![구현방법](https://user-images.githubusercontent.com/71091406/101994156-74e08700-3d03-11eb-82a2-92474e30cbe0.png)

## 3. 프로그램 코드 및 설명

### 3) face_training.py

import cv2
import numpy as np
from PIL import Image
import os

path = 'dataset'
recognizer = cv2.face.LBPHFaceRecognizer_create()
detector = cv2.CascadeClassifier("haarcascades/haarcascade_frontalface_default.xml");

def getImagesAndLabels(path):
    imagePaths = [os.path.join(path,f) for f in os.listdir(path)]     
    faceSamples=[]
    ids = []
    for imagePath in imagePaths:
        PIL_img = Image.open(imagePath).convert('L') # convert it to grayscale
        img_numpy = np.array(PIL_img,'uint8')
        id = int(os.path.split(imagePath)[-1].split(".")[1])
        faces = detector.detectMultiScale(img_numpy)
        for (x,y,w,h) in faces:
            faceSamples.append(img_numpy[y:y+h,x:x+w])
            ids.append(id)
    return faceSamples,ids
print ("\n [INFO] Training faces. It will take a few seconds. Wait ...")
faces,ids = getImagesAndLabels(path)
recognizer.train(faces, np.array(ids))

recognizer.write('trainer/trainer.yml') # recognizer.save() worked on Mac, but not on Pi
print("\n [INFO] {0} faces trained. Exiting Program".format(len(np.unique(ids))))

## 4. 프로그램 동작원리 요약


# 프로젝트 결과물
