## 1. 프로젝트 개요
코드가 모두의 컴퓨터에서 돌아갈 수 있도록 개발 워크스테이션을 만드는 작업을 목표로 합니다. 터미널로 작업 디렉토리와 권한을 정하고 Docker 설치 및 점검, 컨테이너 실행/관리를 수행했습니다.
이어서 웹 서버를 Dockerfile로 컨테이너화하고, 포트 매핑으로 접속을 확인, 바인드 마운트/볼륨으로 "변경 반영"과 "데이터 영속성"을 검증했습니다.

## 2. 실행 환경
* OS: macOS 
* 터미널: 
* Docker 버전: 28.5.2

## 3. 수행 항목 체크리스트
| 터미널 | 권한 | Docker | Dockerfile | 포트 | 볼륨 | Git | Github |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| o | o | o | o | o | o | o | o |

## 4. 수행 및 검증 내용


### 4.1 터미널 조작 및 권한 실습
| 기능 | 명령어 | 기능 | 명령어 |
| :--- | :--- | :--- | :--- |
| 현재 위치 확인 | `pwd` | 빈 파일 생성 | `touch 파일명` |
| 상위 폴더 이동 | `cd ..` | 목록 확인 (기본) | `ls` |
| 폴더 생성 | `mkdir 폴더명` | 목록 확인 (상세) | `ls -al` |
| 이름 변경/이동 | `mv 원본 목표` | 파일 내용 확인 | `cat 파일명` |
| 파일 삭제 | `rm 파일명` | 폴더 이동 | `cd 폴더명` |
| 폴더 삭제 | `rm -rf 폴더명` | 파일 복사 | `cp 원본 복사본` |
| 폴더 복사 | `cp -r 원본 복사본` | | |
<img width="1600" height="1397" alt="Image" src="https://github.com/user-attachments/assets/3242ee10-0434-493d-9f2f-11f67bfda47b" />

**권한 확인 및 변경 증거**
* 권한 변경 전: <img width="1044" height="226" alt="Image" src="https://github.com/user-attachments/assets/845c7726-d0db-4527-ab92-50cb7e8e9afc" />
* 권한 변경 후: <img width="1044" height="226" alt="Image" src="https://github.com/user-attachments/assets/d5c28e7a-1f03-4751-9e87-9d8b85f1bc9b" />

> **학습 내용:** 777은 소유자, 그룹, 기타 사용자 모두에게 읽기/쓰기, 실행 권한을 줌. 700은 소유자에게만 모든 권한을 줌.

### 4.2 Docker 운영 및 검증
* 설치 점검 결과 (`docker info` 등):
  <img width="1132" height="1362" alt="Image" src="https://github.com/user-attachments/assets/a10d754b-9583-4c50-82ed-8b55560185bd" />
<img width="1148" height="438" alt="Image" src="https://github.com/user-attachments/assets/4e5c64b0-154d-4c06-9907-060270e7adfb" />

### 4.3 Dockerfile 기반 웹 서버 컨테이너 및 포트 매핑
* **소스코드 생성 및 Dockerfile 빌드:**
  ```bash
  mkdir app
  echo "<h1>Hello Docker! This is my first web.</h1>" > app/index.html
  cat <<EOF > Dockerfile
  FROM nginx:latest
  COPY app/ /usr/share/nginx/html/
  EOF
  ```
* **포트 매핑 접속 증거:**
  <img width="1448" height="638" alt="Image" src="https://github.com/user-attachments/assets/0d40c074-f57e-4973-91a7-acb72c06a94c" />

### 4.4 바인드 마운트 반영 및 볼륨 영속성 검증
* **바인드 마운트 검증:**
* <img width="1600" height="384" alt="Image" src="https://github.com/user-attachments/assets/fa8a77c6-7bb6-4f48-9143-7bcfa46eb723" />

  ```bash
  docker run -d -p 8080:80 --name my_bind_web -v $(pwd)/app:/usr/share/nginx/html nginx
  ```
  > 도커 이미지를 다시 굽지도 않았고, 컨테이너를 껐다 켜지도 않았는데, 단순히 내 맥북에서 코드를 수정한 것만으로 도커 내부의 웹 서버 화면이 바뀌었음을 확인했습니다.

* **볼륨 영속성 검증:**
  ```bash
  docker volume create my_safe_volume
  docker run -d --name vol_test_container -v my_safe_volume:/data alpine sleep 3600
  docker exec vol_test_container sh -c "echo '이 데이터는 절대 지워지지 않습니다' > /data/secret.txt"
  docker rm -f vol_test_container
  docker run --rm -v my_safe_volume:/data alpine cat /data/secret.txt
  ```
  <img width="1600" height="377" alt="Image" src="https://github.com/user-attachments/assets/f8a5d437-3c35-4103-b6ad-2751bee62cf2" />

  > 컨테이너를 삭제해도 볼륨 내의 데이터는 사라지지 않는다는 것을 확인했습니다.

* **Git 설정 및 GitHub/VScode 연동 증거:**
  <img width="626" height="154" alt="Image" src="https://github.com/user-attachments/assets/04e317cd-aea0-433f-9a1c-7be98c91c23c" />
<img width="1600" height="1134" alt="Image" src="https://github.com/user-attachments/assets/d96b5e07-f111-4816-bc7b-08dda35c1456" />
