# Bag Replay Tool ROS2 - 문서 가이드

이 폴더는 **Bag Replay Tool ROS2** 프로젝트의 전체 문서를 포함하고 있습니다.

---

## 📚 문서 구조

### 1. 🏗️ [ARCHITECTURE.md](./ARCHITECTURE.md)
**대상**: 개발자, 아키텍처 이해가 필요한 사람

**내용**:
- 프로젝트 구조 및 컴포넌트
- 핵심 기능 설명
- 클래스 계층 및 메서드 다이어그램
- 실행 흐름 및 상태 관리
- 기술적 세부사항 (threading, subprocess)
- 성능 고려사항

**언제 읽나?**
- 코드 구조를 이해하고 싶을 때
- 새 기능을 추가하려고 할 때
- 버그를 디버깅해야 할 때
- 아키텍처 의사결정을 검토할 때

---

### 2. 👨‍💼 [USER_GUIDE.md](./USER_GUIDE.md)
**대상**: 최종 사용자, GUI 사용자

**내용**:
- 설치 및 실행 방법
- GUI 사용법 (단계별 가이드)
- 실제 사용 예시
- 메뉴 기능 설명
- 문제 해결 (FAQ)
- 팁 및 트릭
- 키보드 단축키

**언제 읽나?**
- 처음 도구를 사용할 때
- 특정 기능 사용법을 모를 때
- 문제가 생겼을 때
- 고급 기능을 배우고 싶을 때

---

### 3. 🔧 [API_REFERENCE.md](./API_REFERENCE.md)
**대상**: 프로그래머, API 사용자

**내용**:
- BagReplayTool 클래스 상세 정보
- 모든 메서드 시그니처 및 설명
- 파라미터 및 반환값
- 사용 예시
- 신호-슬롯 매핑
- 타입 힌트
- 예외 처리

**언제 읽나?**
- 특정 메서드 사용법을 알아야 할 때
- 클래스를 확장하려고 할 때
- Python API를 통해 프로그래밍할 때
- 시그니처 정보가 필요할 때

---

## 🗺️ 읽기 순서 가이드

### 🆕 **처음 사용자**
```
USER_GUIDE.md
  ├─ 설치 방법
  ├─ 실행 방법
  └─ 기본 사용법
```

### 👨‍💻 **개발자**
```
ARCHITECTURE.md
  ├─ 전체 구조 이해
  ├─ 핵심 개념 학습
  └─ 실행 흐름 파악
       ↓
API_REFERENCE.md
  ├─ 클래스 상세 정보
  ├─ 메서드 활용법
  └─ 프로그래밍 패턴
```

### 🐛 **문제 해결**
```
USER_GUIDE.md
  └─ FAQ 섹션
       ↓
ARCHITECTURE.md
  └─ 기술적 세부사항
       ↓
API_REFERENCE.md
  └─ 메서드별 상세 설명
```

### 📚 **깊이 있는 학습**
```
1. USER_GUIDE.md (전체 기능 이해)
2. ARCHITECTURE.md (설계 및 구조)
3. API_REFERENCE.md (상세 구현)
4. 소스 코드 (bag_replay_tool_node.py)
```

---

## 🎯 빠른 시작

### 5분 안에 시작하기

```bash
# 1. 설치
cd ~/ros2_ws
colcon build --packages-select bag_replay_tool_ros2

# 2. 실행
ros2 launch bag_replay_tool_ros2 bag_replay_tool.launch.py

# 3. 사용
- Browse... 클릭
- Bag 파일 선택
- Play 버튼 클릭
```

