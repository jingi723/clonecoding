# Novu 클론 코딩 학습 플로우

목표: Novu를 이해하고, 알림 시스템을 직접 만들 수 있다.

---

## 전체 로드맵

```
1단계. 기초 (JS/TS)
    ↓
2단계. 백엔드 기초 (Node.js + NestJS)
    ↓
3단계. Novu 코드 읽기
    ↓
4단계. 직접 만들기
```

---

## 1단계. 기초 다지기

### JavaScript 핵심만
> Novu는 TypeScript지만 JS를 먼저 이해해야 한다.

- [ ] 변수, 함수, 조건문, 반복문
- [ ] 비동기 처리 (`async` / `await`, `Promise`)
- [ ] 배열/객체 다루기 (`map`, `filter`, `find`)
- [ ] 모듈 시스템 (`import` / `export`)

### TypeScript 추가 개념
> JS에 타입만 추가된 것이다. 겁먹을 필요 없다.

- [ ] 타입 선언 (`string`, `number`, `boolean`)
- [ ] 인터페이스 / 타입 (`interface`, `type`)
- [ ] 제네릭 (`Array<string>`, `Promise<void>`)
- [ ] 데코레이터 (`@Injectable`, `@Controller`) — NestJS에서 많이 씀

---

## 2단계. 백엔드 기초

### Node.js
- [ ] HTTP 서버가 뭔지 (`req` / `res` 개념)
- [ ] REST API 개념 (GET, POST, DELETE)
- [ ] 환경변수 (`.env`, `process.env`)

### NestJS (Novu 백엔드 프레임워크)
> Spring Boot의 Node.js 버전이라고 생각하면 된다.

- [ ] Module — 기능 묶음
- [ ] Controller — API 입구 (URL 처리)
- [ ] Service / Usecase — 실제 비즈니스 로직
- [ ] 의존성 주입 (DI) — `constructor(private xxx: XxxService)`

### 큐 (Bull Queue)
> 알림 발송을 비동기로 처리하는 핵심 개념

- [ ] 큐가 왜 필요한가 (트리거와 발송 분리)
- [ ] Bull Queue 기본 사용법 (`add`, `process`)

---

## 3단계. Novu 코드 읽기

### 읽는 순서

```
① apps/api/src/main.ts
   → 서버 진입점. 여기서 시작한다.

② apps/api/src/app.module.ts
   → 어떤 기능이 있는지 목차 확인.

③ apps/api/src/app/events/events.controller.ts
   → POST /events/trigger API 입구.

④ apps/api/src/app/events/usecases/parse-event-request/
   → 트리거 요청 처리 로직.
   → workflowQueueService.add() 로 큐에 넣는다.

⑤ apps/worker/src/app/workflow/usecases/run-job/run-job.usecase.ts
   → 큐에서 꺼내서 실행.
   → sendMessage.execute() 로 실제 발송.

⑥ apps/worker/src/app/workflow/usecases/send-message/
   → 채널(이메일/SMS/인앱)별 분기 처리.

⑦ packages/providers/src/
   → 이메일이면 Nodemailer, SMS면 Twilio 등 실제 발송.
```

### 전체 흐름 요약

```
클라이언트
    ↓ POST /events/trigger
EventsController          [apps/api]
    ↓
ParseEventRequest         [apps/api - usecase]
    ↓ 큐에 추가
Bull Queue
    ↓ (비동기)
RunJob                    [apps/worker - usecase]
    ↓
SendMessage               [apps/worker - usecase]
    ↓ 채널 분기
Provider                  [packages/providers]
    ↓
수신자에게 알림 도착
```

---

## 4단계. 직접 만들기

> 이해한 것을 백지에서 직접 구현한다. 코드를 보지 않고 만드는 것이 목표.

### 구현 순서

**Sprint 1 — 뼈대 세팅**
- [ ] pnpm 모노레포 초기화
- [ ] NestJS API 서버 세팅
- [ ] `.env` 환경변수 구성

**Sprint 2 — 트리거 API**
- [ ] `POST /events/trigger` API 만들기
- [ ] MongoDB 연결 + Subscriber / Notification 스키마 정의
- [ ] Bull Queue 연결

**Sprint 3 — Worker**
- [ ] Worker 앱 분리
- [ ] 큐에서 꺼내서 Job 실행하는 로직 구현

**Sprint 4 — 이메일 발송**
- [ ] Nodemailer로 이메일 Provider 구현
- [ ] 실제 이메일 발송 테스트

**Sprint 5 — 대시보드 (선택)**
- [ ] React로 트리거 현황 확인 화면 만들기

---

## 참고

- Novu 원본 코드: `/home/jin/clonecoding/novu/`
- 내가 만드는 코드: `/home/jin/clonecoding/my-novu/` (Sprint 1에서 생성)
- 막히면 원본 열어서 비교하기
