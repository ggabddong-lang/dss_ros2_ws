# Bag Replay Tool ROS2 - 사용자 가이드

## 🎯 목적

ROS2 bag 파일 재생 시 GUI를 통해 쉽게 재생 제어, Topic 선택, 재생 속도 조절 등을 할 수 있는 도구입니다.

---

## 📥 설치

### 1️⃣ 패키지 빌드
```bash
cd ~/ros2_ws
colcon build --packages-select bag_replay_tool_ros2
source install/setup.bash
```

### 2️⃣ 의존성 확인
```bash
# Python 패키지 설치 여부 확인
python3 -c "from PyQt5 import QtWidgets; print('✅ PyQt5 installed')"
```

PyQt5가 없다면 설치:
```bash
sudo apt install python3-pyqt5
```

---

## 🚀 실행 방법

### 방법 1: Launch 파일 사용 (추천)
```bash
ros2 launch bag_replay_tool_ros2 bag_replay_tool.launch.py
```

### 방법 2: 직접 실행
```bash
ros2 run bag_replay_tool_ros2 bag_replay_tool_node
```

### 방법 3: Python으로 직접 실행
```bash
cd ~/ros2_ws
source install/setup.bash
python3 -m bag_replay_tool_ros2.bag_replay_tool_node
```

---

## 🎮 GUI 사용법

### 메인 인터페이스 구성

```
┌─────────────────────────────────────────────┐
│  File   Edit   View   Help                  │  ← 메뉴 바
├─────────────────────────────────────────────┤
│ Bag File Selection                          │
│ [          파일 경로           ] [Browse...] │
├─────────────────────────────────────────────┤
│ Bag Information                 Topics       │
│ ┌────────────────────────┐ ┌──────────────┐ │
│ │ 메타데이터 정보        │ │ ☑ /topic1    │ │
│ │ - Duration: 10s        │ │ ☑ /topic2    │ │
│ │ - Topics: 5            │ │ ☑ /topic3    │ │
│ │ - Messages: 1000       │ │ ☐ /topic4    │ │
│ │                        │ │              │ │
│ └────────────────────────┘ └──────────────┘ │
├─────────────────────────────────────────────┤
│ Playback Controls                           │
│ [▶ Play] [⏸ Pause] [⏹ Stop]                │
├─────────────────────────────────────────────┤
│ Playback Options                            │
│ ☑ Loop         Rate: 1.0x   ☐ Publish Clock │
├─────────────────────────────────────────────┤
│ Status: Ready                               │
└─────────────────────────────────────────────┘
```

### 단계별 사용법

#### 1단계: Bag 파일 선택
**방법 A: Browse 버튼 사용**
1. "Browse..." 버튼 클릭
2. Bag 파일이 있는 디렉토리 선택
3. OK 버튼 클릭

**방법 B: 메뉴에서 열기**
- File → Open Bag File... (또는 Ctrl+O)

**방법 C: 최근 파일 사용**
- File → Recent Files → 원하는 파일 선택

#### 2단계: Topic 선택 (선택사항)
Topic 목록에서:
- ✅ **체크됨**: 재생 시 발행됨
- ☐ **체크 안 됨**: 재생 시 발행 안 됨

모든 Topic 필요 → 그대로 두기
특정 Topic만 필요 → 불필요한 것은 체크 해제

#### 3단계: 재생 옵션 설정

**Loop (반복 재생)**
- ☑ 활성화: 끝까지 재생 후 처음부터 다시 재생
- ☐ 비활성화: 한 번만 재생

**Rate (재생 속도)**
- 값 범위: 0.1x ~ 10x
- 예시:
  - 0.5x: 절반 속도 (슬로우 모션)
  - 1.0x: 정상 속도 (기본값)
  - 2.0x: 2배 속도 (빠른 재생)

**Publish Clock (시뮬레이션 시간 발행)**
- ☑ 활성화: `/clock` Topic으로 시뮬레이션 시간 발행
  - Gazebo 등과 동기화 필요할 때 사용
  - 다른 노드가 `/clock` 구독 필요
- ☐ 비활성화: 시뮬레이션 시간 발행 안 함

#### 4단계: 재생 제어

