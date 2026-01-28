# Bag Replay Tool ROS2 - 아키텍처 및 분석 문서

## 📋 개요

**Bag Replay Tool ROS2**는 ROS2 bag 파일을 재생하기 위한 GUI 도구입니다. ROS1 버전에서 ROS2로 변환되었으며, PyQt5를 사용한 사용자 인터페이스와 `ros2 bag play` 명령어를 활용하여 구현되었습니다.

- **버전**: 2.0.0
- **타입**: ROS2 Python 패키지 (ament_python)
- **라이선스**: MIT
- **유지보수자**: amap

---

## 🏗️ 프로젝트 구조

```
bag_replay_tool_ros2/
├── bag_replay_tool_ros2/
│   ├── __init__.py
│   └── bag_replay_tool_node.py      # 메인 애플리케이션 코드
├── docs/                             # 문서 폴더
├── launch/                           # ROS2 launch 파일
├── ui/                               # PyQt5 UI 파일 (.ui)
├── test/                             # 테스트 코드
├── package.xml                       # ROS2 패키지 메타데이터
├── setup.py                          # Python 설치 스크립트
├── setup.cfg                         # setuptools 설정
└── README.md                         # 프로젝트 README
```

---

## 🔧 의존성

### 런타임 의존성
- **rclpy**: ROS2 Python 클라이언트 라이브러리
- **rosbag2_py**: ROS2 bag 파일 접근 라이브러리
- **sensor_msgs**: ROS2 센서 메시지 타입
- **std_msgs**: ROS2 표준 메시지 타입
- **PyQt5**: GUI 프레임워크
- **yaml**: YAML 파일 파싱
- **sqlite3**: ROS2 bag 데이터베이스 접근

### 테스트 의존성
- ament_copyright, ament_flake8, ament_pep257, python3-pytest

---

## 📌 핵심 기능

### 1️⃣ GUI 기반 파일 선택
- **경로**: `bag_replay_tool_node.py::on_browse_clicked()`
- **기능**: 
  - 파일 다이로그를 통해 ROS2 bag 디렉토리 선택
  - `metadata.yaml` 파일 존재 여부로 유효성 검증
  - 최근 파일 목록에 추가

```python
def on_browse_clicked(self):
    file_path = QFileDialog.getExistingDirectory(...)
    metadata_path = os.path.join(file_path, 'metadata.yaml')
    # 유효성 검증
```

### 2️⃣ Bag 파일 정보 표시
- **경로**: `bag_replay_tool_node.py::display_bag_info()`
- **기능**:
  - `ros2 bag info` 명령어 실행
  - 파일 정보를 UI 텍스트 위젯에 표시
  - 타임아웃 처리 (10초)

```python
def display_bag_info(self, bag_file):
    result = subprocess.run(['ros2', 'bag', 'info', bag_file], timeout=10)
```

### 3️⃣ Topic 로드 및 선택
- **경로**: `bag_replay_tool_node.py::load_bag_topics()`
- **기능**:
  - `metadata.yaml` 파일 파싱
  - Topic 목록을 체크박스 리스트로 표시
  - Topic 이름, 타입, 메시지 수 표시
  - 기본값: 모든 토픽 선택

```python
def load_bag_topics(self, bag_file):
    with open(metadata_path, 'r') as f:
        metadata = yaml.safe_load(f)
    # topics_with_message_count에서 topic 추출
    item = QListWidgetItem(f"{topic_name} ({topic_type}) - {message_count} msgs")
```

### 4️⃣ 재생 제어
- **경로**: `bag_replay_tool_node.py::_playback_thread_worker()`
- **기능**:
  - `ros2 bag play` subprocess로 재생
  - Play, Pause, Stop 버튼 지원
  - 무한 반복 (Loop) 옵션
  - 재생 속도 조절 (0.1x ~ 10x)
  - 시뮬레이션 시간 발행 옵션

**Subprocess 기반 아키텍처**:
```python
def _playback_thread_worker(self):
    cmd = ['ros2', 'bag', 'play', self.current_bag_file]
    if loop:
        cmd.append('--loop')
    cmd.extend(['--rate', str(rate)])
    if use_sim_time:
        cmd.append('--clock')
    process = subprocess.Popen(cmd, stdout=PIPE, stderr=PIPE)
```

### 5️⃣ 최근 파일 관리
- **경로**: `bag_replay_tool_node.py::load_recent_files()`, `save_recent_files()`
- **저장 위치**: `~/.ros2/bag_replay_tool_recent.json`
- **기능**:
  - 최대 10개 파일 관리
  - 메뉴에서 빠른 접근
  - 존재하지 않는 파일 자동 제거
  - JSON 형식 저장

