# 📞 Business AI Assistant

> 소상공인을 위한 AI 통화 비서 서비스

소상공인이 받는 모든 전화를 자동으로 녹음하고, AI가 통화 내용을 분석·요약하여 핵심 정보를 한눈에 보여주는 서비스입니다.

## 🎯 프로젝트 소개

소상공인은 매일 수많은 전화를 받지만, 영업 중에는 메모할 시간이 없어 중요한 내용을 놓치기 쉽습니다. 본 서비스는 통화를 자동으로 녹음·분석하여 다음과 같은 가치를 제공합니다.

- **자동 녹음**: 갤럭시 통화 녹음 기능 활용
- **STT 변환**: 통화 내용을 텍스트로 변환
- **AI 요약**: Claude를 활용한 통화 요약
- **키워드 추출**: 예약, 주문, 환불 등 핵심 키워드 자동 감지
- **대시보드**: 모든 통화 이력과 요약을 한눈에 확인

## 🛠 기술 스택

### 📱 Android
- Kotlin + Jetpack Compose
- Room, Retrofit, WorkManager
- Firebase Auth + Kakao SDK

### 🌐 Web
- Next.js 14, JavaScript, Tailwind CSS
- Firebase Auth + Kakao SDK

### ⚙️ Backend
- Python 3.12, FastAPI, AWS Lambda
- MySQL (AWS RDS), CLOVA Speech, Claude API

### ☁️ Infrastructure
- AWS (Lambda, RDS, S3, API Gateway, VPC, Secrets Manager)
- Firebase Authentication

## 📂 저장소 구조

| 저장소 | 설명 | 링크 |
|--------|------|------|
| 🐍 Backend | FastAPI 서버 + 키워드 엔진 | [ai-call-assistant](https://github.com/seongminj0613-tech/ai-call-assistant) |
| 🌐 Web | Next.js 관리자 대시보드 | [ai-call-assistant-web](https://github.com/seongminj0613-tech/ai-call-assistant-web) |
| 📱 Android | 통화 녹음 자동 업로드 앱 | [call-recorder-android](https://github.com/seongminj0613-tech/call-recorder-android) |
| ☁️ Lambda Layer | AWS Lambda용 Python 패키지 | [lambda-layer](https://github.com/seongminj0613-tech/lambda-layer) |

## 📌 주요 API

| 메서드 | 엔드포인트 | 설명 |
|--------|----------|------|
| `POST` | `/auth/kakao` | 카카오 로그인 |
| `POST` | `/stores` | 가게 등록 |
| `GET` | `/stores` | 가게 목록 |
| `POST` | `/calls/upload` | 녹음 파일 업로드 |
| `POST` | `/calls/{id}/process` | STT 처리 시작 |
| `GET` | `/calls` | 통화 이력 |
| `GET` | `/calls/{id}` | 통화 상세 |

## 👥 팀

| 이름 | GitHub | 담당 |
|------|--------|------|
| 정성민 | [@seongminj0613-tech](https://github.com/seongminj0613-tech) | Full Stack |

> 팀원 정보 추후 업데이트 예정

## 📚 학습 내용

- 풀스택 개발 (Android + Web + Backend)
- AWS 클라우드 인프라 설계
- Firebase Auth + 카카오 OAuth 연동
- STT(CLOVA) + LLM(Claude) AI 파이프라인
- 갤럭시 통화 녹음 자동 감지 + 백그라운드 업로드

## 📝 라이선스

부트캠프 학습 프로젝트입니다.