| 버튼 | 기능 | 설명 |
|------|------|------|
| **▶ Play** | 재생 | Bag 파일 재생 시작. 일시정지 상태에서는 재개 |
| **⏸ Pause** | 일시정지 | 현재 위치에서 멈춤. Play 버튼으로 재개 가능 |
| **⏹ Stop** | 중지 | 재생 완전히 중지. 처음부터 시작해야 함 |

---

## 💡 사용 예시

### 예시 1: 기본 재생
```
1. Browse... → bag 디렉토리 선택
2. (모든 Topic 자동 선택됨)
3. ▶ Play 버튼 클릭
4. 재생 시작
```

### 예시 2: 특정 Topic만 재생
```
1. Browse... → bag 디렉토리 선택
2. Topic 목록에서 필요한 것만 체크
   예: /camera/image만 필요하면 다른 것 체크 해제
3. ▶ Play 버튼 클릭
4. /camera/image 메시지만 발행됨
```

### 예시 3: 슬로우 모션 재생
```
1. Bag 파일 선택
2. Rate 값 변경: 1.0 → 0.5
3. ▶ Play 버튼 클릭
4. 절반 속도로 재생 (디버깅에 유용)
```

### 예시 4: 무한 반복 재생 (자동 테스트)
```
1. Bag 파일 선택
2. ☑ Loop 체크
3. Rate: 1.0x
4. ▶ Play 버튼 클릭
5. 자동으로 반복 재생
6. ⏹ Stop으로 중지 시까지 계속 반복
```

### 예시 5: 다른 노드와 동기화
```
1. Bag 파일 선택
2. ☑ Publish Clock 체크
3. ▶ Play 버튼 클릭
4. 다른 노드들이 /clock 구독 시 시간 동기화
   (예: Gazebo, RVIZ 등)
```

---

## 🔍 Bag 파일 정보 이해하기

Bag 정보 영역에 표시되는 내용:

```
Files:             bag_0.db3 (1.2 GB)
Bag size:          1.2 GB
Storage id:        sqlite3
Duration:          600.123s
Start date:        Sun Jan 28 15:30:45 2026
End date:          Sun Jan 28 15:40:45 2026
Messages:          50000
Topics:
  /camera/image:           10000 msgs
  /imu/data:              15000 msgs
  /odom:                  10000 msgs
  /tf:                    15000 msgs
```

**각 항목 설명**:
- **Duration**: 전체 재생 시간
- **Messages**: 전체 메시지 수
- **Topics**: Topic별 메시지 수 (많을수록 더 많은 데이터)

---

## 📋 메뉴 기능

### File 메뉴
- **Open Bag File...** (Ctrl+O)
  - 새로운 Bag 파일 열기
  
- **Recent Files**
  - 최근 10개 파일 목록
  - 마우스 호버로 전체 경로 표시
  
- **Clear Recent Files**
  - 최근 파일 목록 초기화

- **Exit** (Ctrl+Q)
  - 프로그램 종료

### Edit 메뉴
(향후 기능)

### View 메뉴
(향후 기능)

### Help 메뉴
- **About**
  - 프로그램 정보

---

## ⚙️ 설정

### 최근 파일 저장소
- 위치: `~/.ros2/bag_replay_tool_recent.json`
- 자동 생성 (처음 사용 시)
- 최대 10개 파일 저장

### 기본 설정값
- Loop: 활성화 (체크됨)
- Rate: 1.0x (정상 속도)
- Publish Clock: 비활성화 (체크 안 됨)
- Topic: 모두 선택

---

## 🐛 문제 해결

### 🔴 "Invalid Bag" 경고
**원인**: 선택한 디렉토리가 유효한 Bag 파일이 아님
**해결**:
```bash
# Bag 파일 유효성 확인
ros2 bag info /path/to/bag_directory

# 올바른 경로인지 확인
ls -la /path/to/bag_directory/
# metadata.yaml, *.db3 파일 있어야 함
```

### 🔴 "Error getting bag info"
**원인**: `ros2 bag info` 명령 실패
**해결**:
```bash
# ROS2 환경 확인
echo $ROS_DISTRO
source /opt/ros/{distro}/setup.bash

# rosbag2 패키지 설치 확인
sudo apt install ros-{distro}-rosbag2
```

### 🔴 UI 파일을 찾을 수 없음
**원인**: 패키지가 올바르게 빌드되지 않음
**해결**:
```bash
cd ~/ros2_ws
colcon build --packages-select bag_replay_tool_ros2 --symlink-install
source install/setup.bash
```

