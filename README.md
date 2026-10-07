# Furi - DirectX 11 C++프로젝트

> C++과 DirectX 11을 사용해 Furi의 보스 전투를 재현한 모작 프로젝트입니다.  
> 콤보·차지·패링 등의 전투 기능과 함께, 이동 가능 영역 검사와 파티클·화면 효과를 구현했습니다.

---

## 영상

<a href="https://youtu.be/3XedrRiZyg8">
  <img width="1936" height="1048" alt="ScreenShot00040" src="https://github.com/user-attachments/assets/7428b141-4c46-476f-a6b1-67cf8e904d31" />
</a>

- [전체 시연 영상](https://youtu.be/3XedrRiZyg8)

---

## 1. 프로젝트 개요

| 항목 | 내용 |
|---|---|
| 프로젝트명 | Furi 모작 |
| 장르 | 3D 액션 / 보스 전투 |
| 사용 기술 | C++ / DirectX 11 / HLSL |
| 개발 기간 | 입력 예정 |
| 개발 인원 | 입력 예정 |
| 담당 범위 | 입력 예정 |

---

## 2. 핵심 구현

### 2.1 플레이어 전투와 보스 패턴

공격 애니메이션의 진행률에 따라 추가 입력을 받아 다음 콤보로 연결하고, 차지 공격과 패링을 별도 상태로 처리했습니다.

보스는 페이즈에 따라 공격 패턴을 바꾸고, 원거리·근거리 전투 전환에 맞춰 카메라 시점도 변경했습니다.

### 2.2 NavMesh 데이터 로딩과 이동 가능 영역 검사

이동 가능 영역으로 지정한 Mesh에서 삼각형 데이터를 읽고, 맵에 배치된 위치·회전·크기를 반영했습니다.

일반 이동 시 다음 위치에서 Ray와 삼각형의 교차를 검사해, 이동 가능한 영역일 때만 위치를 갱신했습니다.

<img width="547" height="245" alt="image" src="https://github.com/user-attachments/assets/e8dbf8c2-fc46-4d39-b13d-a50594d6b4e7" />

### 2.3 Compute Shader와 Geometry Shader를 이용한 파티클

Compute Shader에서 파티클을 생성하고 위치·수명을 갱신했습니다. Geometry Shader에서는 각 점을 카메라를 향하는 사각형으로 확장해 표시했습니다.

수명에 따라 크기와 색상이 변하도록 하고, 생성 범위·속도·방향을 조절해 차지와 피격 등의 전투 효과에 사용했습니다.

<img width="459" height="357" alt="image" src="https://github.com/user-attachments/assets/e325a002-352e-4def-8be9-8f9a3fc295c9" />

### 2.4 섀도우맵과 화면 후처리

#### 2.4.1 섀도우맵
광원 시점에서 깊이를 저장하고, 각 지점을 같은 시점으로 변환해 깊이를 비교하여 그림자를 표현했습니다.

<img width="326" height="318" alt="image" src="https://github.com/user-attachments/assets/034d591f-7c8a-4daf-96de-3cbcc6c95a8d" />

#### 2.4.2 Post Effect
렌더링된 화면을 텍스처로 읽어 피격 시 화면 가장자리를 붉게 만드는 효과와, 잡기 연출 중 화면이 어긋나는 글리치 효과를 적용했습니다.

<img width="270" alt="Vignette 효과" src="https://github.com/user-attachments/assets/010c062e-562c-46f0-be7f-00f94f9ab9ff" />

***Vignette:*** 화면 중심과의 거리로 색상 혼합 비율을 계산하고, 원본 색상과 지정한 비네트 색상을 **선형 보간**해 가장자리로 갈수록 붉게 보이도록 했습니다. 

<img width="270" alt="Glitch Line 효과" src="https://github.com/user-attachments/assets/a858dc85-ed22-4f37-a7f5-7be29dc993be" />

***Glitch Line:*** 화면을 블록으로 나누고, 블록 좌표와 시간을 이용해 노이즈를 계산했습니다. R 채널은 원래 위치에서, G·B 채널은 노이즈에 따라 **가로 방향으로 이동한 위치에서 샘플링**하여 RGB 채널이 어긋나는 효과를 구현했습니다.