```python
self.recent_files_config_path = os.path.expanduser('~/.ros2/bag_replay_tool_recent.json')
```

---

## 🎯 주요 클래스 및 메서드

### BagReplayTool 클래스

```
BagReplayTool (QtWidgets.QMainWindow)
├── __init__(node)                          # 초기화
├── set_default_values()                    # UI 기본값 설정
├── setup_menu_actions()                    # 메뉴 액션 연결
├── connect_signals()                       # UI 신호 연결
│
├── [파일 선택]
├── on_browse_clicked()                     # 파일 선택 대화
├── open_recent_file(file_path)             # 최근 파일 열기
│
├── [최근 파일 관리]
├── load_recent_files()                     # 최근 파일 로드
├── save_recent_files()                     # 최근 파일 저장
├── add_recent_file(file_path)              # 최근 파일 추가
├── update_recent_files_menu()              # 메뉴 업데이트
├── clear_recent_files()                    # 최근 파일 초기화
│
├── [Bag 정보]
├── display_bag_info(bag_file)              # Bag 정보 표시
├── load_bag_topics(bag_file)               # Topic 로드
├── get_selected_topics()                   # 선택된 Topic 반환
│
├── [재생 제어]
├── on_play_clicked()                       # Play 버튼
├── on_pause_clicked()                      # Pause 버튼
├── on_stop_clicked()                       # Stop 버튼
├── stop_playback()                         # 재생 중지
├── _playback_thread_worker()               # 재생 스레드 작업자
│
└── closeEvent(event)                       # 종료 이벤트
```

---

## 🔄 실행 흐름

### 1. 애플리케이션 시작
```
main()
  ├─ rclpy.init()
  ├─ node = rclpy.create_node('bag_replay_tool')
  ├─ app = QtWidgets.QApplication()
  ├─ window = BagReplayTool(node)
  │   ├─ UI 로드 (bag_replay_tool.ui)
  │   ├─ 신호 연결 (connect_signals)
  │   ├─ 메뉴 설정 (setup_menu_actions)
  │   └─ 기본값 설정 (set_default_values)
  ├─ ROS2 spin 타이머 시작 (100ms 간격)
  └─ app.exec_()
```

### 2. Bag 파일 선택 ~ 재생
```
사용자가 파일 선택
  ↓
on_browse_clicked()
  ├─ QFileDialog.getExistingDirectory()
  ├─ metadata.yaml 검증
  ├─ display_bag_info() → ros2 bag info 실행
  ├─ load_bag_topics() → topic 리스트 표시
  └─ add_recent_file() → 최근 파일에 추가
  ↓
사용자가 Play 버튼 클릭
  ↓
on_play_clicked()
  ├─ 기존 재생 중지 (if any)
  ├─ 플래그 초기화
  ├─ playback_thread 시작
  └─ UI 상태: "Playing"
  ↓
_playback_thread_worker() [별도 스레드]
  ├─ ros2 bag play 명령어 구성
  ├─ subprocess.Popen() 실행
  ├─ 프로세스 모니터링 (pause/stop 플래그 확인)
  └─ 완료 후 정리
```

### 3. Pause/Resume 메커니즘
```
on_pause_clicked()
  ├─ pause_playback_flag.set()
  └─ UI 상태: "Paused"
  ↓
_playback_thread_worker() [모니터링]
  ├─ pause_playback_flag 확인
  ├─ process.send_signal(signal.SIGSTOP)
  └─ is_paused = True
  ↓
사용자가 Play 버튼 클릭 (일시정지 상태에서)
  ├─ pause_playback_flag.clear()
  ├─ process.send_signal(signal.SIGCONT)
  └─ is_paused = False
```

---

## 🛠️ 기술적 세부사항

### Subprocess 기반 재생
- **왜 subprocess인가?**: ROS2 bag play 명령어의 네이티브 기능을 직접 활용
- **장점**:
  - 복잡한 프로토콜 구현 불필요
  - 표준 CLI 인터페이스 사용
  - 버전 호환성 자동 처리
  
- **명령어 구성 예시**:
```bash
ros2 bag play /path/to/bag --loop --rate 1.5 --clock --topics /topic1 /topic2
```

### Threading 모델
- **GUI 스레드**: PyQt5 메인 루프 (UI 업데이트)
- **재생 스레드**: subprocess 모니터링 (블로킹 작업)
- **ROS2 spin 타이머**: 100ms 간격 (ROS2 콜백 처리)

```python
timer = QtCore.QTimer()
timer.timeout.connect(lambda: rclpy.spin_once(node, timeout_sec=0))
timer.start(100)  # 100ms
```

