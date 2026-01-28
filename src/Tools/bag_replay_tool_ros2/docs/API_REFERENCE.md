# Bag Replay Tool ROS2 - API 레퍼런스

## 📚 목차
- [BagReplayTool 클래스](#bagreplaytool-클래스)
- [메서드 상세 설명](#메서드-상세-설명)
- [신호 및 슬롯](#신호-및-슬롯)
- [상수 및 전역 변수](#상수-및-전역-변수)

---

## BagReplayTool 클래스

### 클래스 정의

```python
class BagReplayTool(QtWidgets.QMainWindow):
    """GUI tool for replaying ROS2 bag files with playback control"""
```

**상속**: `QtWidgets.QMainWindow` (PyQt5)

**목적**: ROS2 bag 파일 재생 GUI 제공

---

## 메서드 상세 설명

### 초기화 메서드

#### `__init__(self, node)`

**설명**: BagReplayTool 인스턴스 생성 및 초기화

**매개변수**:
- `node` (rclpy.node.Node): ROS2 노드 객체

**반환값**: None

**동작**:
1. UI 파일 로드 (`bag_replay_tool.ui`)
2. 내부 변수 초기화
3. 신호 연결 (`connect_signals()`)
4. 메뉴 액션 설정 (`setup_menu_actions()`)
5. 기본값 설정 (`set_default_values()`)
6. 창 표시 및 준비

**예외**:
- `FileNotFoundError`: UI 파일을 찾을 수 없을 때 프로그램 종료

**예시**:
```python
node = rclpy.create_node('bag_replay_tool')
window = BagReplayTool(node)
```

---

### UI 초기화 메서드

#### `set_default_values(self)`

**설명**: UI 컨트롤의 기본값 설정

**매개변수**: 없음

**반환값**: None

**설정 항목**:
- `checkbox_loop`: True (반복 재생 활성화)
- `spinbox_rate`: 1.0 (정상 속도)
- `checkbox_clock`: False (시뮬레이션 시간 미발행)

**로깅**: 각 설정 항목마다 INFO 레벨 로그 기록

**예시**:
```python
tool = BagReplayTool(node)
# 자동으로 호출됨
```

---

#### `setup_menu_actions(self)`

**설명**: 메뉴 바의 액션 연결 및 초기화

**매개변수**: 없음

**반환값**: None

**설정 항목**:
- File → Open: `on_browse_clicked()` 연결
- File → Clear Recent: `clear_recent_files()` 연결
- Recent Files 메뉴 업데이트

**예시**:
```python
# self.action_open 액션이 생성되어 있어야 함
# (UI 파일에서 정의됨)
```

---

#### `connect_signals(self)`

**설명**: UI 신호를 슬롯에 연결

**매개변수**: 없음

**반환값**: None

**연결 항목**:
| UI 요소 | 신호 | 슬롯 |
|---------|------|------|
| btn_browse | clicked | `on_browse_clicked()` |
| btn_play | clicked | `on_play_clicked()` |
| btn_pause | clicked | `on_pause_clicked()` |
| btn_stop | clicked | `on_stop_clicked()` |

**조건부 연결**:
- UI 요소가 존재하는 경우만 연결 (`hasattr()` 확인)

**예시**:
```python
# 자동으로 호출되며 수동 호출 불필요
```

---

### 파일 선택 메서드

#### `on_browse_clicked(self)`

**설명**: Browse 버튼 클릭 시 파일 선택 대화 표시

**매개변수**: 없음

**반환값**: None

**동작**:
1. `QFileDialog.getExistingDirectory()` 표시
2. `metadata.yaml` 파일 유효성 검증
3. 유효하면:
   - Bag 파일 경로 저장
   - `display_bag_info()` 호출
   - `load_bag_topics()` 호출
   - `add_recent_file()` 호출
4. 유효하지 않으면 경고 메시지 표시

**예외 처리**:
- 파일 선택 취소: 함수 조용히 종료
- 유효성 검증 실패: 경고 메시지 표시
- 기타 예외: 에러 다이얼로그 표시

**예시**:
```python
# 클릭 이벤트로 자동 호출
tool.on_browse_clicked()  # 수동 호출도 가능
```

---

### 최근 파일 관리 메서드

#### `load_recent_files(self)`

**설명**: 디스크에서 최근 파일 목록 로드

**매개변수**: 없음

**반환값**: None

**저장소**:
- 위치: `~/.ros2/bag_replay_tool_recent.json`
- 형식: JSON 배열

**동작**:
1. 설정 파일 존재 확인
2. JSON 파일 로드
3. 존재하지 않는 파일 필터링
4. `self.recent_files` 업데이트

**예외**:
- 파일 없음: 빈 리스트로 초기화
- JSON 파싱 에러: 빈 리스트로 초기화

**예시**:
```python
# __init__에서 자동 호출
recent = tool.recent_files  # 로드된 파일 목록
```

---

#### `save_recent_files(self)`

**설명**: 최근 파일 목록을 디스크에 저장

**매개변수**: 없음

**반환값**: None

**동작**:
1. `~/.ros2/` 디렉토리 생성 (존재하지 않으면)
2. JSON 형식으로 파일 저장
3. 저장 완료 로그 기록

**예외**:
- 디렉토리 생성 실패: 에러 로그만 기록
- 파일 쓰기 실패: 에러 로그 기록

**예시**:
```python
tool.add_recent_file('/path/to/bag')
# 자동으로 save_recent_files() 호출됨
```

---

#### `add_recent_file(self, file_path)`

**설명**: 최근 파일 목록에 파일 추가

**매개변수**:
- `file_path` (str): 추가할 파일 경로

**반환값**: None

**동작**:
1. 파일이 이미 목록에 있으면 제거 (중복 방지)
2. 파일을 목록 맨 앞에 삽입
3. 최대 10개까지만 유지 (초과분 제거)
4. `save_recent_files()` 호출
5. `update_recent_files_menu()` 호출

**예시**:
```python
tool.add_recent_file('/home/user/bag_2024-01-28')
# 최근 파일 목록 업데이트
```

---

#### `update_recent_files_menu(self)`

**설명**: File 메뉴의 "Recent Files" 서브메뉴 업데이트

**매개변수**: 없음

**반환값**: None

**동작**:
1. 기존 Recent Files 메뉴 제거
2. 최근 파일이 있으면 새 메뉴 생성
3. 각 파일에 대해:
   - 액션 생성
   - 파일명으로 표시, 전체 경로는 툴팁
   - `open_recent_file()` 연결
4. 메뉴에 삽입

**예시**:
```python
tool.update_recent_files_menu()
# UI 메뉴 갱신됨
```

---

#### `open_recent_file(self, file_path)`

**설명**: 최근 파일 목록에서 파일 열기

**매개변수**:
- `file_path` (str): 열 파일의 경로

**반환값**: None

**동작**:
1. 파일 존재 확인
2. 존재하지 않으면:
   - 경고 메시지 표시
   - 최근 파일 목록에서 제거
3. 존재하면:
   - `display_bag_info()` 호출
   - `load_bag_topics()` 호출
   - `add_recent_file()` 호출

**예외**:
- 파일 없음: 경고 다이얼로그 표시

**예시**:
```python
tool.open_recent_file('/home/user/bag_2024-01-28')
```

---

#### `clear_recent_files(self)`

**설명**: 최근 파일 목록 초기화

**매개변수**: 없음

**반환값**: None

**동작**:
1. 확인 대화 표시
2. 사용자가 "Yes" 선택하면:
   - `self.recent_files` 초기화
   - `save_recent_files()` 호출
   - `update_recent_files_menu()` 호출

**예시**:
```python
tool.clear_recent_files()
# 사용자 확인 후 최근 파일 초기화
```

---

### Bag 정보 처리 메서드

#### `display_bag_info(self, bag_file)`

**설명**: Bag 파일의 메타데이터 정보 표시

**매개변수**:
- `bag_file` (str): Bag 파일 경로

**반환값**: None

**동작**:
1. `subprocess.run(['ros2', 'bag', 'info', ...])` 실행
2. 표준 출력을 텍스트 위젯에 설정
3. 실패 시 에러 메시지 표시

**타임아웃**: 10초

**예외**:
- Subprocess 타임아웃: 타임아웃 에러 메시지 표시
- 기타 실패: 에러 메시지 표시

**출력 형식**:
```
Files:             bag_0.db3
Bag size:          1.2 GB
Storage id:        sqlite3
Duration:          600.123s
...
```

**예시**:
```python
tool.display_bag_info('/home/user/bag_2024-01-28')
```

---

#### `load_bag_topics(self, bag_file)`

**설명**: Bag 파일에서 Topic 목록 로드 및 표시

**매개변수**:
- `bag_file` (str): Bag 파일 경로

**반환값**: None

**동작**:
1. `metadata.yaml` 파일 읽기
2. YAML 파싱
3. `rosbag2_bagfile_information` → `topics_with_message_count` 추출
4. 각 Topic에 대해:
   - `self.bag_topics` 딕셔너리에 추가
   - UI 리스트에 체크박스 아이템 추가
   - 기본값: 모두 체크됨

**Topic 정보**:
- 이름 (name)
- 타입 (type)
- 메시지 수 (message_count)

**UI 표시**:
```
☑ /topic_name (sensor_msgs/msg/Image) - 1000 msgs
☑ /another_topic (nav_msgs/msg/Odometry) - 500 msgs
```

**예외**:
- 파일 찾기 실패: 에러 로그
- YAML 파싱 실패: 에러 로그
- 메타데이터 형식 오류: 경고 로그

**예시**:
```python
tool.load_bag_topics('/home/user/bag_2024-01-28')
# 토픽 리스트가 UI에 표시됨
print(tool.bag_topics)  # {'topic_name': 'type', ...}
```

---

#### `get_selected_topics(self)`

**설명**: Topic 리스트에서 선택된 Topic 반환

**매개변수**: 없음

**반환값**: List[str] - 선택된 Topic 이름 목록

**동작**:
1. 리스트 위젯 반복
2. `checkState()` 확인
3. 체크된 아이템의 Topic 이름 추출
4. 리스트로 반환

**예시**:
```python
selected = tool.get_selected_topics()
# ['/topic1', '/topic3', '/topic5']
```

---

### 재생 제어 메서드

#### `on_play_clicked(self)`

**설명**: Play 버튼 클릭 처리

**매개변수**: 없음

**반환값**: None

**동작**:
1. Bag 파일 확인
2. 일시정지 상태면 재개:
   - `pause_playback_flag` 클리어
   - UI 상태 업데이트
3. 새 재생 시작:
   - 기존 재생 중지 (있으면)
   - 플래그 초기화
   - 스레드 시작
   - UI 상태 "Playing"으로 설정

**전제조건**:
- `self.current_bag_file` 설정되어야 함

**예외**:
- Bag 파일 미선택: 경고 메시지
- 파일 없음: 에러 메시지

**예시**:
```python
# UI에서 자동 호출
tool.on_play_clicked()
```

---

#### `on_pause_clicked(self)`

**설명**: Pause 버튼 클릭 처리

**매개변수**: 없음

**반환값**: None

**동작**:
1. 재생 스레드 확인
2. 스레드가 실행 중이면:
   - `pause_playback_flag` 설정
   - `is_paused = True`
   - UI 상태 "Paused"로 설정
3. 실행 중이 아니면 경고 로그

**예시**:
```python
tool.on_pause_clicked()
```

---

#### `on_stop_clicked(self)`

**설명**: Stop 버튼 클릭 처리

**매개변수**: 없음

**반환값**: None

**동작**:
- `stop_playback()` 호출

**예시**:
```python
tool.on_stop_clicked()
```

---

#### `stop_playback(self)`

**설명**: 현재 재생 중지

**매개변수**: 없음

**반환값**: None

**동작**:
1. 스레드 참조 저장
2. `stop_playback_flag` 설정
3. `pause_playback_flag` 클리어
4. 스레드 종료 대기 (최대 5초)
5. 종료하지 않으면 경고 로그
6. `is_paused` 초기화
7. UI 상태 "Stopped"로 설정

**스레드 안전성**: join() 사용으로 안전하게 처리

**예시**:
```python
tool.stop_playback()
```

---

#### `_playback_thread_worker(self)`

**설명**: 별도 스레드에서 Bag 재생 수행

**매개변수**: 없음

**반환값**: None

**동작**:
1. 재생 옵션 수집:
   - loop 플래그
   - rate 값
   - use_sim_time 플래그
   - 선택된 Topic 목록

2. 명령어 구성:
   ```bash
   ros2 bag play <path> [--loop] [--rate N] [--clock] [--topics ...]
   ```

3. Subprocess 시작:
   ```python
   process = subprocess.Popen(cmd, stdout=PIPE, stderr=PIPE)
   ```

4. 모니터링 루프:
   - 프로세스 상태 확인
   - Pause/Stop 플래그 확인
   - SIGSTOP/SIGCONT 시그널 전송

5. 정리:
   - Subprocess 종료
   - 스레드 정보 초기화

**신호 사용**:
- SIGSTOP: 일시정지
- SIGCONT: 재개

**예외 처리**:
- 각 단계에서 예외 처리
- Finally 블록으로 정리 보장

**로깅**:
- 시작: "Starting playback: ..."
- 종료: "Playback completed"
- 에러: "Playback error: ..."

**예시**:
```python
# 스레드에서 자동 호출 - 직접 호출하지 않음
```

---

### 윈도우 이벤트 메서드

#### `closeEvent(self, event)`

**설명**: 윈도우 종료 이벤트 처리

**매개변수**:
- `event` (QCloseEvent): 종료 이벤트 객체

**반환값**: None

**동작**:
1. 로그: "Bag Replay Tool closing..."
2. 재생 스레드 확인
3. 실행 중이면 `stop_playback()` 호출
4. 이벤트 수락: `event.accept()`

**예시**:
```python
# 윈도우 닫기 버튼으로 자동 호출
```

---

## 신호 및 슬롯

### PyQt5 신호-슬롯 매핑

| UI 요소 | 신호 | 슬롯 |
|---------|------|------|
| btn_browse | clicked() | on_browse_clicked() |
| btn_play | clicked() | on_play_clicked() |
| btn_pause | clicked() | on_pause_clicked() |
| btn_stop | clicked() | on_stop_clicked() |
| action_open | triggered() | on_browse_clicked() |
| action_clear_recent | triggered() | clear_recent_files() |
| recent_file_actions | triggered() | open_recent_file() |

---

## 상수 및 전역 변수

### 인스턴스 변수

#### 파일 관련
```python
self.current_bag_file: str | None  # 현재 열린 Bag 파일 경로
self.bag_topics: Dict[str, str]    # {topic_name: topic_type}
self.recent_files: List[str]       # 최근 파일 목록
self.max_recent_files: int = 10    # 최대 최근 파일 수
self.recent_files_config_path: str # 설정 파일 경로
```

#### 재생 제어
```python
self.playback_thread: threading.Thread | None      # 재생 스레드
self.stop_playback_flag: threading.Event()         # 중지 신호
self.pause_playback_flag: threading.Event()        # 일시정지 신호
self.is_paused: bool = False                       # 일시정지 상태
self.publishers: Dict[str, rclpy.Publisher]        # Topic 발행자
```

#### UI
```python
self.node: rclpy.node.Node  # ROS2 노드 객체
```

---

## 전역 함수

### `main(args=None)`

**설명**: 애플리케이션 진입점

**매개변수**:
- `args` (List[str] | None): 명령줄 인자 (선택사항)

**반환값**: int - 종료 코드

**동작**:
1. ROS2 초기화: `rclpy.init()`
2. 노드 생성: `rclpy.create_node()`
3. PyQt5 앱 생성: `QtWidgets.QApplication()`
4. BagReplayTool 생성
5. ROS2 spin 타이머 설정 (100ms)
6. PyQt5 메인 루프 시작: `app.exec_()`
7. 정리: `rclpy.shutdown()`, `node.destroy_node()`

**예외 처리**:
- 모든 예외 catch 및 로깅
- 안전한 종료 보장

**실행**:
```bash
python3 -m bag_replay_tool_ros2.bag_replay_tool_node
```

---

## 타입 힌트

문서에서 사용된 타입:

```python
str              # 문자열
int              # 정수
bool             # 불린
List[str]        # 문자열 목록
Dict[str, str]   # 문자열 매핑
None             # 없음
```

---

## 예외 처리 체인

```python
Exception (기본)
├── FileNotFoundError (UI 파일)
├── ValueError (설정값)
├── subprocess.TimeoutExpired (Bag info)
└── OSError (파일 I/O)
```

---

## 성능 고려사항

### 메모리
- Bag 메타데이터만 메모리 로드 (전체 데이터 아님)
- Topic 목록: O(n) 메모리 (n = topic 수)
- 최근 파일: 최대 10개 (약 1KB)

### CPU
- UI 이벤트 루프: 주기적
- ROS2 spin: 100ms 간격
- Subprocess 모니터링: 100ms 간격

### 네트워크
- subprocess에 의해 관리
- Topic 필터링으로 대역폭 제어

---

**마지막 업데이트**: 2026-01-28  
**문서 버전**: 1.0  
**호환성**: ROS2 Humble 이상
