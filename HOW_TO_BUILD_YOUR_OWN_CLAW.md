# 🦞 나만의 OpenClaw 만들기 가이드

> **목표**: 기존 OpenClaw의 장점을 취하고 단점을 보완한 개인 AI 어시스턴트 개발

---

## 📋 목차

1. [왜 새로 만드는가?](#1-왜-새로-만드는가)
2. [아키텍처 설계 원칙](#2-아키텍처-설계-원칙)
3. [기술 스택 선택](#3-기술-스택-선택)
4. [핵심 컴포넌트](#4-핵심-컴포넌트)
5. [보안 설계](#5-보안-설계)
6. [개발 로드맵](#6-개발-로드맵)
7. [참고할 프로젝트](#7-참고할-프로젝트)

---

## 1. 왜 새로 만드는가?

### OpenClaw의 문제점

| 문제 | 설명 |
|-----|------|
| **코드 복잡성** | 435K 라인, 53개 설정 파일, 70+ 의존성 |
| **보안 우려** | 애플리케이션 레벨 격리만 제공, 시크릿 노출 위험 |
| **리소스** | 1GB+ RAM 필요, 무거운 Node.js 프로세스 |
| **커스터마이징** | 복잡한 구조로 수정 어려움 |

### 우리가 원하는 것

```
✅ 작고 이해하기 쉬운 코드
✅ OS 레벨 보안 격리
✅ 가벼운 리소스 사용
✅ 쉬운 커스터마이징
✅ 플러그인 아키텍처
```

---

## 2. 아키텍처 설계 원칙

### 2.1 미니멀리즘 (NanoClaw 철학)

```
"8분 안에 전체 코드를 읽을 수 있어야 한다"
```

- 하나의 프로세스
- 최소한의 파일
- 마이크로서비스 없음
- 설정 파일 최소화 (동작 변경 = 코드 수정)

### 2.2 샌드박싱 (IronClaw 철학)

```
"AI는 절대 당신의 시크릿에 직접 접근하면 안 된다"
```

- 모든 도구는 샌드박스 내에서 실행
- 크리덴셜은 호스트 경계에서 주입
- 허용 목록 기반 HTTP 요청

### 2.3 스왑 가능한 모든 것 (ZeroClaw 철학)

```
"한 번 빌드하고, 어디서든 실행"
```

- 프로바이더 스왑 (OpenAI ↔ Anthropic ↔ 로컬 모델)
- 채널 스왑 (Telegram ↔ Discord ↔ Slack)
- 도구 스왑 (플러그인 아키텍처)

---

## 3. 기술 스택 선택

### 3.1 언어 선택

| 언어 | 장점 | 단점 | 추천 상황 |
|-----|------|------|----------|
| **Rust** | 성능, 안전성, WASM 지원 | 러닝커브 | 프로덕션, 보안 중시 |
| **Go** | 단순함, 빠른 빌드, 단일 바이너리 | GC | 빠른 개발, 임베디드 |
| **TypeScript** | 생태계, 빠른 프로토타이핑 | 런타임 필요 | MVP, 웹 통합 |

### 3.2 추천 스택

```
┌─────────────────────────────────────────────────────────┐
│                    권장 스택 (v1)                        │
├─────────────────────────────────────────────────────────┤
│  언어: Rust (또는 Go for MVP)                           │
│  샌드박스: Docker / Apple Container / WASM              │
│  DB: SQLite (경량) / PostgreSQL+pgvector (풀스택)       │
│  메시징: 직접 API (Telegram Bot API 등)                 │
│  LLM: Anthropic Claude API (또는 OpenAI)               │
└─────────────────────────────────────────────────────────┘
```

---

## 4. 핵심 컴포넌트

### 4.1 최소 기능 세트 (MVP)

```
┌─────────────────────────────────────────┐
│              Core Engine                │
├─────────────────────────────────────────┤
│  1. Message Handler (입력 처리)          │
│  2. LLM Client (AI 호출)                │
│  3. Tool Executor (도구 실행)            │
│  4. Response Formatter (출력 포맷)       │
└─────────────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────┐
│              Channels                   │
├─────────────────────────────────────────┤
│  - Telegram Bot                         │
│  - CLI (로컬 테스트용)                   │
└─────────────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────┐
│              Sandbox                    │
├─────────────────────────────────────────┤
│  - Docker Container                     │
│  - 파일시스템 격리                       │
│  - 네트워크 격리                         │
└─────────────────────────────────────────┘
```

### 4.2 모듈 구조

```
my-claw/
├── src/
│   ├── main.rs              # 엔트리포인트
│   ├── core/
│   │   ├── engine.rs        # 핵심 로직
│   │   ├── llm.rs           # LLM 클라이언트
│   │   └── tools.rs         # 도구 실행기
│   ├── channels/
│   │   ├── telegram.rs      # Telegram 봇
│   │   └── cli.rs           # CLI
│   ├── sandbox/
│   │   ├── docker.rs        # Docker 샌드박스
│   │   └── wasm.rs          # WASM 샌드박스 (선택)
│   └── config.rs            # 설정
├── tools/                   # 플러그인 도구들
├── Cargo.toml
└── README.md
```

---

## 5. 보안 설계

### 5.1 계층별 보안

```
┌─────────────────────────────────────────────────────────┐
│ Layer 1: 인증 (Authentication)                          │
│   - API 토큰 검증                                        │
│   - 페어링 코드 (NanoClaw 방식)                          │
├─────────────────────────────────────────────────────────┤
│ Layer 2: 격리 (Isolation)                               │
│   - 컨테이너 샌드박스 (Docker/WASM)                      │
│   - 파일시스템 마운트 제한                               │
│   - 네트워크 allowlist                                  │
├─────────────────────────────────────────────────────────┤
│ Layer 3: 시크릿 보호 (Credential Protection)            │
│   - 환경변수 주입 (호스트 경계에서)                       │
│   - LLM에 시크릿 노출 금지                               │
│   - 누출 감지                                           │
├─────────────────────────────────────────────────────────┤
│ Layer 4: 프롬프트 보안 (Prompt Security)                │
│   - 인젝션 패턴 감지                                     │
│   - 콘텐츠 새니타이제이션                                │
│   - 정책 강제                                           │
└─────────────────────────────────────────────────────────┘
```

### 5.2 시크릿 처리 (IronClaw 방식)

```rust
// ❌ 잘못된 방식: LLM에 시크릿 전달
let prompt = format!("API_KEY={} 를 사용해서...", secret);

// ✅ 올바른 방식: 호스트에서 주입
fn execute_tool(tool: &Tool, context: &Context) -> Result<Output> {
    let mut env = HashMap::new();
    
    // 호스트 경계에서 시크릿 주입
    if tool.requires_credential("API_KEY") {
        env.insert("API_KEY", get_secret("API_KEY")?);
    }
    
    // 샌드박스 내에서 실행 (시크릿은 환경변수로만 접근)
    sandbox.execute(tool.command, env)
}
```

---

## 6. 개발 로드맵

### Phase 1: MVP (2주)

```
Week 1:
├── [ ] 프로젝트 구조 설정
├── [ ] LLM 클라이언트 구현 (Claude API)
├── [ ] 기본 메시지 핸들링
└── [ ] CLI 채널 구현

Week 2:
├── [ ] Telegram 봇 연동
├── [ ] Docker 샌드박스 기본 구현
├── [ ] 기본 도구 (bash, 파일 읽기)
└── [ ] 테스트 및 문서화
```

### Phase 2: 보안 강화 (2주)

```
Week 3:
├── [ ] 시크릿 관리 시스템
├── [ ] 네트워크 allowlist
├── [ ] 프롬프트 인젝션 방어
└── [ ] 감사 로깅

Week 4:
├── [ ] WASM 샌드박스 (선택)
├── [ ] 페어링 시스템
├── [ ] 보안 테스트
└── [ ] 문서화
```

### Phase 3: 기능 확장 (2주)

```
Week 5:
├── [ ] 플러그인 시스템
├── [ ] 추가 채널 (Discord, Slack)
├── [ ] 메모리/컨텍스트 관리
└── [ ] 스케줄링 (cron)

Week 6:
├── [ ] 웹 대시보드 (선택)
├── [ ] 멀티 에이전트 (선택)
├── [ ] 성능 최적화
└── [ ] 프로덕션 배포
```

---

## 7. 참고할 프로젝트

### 코드 참고

| 프로젝트 | 참고할 부분 | GitHub |
|---------|-----------|--------|
| **NanoClaw** | 전체 구조, 컨테이너 격리 | [qwibitai/nanoclaw](https://github.com/qwibitai/nanoclaw) |
| **IronClaw** | WASM 샌드박스, 시크릿 보호 | [nearai/ironclaw](https://github.com/nearai/ironclaw) |
| **PicoClaw** | 경량화, Go 구현 | [sipeed/picoclaw](https://github.com/sipeed/picoclaw) |
| **TinyClaw** | 멀티에이전트, 팀 협업 | [jlia0/tinyclaw](https://github.com/jlia0/tinyclaw) |
| **ZeroClaw** | 플러그인 아키텍처, Rust | [zeroclaw-labs/zeroclaw](https://github.com/zeroclaw-labs/zeroclaw) |

### 분석 레포

모든 프로젝트 클론됨: `~/workspace/projects/claw-analysis/`

---

## 🚀 시작하기

### Step 1: 레포 클론

```bash
git clone https://github.com/smarthug/claw-analysis.git
cd claw-analysis
```

### Step 2: 참고 프로젝트 분석

```bash
# NanoClaw 구조 확인 (가장 간단)
ls nanoclaw/src/

# IronClaw 보안 코드 확인
cat ironclaw/src/sandbox/
```

### Step 3: 새 프로젝트 시작

```bash
# Rust 프로젝트 생성
cargo new my-claw
cd my-claw

# 또는 Go 프로젝트
mkdir my-claw && cd my-claw
go mod init my-claw
```

---

## 💡 핵심 인사이트

1. **작게 시작하라** - MVP는 500줄 이하로 가능
2. **보안은 처음부터** - 나중에 추가하기 어려움
3. **샌드박스 필수** - Docker가 가장 쉬움
4. **플러그인 설계** - 처음부터 확장성 고려
5. **테스트 작성** - 특히 보안 관련

---

## 📞 팀 논의 포인트

- [ ] 언어 선택: Rust vs Go vs TypeScript?
- [ ] 샌드박스: Docker vs WASM vs 둘 다?
- [ ] 첫 번째 채널: Telegram vs Discord?
- [ ] MVP 스코프: 어디까지?
- [ ] 타임라인: 몇 주?

---

*작성일: 2026-02-28*
*분석 레포: https://github.com/smarthug/claw-analysis*