### 🔴 "PyQt5 not found"
**원인**: PyQt5 미설치
**해결**:
```bash
sudo apt install python3-pyqt5
```

### 🔴 재생이 시작되지 않음
**원인 1**: Bag 파일이 없음
```bash
# 파일 존재 확인
ls -la /path/to/bag_directory/
```

**원인 2**: ROS2 환경 미설정
```bash
source ~/ros2_ws/install/setup.bash
```

**원인 3**: 다른 프로세스가 포트 점유
```bash
# 기존 bag play 프로세스 종료
pkill -f "ros2 bag play"
```

---

## 📊 Topic 분석 팁

### 메시지 수가 많은 Topic 제거
- 대역폭이 제한적이면 고주파 Topic 제거
- 예: `/tf_static` (높은 주파수) 제거

### 필요한 Topic만 선택
```bash
# 먼저 Bag 파일 분석
ros2 bag info /path/to/bag
```

### 속도 조절로 데이터 검증
- 0.1x로 느리게 재생하며 데이터 확인
- 이상 치 발견 시 멈추고 재검토

---

## 🚀 고급 팁

### Gazebo와 동기화
```bash
# Terminal 1: Gazebo 실행
gazebo --verbose

# Terminal 2: Bag 재생 (Clock 발행)
ros2 launch bag_replay_tool_ros2 bag_replay_tool.launch.py
# UI에서 "Publish Clock" 체크

# Terminal 3: RViz 실행
rviz2 -d config.rviz
```

### 성능 최적화
- 큰 Topic (예: point cloud) 제외
- 재생 속도 2배 이상으로 빠르게
- Topic 필터링으로 네트워크 대역폭 절감

### 데이터 검증
```bash
# 메시지 샘플링
ros2 topic echo /topic_name

# 메시지 Hz 확인
ros2 topic hz /topic_name

# 메시지 대역폭 확인
ros2 topic bw /topic_name
```

---

## 📱 키보드 단축키

| 단축키 | 기능 |
|--------|------|
| Ctrl+O | Bag 파일 열기 |
| Ctrl+Q | 종료 |
| Space | Play/Pause 토글 (향후 기능) |

---

## 📝 로그 확인

### 런타임 로그 보기
```bash
# Terminal에서 직접 실행
ros2 run bag_replay_tool_ros2 bag_replay_tool_node

# 로그 출력 예
# 🎬 Bag Replay Tool starting...
# ✅ UI loaded from: /opt/ros/...
# 📁 Selected bag file: /home/user/bag_2024-01-28
# ✅ Loaded 15 topics from bag file
# ▶️ Starting playback thread
```

### 디버그 로그 활성화
```bash
export RCL_LOG_LEVEL=DEBUG
ros2 launch bag_replay_tool_ros2 bag_replay_tool.launch.py
```

---

## 💬 자주 묻는 질문

**Q: Bag 파일을 계속 반복 재생하고 싶습니다.**  
A: Loop 체크박스를 활성화하고 Play를 클릭하세요.

**Q: 특정 Topic의 메시지만 추출할 수 있나요?**  
A: 네, Topic 목록에서 필요한 것만 체크하면 됩니다.

**Q: 재생 속도를 조절할 수 있나요?**  
A: 네, Rate 스핀박스에서 0.1x ~ 10x 범위로 조절 가능합니다.

**Q: 여러 Bag 파일을 동시에 재생할 수 있나요?**  
A: 현재는 한 번에 하나씩만 가능합니다. 여러 번 실행하면 됩니다.

**Q: 최근 파일 목록을 삭제하려면?**  
A: File → Clear Recent Files를 클릭하세요.

**Q: Bag 파일이 손상되었는지 확인하려면?**  
A: `ros2 bag info /path/to/bag` 명령으로 유효성 검사하세요.

---

## 🔗 관련 링크

- [ROS2 Bag 문서](https://docs.ros.org/en/humble/Concepts/Intermediate/Rosbag2.html)
- [rosbag2 CLI](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Recording-And-Playing-Back-Data/Recording-And-Playing-Back-Data.html)
- [PyQt5 문서](https://doc.qt.io/qt-5/)

---

**마지막 업데이트**: 2026-01-28  
**문서 버전**: 1.0