자세한 내용은 [USER_GUIDE.md](./USER_GUIDE.md#-사용-방법) 참조

---

## 📋 핵심 개념 정리

### **Bag 파일**
ROS2에서 센서 및 기타 메시지를 기록한 파일. 디렉토리 형식으로 `metadata.yaml` 포함.

```
my_bag/
├── metadata.yaml      # 메타데이터
├── bag_0.db3          # 메시지 데이터
└── my_bag.yaml        # 추가 설정
```

### **Topic**
ROS2에서 메시지가 발행되는 채널. 예: `/camera/image`, `/imu/data`

### **재생 (Playback)**
기록된 메시지들을 시간 순서대로 다시 발행하는 과정

### **Subprocess**
이 도구는 `ros2 bag play` 명령을 subprocess로 실행하여 재생

---

## 🔍 자주 찾는 정보

| 찾는 내용 | 문서 | 섹션 |
|---------|------|------|
| 설치 방법 | USER_GUIDE | 설치 |
| Bag 파일 선택 | USER_GUIDE | 단계별 사용법 |
| Topic 필터링 | USER_GUIDE | Topic 선택 |
| 재생 속도 조절 | USER_GUIDE | 재생 옵션 설정 |
| 문제 해결 | USER_GUIDE | 문제 해결 |
| 아키텍처 | ARCHITECTURE | 구조 |
| Threading 모델 | ARCHITECTURE | 기술적 세부사항 |
| BagReplayTool 클래스 | API_REFERENCE | 클래스 정의 |
| play_clicked() 메서드 | API_REFERENCE | 메서드 상세 설명 |
| 신호-슬롯 | API_REFERENCE | 신호 및 슬롯 |

---

## 💬 문서 규칙

### 이모지 사용
- 🎯 목적/개요
- 📋 목차/리스트
- 🚀 시작/실행
- 🔧 설정/구현
- 🐛 문제/디버깅
- 💡 팁/트릭
- ✅ 성공/완료
- ❌ 실패/에러
- ⚠️ 경고
- 📚 참고/문서

### 코드 블록
```python
# Python 코드
```

```bash
# Shell 명령어
```

```yaml
# 설정 파일
```

### 표 형식
| 헤더1 | 헤더2 |
|------|------|
| 내용1 | 내용2 |

---

## 🔗 문서 간 링크

### 상호 참조
각 문서는 다른 문서의 관련 섹션을 참조합니다.

**예시**:
- USER_GUIDE의 "문제 해결"
  → ARCHITECTURE의 "에러 처리"로 연결
  → API_REFERENCE의 메서드 정보로 연결

---

## 📝 문서 유지보수

### 업데이트 주기
- 기능 추가 시: 즉시 문서 업데이트
- 버그 수정 시: 필요하면 문서 갱신
- 분기별 종합 검토

### 버전 관리
- 문서 상단에 "마지막 업데이트" 기록
- 주요 변경사항은 변경 로그 작성

### 기여 지침
1. 오타 발견 시: 즉시 수정
2. 부정확한 정보: 확인 후 수정
3. 누락된 내용: 추가 제안
4. 새로운 기능: 문서화 필수

---

## 🎓 학습 리소스

### 관련 ROS2 문서
- [ROS2 공식 문서](https://docs.ros.org/)
- [Rosbag2 사용 가이드](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Recording-And-Playing-Back-Data/Recording-And-Playing-Back-Data.html)

### PyQt5 리소스
- [PyQt5 공식 문서](https://doc.qt.io/qt-5/)
- [PyQt5 튜토리얼](https://www.tutorialspoint.com/pyqt5/)

### Python 스레딩
- [threading 모듈](https://docs.python.org/3/library/threading.html)
- [subprocess 모듈](https://docs.python.org/3/library/subprocess.html)

---

## ❓ 이 문서가 없나요?

### 찾는 내용이 없다면:

1. **사용 방법** → [USER_GUIDE.md](./USER_GUIDE.md) 참조
2. **코드 구조** → [ARCHITECTURE.md](./ARCHITECTURE.md) 참조
3. **API 정보** → [API_REFERENCE.md](./API_REFERENCE.md) 참조
4. **여전히 안 나오면** → 소스 코드의 주석 확인
5. **버그 의심** → 깃허브 이슈 확인

---

## 📞 지원

### 문서 오류 신고
- 오류 발견 시 수정 제안
- 명확하지 않은 부분 지적
- 누락된 정보 제시

### 개선 제안
- 더 필요한 문서 주제
- 다른 형식으로의 문서화
- 시각 자료 추가

---

## 📊 문서 통계

| 문서 | 페이지 수 | 단어 수 | 예시 수 |
|------|---------|---------|--------|
| ARCHITECTURE.md | 5 | 3,500+ | 20+ |
| USER_GUIDE.md | 6 | 4,000+ | 15+ |
| API_REFERENCE.md | 8 | 5,000+ | 30+ |
| **합계** | **19** | **12,500+** | **65+** |

---

## 🎉 축하합니다!

이제 **Bag Replay Tool ROS2**의 전체 기능과 사용법을 배웠습니다!

다음 단계:
- ✅ 도구 설치 및 실행
- ✅ 첫 번째 Bag 파일 재생
- ✅ 다양한 옵션 시도
- ✅ 커뮤니티에 피드백 제공

행운을 빕니다! 🚀

---

**마지막 업데이트**: 2026-01-28  
**문서 버전**: 1.0  
**전체 검토**: 2026-01-28