### 상태 관리
| 상태 | 설명 | 플래그 |
|------|------|--------|
| **Stopped** | 재생 중지 | - |
| **Playing** | 재생 중 | - |
| **Paused** | 일시정지 | `pause_playback_flag.is_set()` |
| **Stop Signal** | 중지 신호 | `stop_playback_flag.is_set()` |

---

## 📦 설치 및 빌드

```bash
cd ~/ros2_ws
colcon build --packages-select bag_replay_tool_ros2
source install/setup.bash
```

### 실행 방법
```bash
# Launch 파일 사용
ros2 launch bag_replay_tool_ros2 bag_replay_tool.launch.py

# 또는 직접 실행
ros2 run bag_replay_tool_ros2 bag_replay_tool_node
```

---

## ⚙️ 설정 파일

### UI 파일 위치
- 패키지 설치 후: `{PKG_SHARE_DIR}/ui/bag_replay_tool.ui`
- 개발 중: `../ui/bag_replay_tool.ui` (상대 경로)

### 최근 파일 설정
- 위치: `~/.ros2/bag_replay_tool_recent.json`
- 형식: JSON 배열 (경로 리스트)
- 최대 항목: 10개

```json
[
  "/home/user/bag_files/bag_2024-01-28",
  "/home/user/bag_files/bag_2024-01-27",
  ...
]
```

---

## 🐛 에러 처리

### 주요 에러 시나리오

1. **UI 파일 로드 실패**
   ```python
   if not os.path.exists(ui_file_path):
       self.node.get_logger().error(f"UI file not found: {ui_file_path}")
       sys.exit(1)
   ```

2. **유효하지 않은 Bag 파일**
   ```python
   metadata_path = os.path.join(file_path, 'metadata.yaml')
   if not os.path.exists(metadata_path):
       QMessageBox.warning(self, "Invalid Bag", "...")
   ```

3. **Bag Info 타임아웃**
   ```python
   result = subprocess.run(..., timeout=10)
   ```

4. **Subprocess 종료 실패**
   ```python
   process.terminate()  # 시도 1
   process.wait(timeout=5)
   process.kill()       # 시도 2 (강제 종료)
   ```

---

## 📊 로깅

모든 주요 작업에는 로그가 기록됩니다:

| 레벨 | 사용 예시 |
|------|---------|
| **info** ✅ | `node.get_logger().info("✅ Bag info displayed")` |
| **warn** ⚠️ | `node.get_logger().warn("⚠️ No topics found")` |
| **error** ❌ | `node.get_logger().error("❌ Error loading topics")` |

```
🎬 Bag Replay Tool starting...
✅ UI loaded from: /opt/.../ui/bag_replay_tool.ui
✅ Loop checkbox set to checked by default
✅ Bag Replay Tool initialized
📁 Selected bag file: /path/to/bag
✅ Bag info displayed
✅ Loaded 15 topics from bag file
▶️ Starting playback thread
🎬 Starting playback: ros2 bag play /path/to/bag --rate 1.5 --clock
✅ Playback completed
🛑 Stopping playback...
```

---

## 🔮 확장 가능성

### 향후 개선 사항
1. **재생 타임라인**: 진행률 슬라이더
2. **고급 필터링**: Topic 검색, 타입별 필터
3. **메시지 뷰어**: 선택된 메시지 상세 표시
4. **다중 Bag 재생**: 여러 파일 동시 재생
5. **기록 기능**: 재생 중 메시지 기록
6. **프리셋**: 자주 사용하는 설정 저장

---

## 🚀 성능 고려사항

### 메모리 사용
- Bag 파일은 subprocess로 재생 (메모리 효율적)
- Topic 메타데이터만 메모리에 로드

### CPU 사용
- ROS2 spin: 100ms 간격 (10 Hz)
- subprocess 모니터링: 100ms 간격
- 전체적으로 경량 아키텍처

### 확장성
- 큰 Bag 파일 지원 (subprocess 기반)
- Topic 수 제한 없음 (메타데이터만 로드)

---

## 📝 라이선스

MIT License - 자유로운 사용 및 수정 가능

---

## 👨‍💻 개발 노트

### ROS1 → ROS2 변환
- `rosbag` → `rosbag2_py`
- ROS1 pub/sub → ROS2 pub/sub
- `ros1_bag play` → `ros2 bag play`

### 디버깅 팁
1. ROS2 span 활성화: `colcon build --symlink-install`
2. 로그 레벨 증가: `export RCL_LOG_LEVEL=DEBUG`
3. subprocess 로그: `cmd` 리스트 출력 확인
4. Bag 유효성: `ros2 bag info /path/to/bag`

---

**마지막 업데이트**: 2026-01-28  
**문서 버전**: 1.0
