# 교실 화면 공유 v1

크롬북 학생의 화면을 교사 PC에서 실시간으로 확인하는 **무료 WebRTC 직접 연결 실험용** 웹앱입니다.

**현재 상태:** 초기 소스 업로드 완료. Firebase 규칙 게시, 교사 관리자 UID 등록, GitHub Pages 설정 및 실제 연결 시험은 아직 완료되지 않았습니다. 실수업 사용 전 안전성과 안정성 검증이 필요합니다.

## v1 기능
- 이메일/비밀번호 교사 로그인, 교사 관리자 UID 확인
- 7자리 교실 코드 생성 및 학생용 링크 복사
- 학생 익명 인증 + 화면 공유 동의
- 학생 영상 25분할 / 4분할 / 1명 확대
- 학생 공유 중 안내, 학생 공유 중지, 교사 전체 종료
- WebRTC 직접 연결 여부에 대한 기본 진단
- 화면 녹화·서버 영상 저장 없음

**아직 없는 기능:** 랜덤 발표, 자동 넘김, 링크 전송, 교사 화면 송출, 안정적인 자동 재연결.

## 무료 운영의 전제
- GitHub Pages와 Firebase **Spark 무료 요금제** 이용
- 영상은 Firebase로 보내지 않고 WebRTC로 직접 전송
- 무료 STUN만 사용. TURN 중계 서버가 **없으므로 직접 연결이 차단된 학교 환경에서는 작동하지 않을 수 있음**
- 무료 정책이나 사용량 제한에 따라 서비스가 중단될 수 있음. 영구 무료 가동 보장은 아님
- 결제 계정 연결 또는 Blaze 업그레이드를 하지 않음

## 1. Firebase 교사 관리자 UID 등록
1. Firebase 콘솔 → Authentication → 사용자
2. 교사 이메일/비밀번호 계정의 **전체 사용자 UID** 복사
3. Realtime Database → 데이터 탭
4. 루트에 admins 노드를 만들고, 그 아래 **전체 UID를 키로 사용하는 Boolean 값 true**를 저장

예시 (아래는 실제 UID가 아닙니다):

    admins
      EXAMPLE_TEACHER_UID: true

이 작업은 Firebase 콘솔에서 프로젝트 소유자가 수행합니다. 학생에게 이 권한을 주지 않습니다.

## 2. Firebase 데이터베이스 보안 규칙 게시
1. Realtime Database → **규칙** 탭
2. 이 저장소의 [database.rules.json](./database.rules.json) **전체 내용**을 붙여넣음
3. **게시** 버튼 클릭
4. Firebase 규칙 시뮬레이터를 이용해 학생의 관리자 쓰기가 차단되는지 검사

중요: **GitHub에 규칙 파일을 업로드했다고 Firebase에 자동 적용되는 것은 아닙니다.** 초기 잠금 규칙을 무턱대고 공개 규칙으로 변경하지 마세요.

## 3. Firebase 승인된 도메인
Authentication → 설정 → 승인된 도메인에 **slowjjb.github.io** 등록.

기존에 등록한 slowjib.github.io는 오타입니다. 삭제해도 됩니다.

## 4. GitHub Pages 사용 설정
저장소 → Settings → Pages → Build and deployment

- Source: Deploy from a branch
- Branch: main
- Folder: /(root)
- Save

예상 접속 주소: https://slowjjb.github.io/classroom-screen-share/

## 5. 테스트 순서
1. 교사 PC에서 웹앱을 열고 Firebase 교사 이메일/비밀번호로 로그인
2. '새 교실'을 눌러 7자리 코드를 생성
3. '학생 주소 복사'를 눌러 크롬북으로 열기
4. 학생은 코드·시험용 이름 입력 후 '화면 선택하고 공유 시작'
5. Chrome 공유 선택창에서 공유할 화면 승인
6. 교사 PC에서 25/4/1분할 확인
7. 학생의 공유 중지 및 교사 전체 종료 시험
8. 2명 → 4명 → 10명 → 20명으로 확대 시험

**실제 학생 정보 대신 시험용 별명을 먼저 사용하세요.** Chrome은 매번 학생이 직접 화면 공유를 허용해야 하며, 몰래 화면을 수집하지 않습니다.

## 6. 파일
- index.html: 교사·학생 UI, Firebase 인증, WebRTC 연결
- database.rules.json: 교사/학생 데이터베이스 권한 분리
- README.md: 설정 절차와 테스트 계획

## 7. 오류 대응
- 교사 권한 없음: admins/교사UID = true 여부 확인
- permission_denied: 규칙 게시 여부 및 UID 확인
- 공유 중 영상 연결 실패: 학교 방화벽, STUN, 크롬북 정책 확인; TURN은 미구현
- 공유 버튼 작동 안 함: HTTPS와 최신 Chrome 및 화면 공유 권한 확인

**주의:** 초기 v1 소스는 실제 기기 및 보안 테스트를 거치지 않은 개발 초안입니다. 작동이 확인되기 전에는 정규 수업에 사용하지 마세요.
