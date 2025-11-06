# eduChatBot

교육용 AI 챗봇 애플리케이션 - Anthropic Claude와 Google Sheets를 활용한 대화형 학습 도구

## 개요

eduChatBot은 Streamlit 기반의 교육용 AI 챗봇 애플리케이션입니다. Anthropic Claude AI를 활용하여 학생들과 대화하고, 모든 대화 내용을 Google Sheets에 자동으로 저장합니다. 교육자는 대화 종료 시 자동으로 생성되는 종합 평가와 평어를 통해 학습 진행 상황을 파악할 수 있습니다.

## 주요 기능

- **AI 대화**: Anthropic Claude를 활용한 실시간 대화형 학습
- **대화 기록 저장**: 모든 대화 내용을 Google Sheets에 자동 저장
- **사용자별 관리**: 사용자별 독립적인 워크시트 생성 및 관리
- **자동 평가**: 대화 종료 시 종합 평가 및 평어 자동 생성
- **스트리밍 응답**: 실시간으로 AI 응답을 확인할 수 있는 스트리밍 지원
- **서비스 관리**: Google Sheets를 통한 서비스 활성화/비활성화 제어

## 기술 스택

- **프론트엔드**: Streamlit
- **AI 모델**: Anthropic Claude
- **데이터 저장**: Google Sheets (gspread)
- **인증**: Google OAuth2
- **언어**: Python 3.x

## 설치 방법

### 1. 저장소 클론

```bash
git clone <repository-url>
cd DDD01
```

### 2. 의존성 설치

```bash
pip install -r requirements.txt
```

### 3. 필수 패키지

```
streamlit>=1.0
streamlit_modal_input
openai>=0.10.0
langchain
anthropic
gspread
google-auth
```

## 환경 설정

### 1. Streamlit Secrets 설정

`.streamlit/secrets.toml` 파일을 생성하고 다음 정보를 입력하세요:

```toml
# Google Sheets API 인증 정보
type = "service_account"
project_id = "your-project-id"
private_key_id = "your-private-key-id"
private_key = "your-private-key"
client_email = "your-client-email"
client_id = "your-client-id"
auth_uri = "https://accounts.google.com/o/oauth2/auth"
token_uri = "https://oauth2.googleapis.com/token"
auth_provider_x509_cert_url = "https://www.googleapis.com/oauth2/v1/certs"
client_x509_cert_url = "your-cert-url"
universe_domain = "googleapis.com"

# 설정 정보 시트 URL
sheet_url = "your-google-sheets-url"
```

### 2. Google Sheets 설정

#### 2.1 Google Cloud Console 설정

1. [Google Cloud Console](https://console.cloud.google.com/)에서 새 프로젝트 생성
2. Google Sheets API 활성화
3. 서비스 계정 생성 및 JSON 키 다운로드
4. 서비스 계정 이메일 주소 복사

#### 2.2 Google Sheets 구조

**설정 시트 ("정보" 워크시트)**

| 항목 | 설명 | 예시 |
|------|------|------|
| 수업할 시트 URL | 대화 내용을 저장할 시트 URL | https://docs.google.com/... |
| 서비스 여부 | on/off | on |
| 생성형 AI | 사용할 AI 모델명 | anthropic |
| API KEY | Anthropic API 키 | sk-ant-... |
| model | 모델명 | claude-3-opus-20240229 |
| max_tokens | 최대 토큰 수 | 4096 |
| temperature | 온도 설정 | 0.7 |
| select | 선택 옵션 | - |
| system | 시스템 프롬프트 | 당신은 교육용 AI입니다... |
| a_p | 종합 평가 프롬프트 | 학습자의 이해도를... |
| e_p | 평어 프롬프트 | 한 줄로 평가하세요... |
| stream | 스트리밍 사용 여부 | true |

**대화 내용 시트 구조**

- A열: 시간 (HH:MM 형식)
- B열: 역할 (USER/ASSISTANT)
- C열: 내용

**템플릿 시트**

- 새 사용자 생성 시 복사될 기본 템플릿
- 원하는 형식으로 커스터마이징 가능

**수업요약 시트**

- 각 사용자별 워크시트 링크
- 종합 평가 및 평어 저장

## 사용 방법

### 1. 애플리케이션 실행

```bash
streamlit run app.py
```

### 2. 사용자 인터페이스

1. **사이드바**
   - 대화명 입력
   - 대화 종료 버튼

2. **메인 화면**
   - 채팅 인터페이스
   - 실시간 대화 내용 표시

3. **대화 시작**
   - 대화명을 입력하면 자동으로 초기화
   - 채팅 입력창에 메시지 입력
   - AI 응답을 실시간으로 확인

4. **대화 종료**
   - "대화 종료" 버튼 클릭
   - 자동으로 종합 평가 및 평어 생성
   - Google Sheets에 결과 저장

## 프로젝트 구조

```
DDD01/
├── app.py              # 메인 애플리케이션
├── utils/
│   └── gs.py          # Google Sheets 유틸리티
├── requirements.txt    # 의존성 목록
├── README.md          # 프로젝트 문서
└── .streamlit/
    └── secrets.toml   # 환경 변수 (git에서 제외)
```

## 주요 함수 설명

### app.py

- `initialize()`: API 및 Google Sheets 초기화
- `execute_prompt()`: AI 모델에 프롬프트 전송
- `message_processing()`: 스트리밍 응답 처리
- `end_conversation()`: 대화 종료 및 평가 생성
- `add_message()`: 메시지 저장

### utils/gs.py

- `get_authorize()`: Google Sheets API 인증
- `getSetupInfo()`: 설정 정보 로드
- `add_Content()`: 대화 내용 저장
- `get_worksheet()`: 사용자별 워크시트 관리
- `add_Hyperlink()`: 수업요약 시트에 링크 추가

## 에러 처리

- Google Sheets 연결 실패 시 재시도 및 에러 메시지
- API 호출 실패 시 상세한 에러 정보 제공
- 초기화 실패 시 사용자 친화적 안내

## 보안 고려사항

- API 키는 Streamlit secrets로 관리
- 서비스 계정 키는 절대 코드에 포함하지 않음
- `.streamlit/secrets.toml`은 `.gitignore`에 추가

## 트러블슈팅

### Google Sheets 연결 오류

```
오류: Google Sheets 인증 정보가 누락되었습니다
해결: secrets.toml 파일의 모든 필드가 올바르게 설정되었는지 확인
```

### API 호출 오류

```
오류: API 사용량 제한 오류
해결: Anthropic API 사용량 확인 및 대기 후 재시도
```

### 초기화 오류

```
오류: 초기화 중 오류가 발생했습니다
해결: API 키와 Google Sheets URL이 올바른지 확인
```

## 라이센스

본 프로젝트는 교육 목적으로 개발되었습니다.

## 기여 방법

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 개선 이력

### v1.1 (최근)

- 초기화 조건문 버그 수정
- 로깅 함수 캐싱 제거
- Dead code 정리 및 평가 기능 활성화
- 사용하지 않는 코드 제거 (config.py, wiget_on_off)
- Google Sheets 연결 에러 처리 강화
- 초기화 함수 에러 처리 추가
- 대기 시간 최적화 (3초 → 1초)
- 상세한 README 문서 작성

### v1.0 (초기 버전)

- 기본 챗봇 기능 구현
- Google Sheets 연동
- 사용자별 워크시트 관리

## 연락처

문제가 발생하거나 제안사항이 있으시면 이슈를 등록해 주세요.
