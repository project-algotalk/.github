<div align="center">

# 🎙️ AlgoTalk

**AI 기반 CS 기술 면접 시뮬레이션 플랫폼**

실전처럼 말하고, 데이터로 피드백 받는 모의 기술 면접

[🌐 algotalk.kr](https://algotalk.kr)

</div>

---

## 📌 AlgoTalk이란?

AlgoTalk은 개발자 취업 준비생을 위한 **AI 모의 기술 면접 서비스**입니다.

CS 기술 질문에 **음성으로 답변**하면, AI가 답변 내용을 분석해 피드백을 제공하고,
면접 중 **웹캠 영상으로 시선·자세·제스처**까지 함께 분석하여 실전 면접에 필요한
비언어적 요소까지 종합적으로 점검할 수 있습니다.

---

## ✨ 주요 기능

- 🗣️ **답변 내용 분석** — STT로 음성 답변을 텍스트로 변환하고, LLM이 기술적 정확성·논리 구조를 분석해 피드백 제공
- 👁️ **시선·자세 분석** — 브라우저에서 MediaPipe 기반으로 시선 처리, 자세 기울임, 얼굴 터치 등 비언어적 습관 측정
- 📊 **종합 피드백 리포트** — 면접 종료 후 답변 점수와 행동 지표를 통합한 리포트와 대시보드 제공
- 💬 **커뮤니티** — 면접 질문 공유, 정보/팁, 후기 게시판으로 함께 준비
- 🔐 **간편 로그인** — Kakao 소셜 로그인(OAuth2) 및 JWT 기반 인증

---

## 🏗️ 시스템 아키텍처

![AlgoTalk System Architecture](./assets/architecture.png)

> 프론트엔드(React)와 AI 서비스(FastAPI)는 **Oracle Cloud A1**, Spring Boot MSA 백엔드는 **AWS EC2**에 배포되어 있으며, 도메인(`algotalk.kr` / `api.algotalk.kr`) 기준으로 트래픽이 분리됩니다.

---

## 📦 Repositories

| Repository | 설명 | 기술 스택 |
|------------|------|----------|
| [**AlgoTalk_BE**](https://github.com/AlgoTalk/AlgoTalk_BE) | Spring Boot MSA 백엔드 (Gateway · Eureka · User · Community · Interview) | Spring Boot 3.5, Spring Cloud, MyBatis |
| [**AlgoTalk_FE**](https://github.com/AlgoTalk/AlgoTalk_FE) | React 프론트엔드 + 브라우저 실시간 영상 분석 | React, Zustand, MediaPipe |
| [**AlgoTalk_AI**](https://github.com/AlgoTalk/AlgoTalk_AI) | AI 분석 서비스 (STT · 답변 분석) | FastAPI, OpenAI API |
| [**AlgoTalk_Config**](https://github.com/AlgoTalk/AlgoTalk_Config) | Spring Cloud Config Server (중앙 설정 관리) | Spring Cloud Config |

---

## 🛠 기술 스택

| 분야 | 기술 |
|------|------|
| **Backend** | Java 17, Spring Boot 3.5, Spring Cloud (Gateway · Eureka · Config), MyBatis |
| **Frontend** | React, Zustand, MediaPipe (Face/Pose Landmarker) |
| **AI** | Python 3.10, FastAPI, OpenAI API, STT |
| **Database** | MariaDB (서비스별 분리), Redis, MongoDB |
| **Infra** | AWS EC2, Oracle Cloud A1 (ARM64), Docker, Nginx |
| **CI/CD** | GitHub Actions (QEMU 멀티 아키텍처 빌드) |
| **인증** | JWT (AT/RT + RTR), OAuth2 (Kakao), HttpOnly Cookie |

---

## 🔍 기술적 특징

### 🔐 MSA + Spring Cloud Gateway 인증
WebFlux 기반 Gateway에서 `GlobalFilter`로 JWT를 검증하고, AT 만료 시 RT 쿠키 기반 **자동 토큰 재발급**(`AutoRefreshOn401Filter`)을 Gateway 레벨에서 처리하여 프론트엔드의 인증 복잡도를 최소화

### 🎥 브라우저 실시간 영상 분석
프레임별 랜드마크 연산을 브라우저(MediaPipe Face/Pose Landmarker)에서 수행 — **영상을 서버에 업로드하지 않고** 시선·자세·제스처를 실시간 분석하여 개인정보 부담과 서버 부하를 동시에 해소

### 👁️ iris 좌표 기반 시선 판정
홍채(iris) 중심 좌표를 eye-contour 기준점으로 정규화하여 정면 응시 여부를 판정 — 해상도·화면 내 얼굴 크기 변화에 영향받지 않는 scale-invariant 설계

### ⚙️ 멀티 클라우드 + 멀티 아키텍처 CI/CD
AWS EC2(amd64)와 Oracle Cloud A1(ARM64) 환경에 맞춰 GitHub Actions + QEMU 기반 크로스 빌드 파이프라인 구성, MSA 서비스별 `paths` 필터로 변경된 서비스만 선택 빌드

---

## 🔄 개발 워크플로우

1인 개발 프로젝트지만, **Issue·PR 기반의 실무 협업 프로세스를 그대로 적용**하여 개발했습니다.

```
Issue 등록 → develop에서 feature 브랜치 분기 → 개발 & 커밋 → PR (→ develop) → 리뷰 → Merge → Issue 자동 종료
```

### 브랜치 전략

| 브랜치 | 용도 |
|--------|------|
| `main` | 배포 가능한 안정 버전 |
| `develop` | 개발 통합 브랜치 — 모든 feature 브랜치의 분기/머지 대상 |
| `feature/{이슈번호}-{기능명}` | 기능 단위 개발 (예: `feature/2-userinfo-api`) |

- feature 브랜치는 **반드시 develop에서만 분기**
- Merge 완료 후 feature 브랜치는 로컬·원격 모두 삭제하여 정리

### Issue 관리

- 모든 작업은 **하나의 Issue = 하나의 작업/기능/버그** 단위로 등록
- Issue 본문에 **진행 상황 체크리스트**를 포함해 작업 단계를 추적

```markdown
## 설명
Spring Boot 기반으로 회원가입 API 개발

## 진행 상황
- [ ] Controller 생성
- [ ] Service 로직 구현
- [ ] 테스트 코드 작성
```

### 커밋 & PR 컨벤션

```
feat: Add login API

사용자 로그인 API 추가.
비밀번호 암호화 적용.

closed #2
```

- 커밋 타입: `feat` / `fix` / `refactor` / `docs` / `chore`
- 커밋 본문 또는 PR에 `closed #이슈번호`를 명시하여 **머지 시 Issue 자동 종료**
- PR 본문에 작업 설명과 관련 이슈를 기록하여 변경 이력 추적

---

## 🚀 배포 환경

| 컴포넌트 | 플랫폼 | 도메인 |
|---------|--------|--------|
| Frontend (React) | Oracle Cloud A1 + Nginx | `algotalk.kr` |
| AI Service (FastAPI) | Oracle Cloud A1 + Docker | `algotalk.kr/ai` |
| Backend MSA (Spring Boot) | AWS EC2 + Docker | `api.algotalk.kr` |
| CI/CD | GitHub Actions | push 시 자동 빌드·배포 |

---

<div align="center">

🎓 졸업 프로젝트 · 1인 풀스택 개발

Developed by [@AnJungHoon](https://github.com/AnJungHoon)

</div>
