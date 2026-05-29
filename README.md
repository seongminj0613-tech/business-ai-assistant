# 📞 소상공인 AI 통화 요약 서비스

> 사장님의 통화를 자동으로 요약·분류하고, 예약·주문·문의 등 핵심 정보를 구조화 카드로 보여주는 비즈니스 비서 서비스

[🌐 웹 데모](https://dk1k75g0ji3vw.cloudfront.net) 

[📱 APK 다운로드](https://drive.google.com/file/d/1jJNRF2CCVcCKSpdIPUODjWL6F5exxJ-T/view?usp=sharing)

[모니터링 대시보드] http://15.165.17.218:3000/public-dashboards/97b5462a12b54bf9b827b07eeee699f4

---

## 🎯 프로젝트 소개

매일 수십 통의 전화를 받는 소상공인(요식업·뷰티업·B2B 거래처형 자영업)을 위한 AI 통화 요약 서비스입니다. 통화를 자동으로 수집·분석하여 예약·주문·문의 등 업무 정보를 **구조화된 카드**로 정리해줍니다.

### 핵심 차별화 — "통화 경로 무간섭" 원칙

본 서비스는 통화 연결·발신·수신 경로에 일체 개입하지 않습니다. 삼성 기본 통화녹음이 만든 파일을 **사후에** 수집·분석할 뿐입니다.

이 원칙 덕분에 경쟁사(에이닷·익시오)에서 반복적으로 보고되는 다음 문제를 **원천 차단**합니다.

- ❌ 통화 품질 저하 / 통화 끊김
- ❌ 부재중 알림 누락
- ❌ 자체 녹음 실패로 인한 업무 피해
- ❌ 특정 통신사 의존

> 📊 시장조사 단계에서 에이닷 부정 리뷰 27건, 익시오 부정 리뷰 71건을 직접 크롤링·분석한 결과, 부정 리뷰의 핵심 호소가 모두 "자체 녹음 방식"이 만든 구조적 부작용이라는 점을 확인했습니다.

---

## ⚙️ 핵심 기능

| 기능 | 설명 |
|------|------|
| 🎙️ **자동 통화 수집** | 삼성 기본녹음 파일을 ContentObserver + WorkManager 이중화로 자동 감지 |
| 🛡️ **자동 PII 분류** | 발신자 정보로 BUSINESS/PERSONAL/UNCLASSIFIED 자동 분류 (개인 통화 기본 미업로드) |
| 🗣️ **STT 변환** | 네이버 CLOVA Speech (한국어 화자 라벨링) — 비동기 처리 + Webhook 콜백 |
| 🤖 **하이브리드 NLP 요약** | 룰베이스 NLP 1차 → 룰 실패 슬롯이 임계 초과 시에만 GPT-4o-mini 호출 |
| 🗂️ **구조화 카드** | 고객명·연락처·일시·인원·메뉴·특이사항 자동 추출 (JSON 스키마 강제) |
| 🔊 **음성 재생** | 통화 상세에서 ExoPlayer / HTML5 audio로 원본 재생 (presigned URL 10분 만료) |
| 📊 **카테고리 분류** | 예약·주문·취소·환불·불만·문의·칭찬·긴급 8종 라벨링 |

---

## 🏗️ 시스템 아키텍처

```
┌─────────────────┐                    ┌─────────────────┐
│  Android App    │                    │ Web (Next.js)   │
│ Kotlin+Compose  │                    │ Static Export   │
│ MediaStore 감지  │                    │ S3 + CloudFront │
└────────┬────────┘                    └────────┬────────┘
         │                                      │
         │     HTTPS + Firebase ID Token        │
         └──────────────────┬───────────────────┘
                            ▼
        ┌─────────────────────────────────────┐
        │   AWS API Gateway (HTTP API)        │
        └─────────────────┬───────────────────┘
                          ▼
        ┌─────────────────────────────────────┐
        │   AWS Lambda (Python, VPC 프라이빗) │
        │   - Custom Token 발급 / CRUD        │
        │   - presigned URL 발급              │
        │   - CLOVA 비동기 호출 + Webhook 수신 │
        │   - 룰베이스 NLP + LLM Fallback     │
        └──┬──────────┬──────────┬────────────┘
           │ Interface │ Gateway │ VPC 내부
           ▼ Endpoint  ▼ Endpoint▼
        ┌────────┐ ┌────────┐ ┌────────┐
        │Secrets │ │   S3   │ │  RDS   │
        │Manager │ │ (음성) │ │MySQL 8 │
        └────────┘ └────────┘ └────────┘
                          │
                          ▼   외부 API (HTTPS)
                ┌─────────────────────┐
                │ CLOVA Speech (한국) │
                │ OpenAI (미국)       │
                │ Firebase (글로벌)   │
                │ Kakao (한국)        │
                └─────────────────────┘
```

---

## 🛠 기술 스택

### 📱 Android
- Kotlin 1.9 / Jetpack Compose (BOM 2024.02)
- Room 2.6.1, Retrofit 2.9, OkHttp 4.12
- WorkManager 2.9 (백그라운드 업로드 큐)
- Firebase Auth + Kakao SDK v2
- Media3 ExoPlayer 1.2.1

### 🌐 Web
- Next.js 14.2 (App Router, Static Export)
- React 18 + Tailwind CSS 3.4
- Firebase JS SDK + Kakao JS SDK v1
- axios 1.15

### ⚙️ Backend
- AWS Lambda (Python 3.12, 기능별 3개 분리 — auth / call / nlp)
- AWS ElastiCache Redis 7.1 (키워드 캐싱 102ms → 5ms, 토큰 캐싱, 중복 방지)
- AWS API Gateway (HTTP API)
- AWS RDS MySQL 8.0 (db.t4g.micro, 프라이빗 서브넷)
- AWS S3 (Seoul) + SSE-S3 암호화
- AWS Secrets Manager (DB 자격증명, Firebase Admin SDK)

### ☁️ Infrastructure
- AWS Seoul (ap-northeast-2) — 개인정보 국내 보관
- VPC 3계층 보안그룹 (lambda-sg / rds-sg / endpoint-sg)
- VPC Endpoint 경유 통신 (Secrets Manager Interface, S3 Gateway)
- CloudFront (글로벌 CDN)
- CloudWatch 경보 + Slack 알림 연동
- CLOVA Webhook 폴링 메커니즘 (5분 주기 자동 복구)

### 🤖 External AI
- 네이버 CLOVA Speech (장문 인식, async 모드 + Webhook)
- OpenAI GPT-4o-mini (Chat Completions, JSON 스키마)
- 학습 비사용 옵션 활성화 (양사 모두)

---

## 📂 저장소 구조

| 저장소 | 설명 | 링크 |
|--------|------|------|
| 🐍 Backend | AWS Lambda 함수 + 키워드 룰셋 | [ai-call-assistant](https://github.com/seongminj0613-tech/ai-call-assistant) |
| 🌐 Web | Next.js 관리자 대시보드 | [ai-call-assistant-web](https://github.com/seongminj0613-tech/ai-call-assistant-web) |
| 📱 Android | 통화 녹음 자동 업로드 앱 | [call-recorder-android](https://github.com/seongminj0613-tech/call-recorder-android) |
| ☁️ Lambda Layer | AWS Lambda용 Python 패키지 | [lambda-layer](https://github.com/seongminj0613-tech/lambda-layer) |

---

## 🔌 주요 API

진입점: AWS API Gateway HTTP API · 인증: `Authorization: Bearer <Firebase ID Token>`

| Method | Path | 설명 | 인증 |
|--------|------|------|------|
| POST | `/auth/kakao` | 카카오 토큰 → Firebase Custom Token 교환 | ❌ |
| POST | `/clova/webhook` | CLOVA STT 완료 콜백 수신 | ❌ (CLOVA만) |
| POST | `/stores` | 가게 등록 | ✅ |
| GET | `/stores` | 내 가게 목록 | ✅ |
| POST | `/calls/upload` | S3 presigned URL 발급 + calls INSERT | ✅ |
| POST | `/calls/{id}/process` | CLOVA STT 처리 시작 | ✅ |
| GET | `/calls` | 통화 목록 (필터: store_id, status) | ✅ |
| GET | `/calls/{id}` | 통화 상세 | ✅ |
| GET | `/calls/{id}/audio` | 음성 재생용 presigned URL (10분) | ✅ |
| PATCH | `/calls/{id}` | 분류 변경 (BUSINESS/PERSONAL) | ✅ |
| DELETE | `/calls/{id}` | 통화 삭제 | ✅ |
| GET | `/summaries/{id}` | 요약 상세 | ✅ |

> 전체 OpenAPI 3.0 명세 및 상세 기술 문서는 비공개 보관 중입니다.

---

## 🔐 보안 설계 하이라이트

개인정보를 다루는 서비스로서, 다음 네 축으로 보안을 설계했습니다.

### 1. 네트워크 격리 (3계층 보안그룹)
| 보안그룹 | 인바운드 규칙 | 효과 |
|---------|------------|------|
| `lambda-sg` | 없음 (아웃바운드 only) | Lambda 외부 직접 접근 불가 |
| `rds-sg` | TCP 3306 ← lambda-sg | RDS는 Lambda에서만 접근 |
| `endpoint-sg` | TCP 443 ← lambda-sg | Secrets Manager는 Lambda만 접근 |

### 2. 자격증명 관리
- 외부 API 키와 DB 비밀번호는 **AWS Secrets Manager에서 런타임 조회**
- 코드·환경변수에 비밀 직접 저장 금지
- RDS 마스터 비밀번호 자동 회전 30일

### 3. 인증·인가
- Firebase Authentication Custom Token 모델
- 카카오 OAuth → Firebase Custom Token 교환
- 모든 보호 엔드포인트에서 `firebase_admin.auth.verify_id_token()` 검증
- 리소스 소유권(`owner_id = 요청자 UID`) 확인

### 4. 개인정보 보호 (Privacy by Default)
- **자동 PII 분류**: 개인 통화는 기본 미업로드 (사용자 명시 동의 시에만)
- **데이터 국내 보관**: AWS Seoul + CLOVA(한국) — OpenAI 호출 단계만 국외 이전(명시 동의)
- **CLOVA·OpenAI 학습 비사용 옵션 활성화**
- **보관 기간**: 오디오 30일 / STT·요약 1년 / 로그 30일 (사용자 직접 삭제는 즉시)

---

## 📊 비기능 요구사항 (SLA)

| 지표 | 목표 |
|------|------|
| 통화 종료 → 카드 알림 도달 (평균) | **60초 이내** |
| 통화 종료 → 카드 알림 도달 (p95) | 120초 이내 |
| API 응답 (p95, /calls 목록) | 1초 이내 |
| 통화 → 카드 처리 성공률 | ≥ 95% |
| 룰 fallback (LLM 호출) 비율 | ≤ 25% (원가 절감 KPI) |
| API 서버 가용성 (월) | ≥ 99.5% |

---

## 👥 팀

| 이름 | 담당 |
|------|------|
| 정성민 | 클라우드 인프라 · Android · 백엔드 Lambda · Web 배포 |
| 강민수 | 백엔드 |
| 김진영 | 디자인 / UX |

> 본 메인 README는 정성민이 정리한 통합 문서입니다. 각 영역별 상세는 개별 저장소 README를 참조하세요.

---

## 📚 학습 내용

- 풀스택 개발 (Android + Web + Backend)
- AWS 클라우드 인프라 설계 및 보안 (VPC 3계층 / Endpoint / Secrets Manager)
- Firebase Auth + 카카오 OAuth Custom Token 교환 모델
- STT(CLOVA) + LLM(GPT-4o-mini) 비동기 AI 파이프라인 + Webhook 처리
- 룰베이스 + LLM 하이브리드 NLP로 LLM 비용 절감 설계
- 삼성 통화 녹음 자동 감지 (MediaStore + ContentObserver + WorkManager)
- Next.js Static Export + S3/CloudFront 배포

---

## 📄 라이선스

부트캠프 학습 프로젝트입니다. 코드 참고·학습 목적의 열람은 자유이나, 본 서비스의 아키텍처·디자인·문서를 무단으로 상업적 목적에 재이용하지 않기를 부탁드립니다.

---

*문서 기준: PRD v2.2 (2026.05.11), Tech Spec v2.5 (2026.05.11), API명세서 v1.0.0*
