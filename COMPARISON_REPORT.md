# 🦞 OpenClaw 대안 분석 리포트

**분석일**: 2026-02-28
**분석 목적**: 나만의 OpenClaw 개발을 위한 대안 분석

---

## 📊 비교 테이블

| 프로젝트 | 언어 | 코드 라인 | 특징 | 보안 | 대상 사용자 |
|---------|------|----------|------|------|------------|
| **OpenClaw** | TypeScript | ~435K | 풀스택, 다채널 | 애플리케이션 레벨 | 일반 사용자 |
| **NanoClaw** | TypeScript | ~30K | 컨테이너 격리, 미니멀 | OS 레벨 샌드박스 | 개발자 |
| **IronClaw** | Rust | ~149K | WASM 샌드박스, 자기확장 | WASM + 크리덴셜 보호 | 보안 중시 |
| **PicoClaw** | Go | ~210 | 초경량, 임베디드 | 기본 | IoT/임베디드 |
| **TinyClaw** | TypeScript | ~10K | 멀티에이전트 팀 | tmux 격리 | 팀/협업 |
| **ClawWork** | - | - | 벤치마크/평가 | - | 연구자 |
| **ZeroClaw** | Rust | ~234K | 런타임 OS, 스왑가능 | 페어링 + 샌드박싱 | 인프라 |

---

## 📦 개별 프로젝트 리포트

### 1. OpenClaw (원본)
**GitHub**: https://github.com/openclaw/openclaw

**개요**: 원본 프로젝트. 풀스택 개인 AI 어시스턴트.

**장점**:
- 200K+ GitHub 스타
- 다채널 지원 (WhatsApp, Telegram, Slack, Discord, Signal, iMessage 등)
- 풍부한 생태계 (스킬 마켓플레이스)
- Canvas 렌더링
- 음성 입출력

**단점**:
- 코드베이스 너무 큼 (435K 라인)
- 보안 우려 (애플리케이션 레벨 격리)
- 53개 설정 파일, 70+ 의존성
- 하나의 Node 프로세스에서 공유 메모리

**기술 스택**: Node.js, TypeScript

---

### 2. NanoClaw
**GitHub**: https://github.com/qwibitai/nanoclaw

**개요**: OpenClaw의 미니멀 버전. 8분 안에 코드 전체를 읽을 수 있음.

**철학**: "작아서 이해할 수 있다"

**장점**:
- 컨테이너 격리 (Apple Container / Docker)
- 코드베이스가 작음 (~30K 라인)
- Claude Code로 셋업 가능
- Agent Swarms 지원 (최초)
- 포크해서 커스터마이즈하는 방식

**단점**:
- 기능이 OpenClaw보다 적음
- macOS/Linux만 지원

**기술 스택**: TypeScript, Claude Agent SDK

**특이점**: 설정 파일 없음. 동작 변경 = 코드 수정.

---

### 3. IronClaw
**GitHub**: https://github.com/nearai/ironclaw

**개요**: Rust로 작성된 보안 중심 AI 어시스턴트.

**철학**: "당신의 AI 어시스턴트는 당신을 위해 일해야 한다"

**장점**:
- WASM 샌드박스 (신뢰할 수 없는 도구 격리)
- 크리덴셜 보호 (시크릿이 도구에 노출 안 됨)
- 프롬프트 인젝션 방어
- 동적 도구 생성 (WASM)
- MCP 프로토콜 지원
- 하이브리드 검색 (전문 + 벡터)

**단점**:
- PostgreSQL + pgvector 필요
- NEAR AI 계정 필요
- 설치 복잡

**기술 스택**: Rust, PostgreSQL, WASM

---

### 4. PicoClaw
**GitHub**: https://github.com/sipeed/picoclaw

**개요**: $10 하드웨어에서 10MB 미만 RAM으로 실행되는 초경량 AI 어시스턴트.

**철학**: "모든 사람이 Mac Mini를 AI 어시스턴트에 바칠 필요는 없다"

**장점**:
- 초경량 (<10MB RAM)
- 초저가 ($10 하드웨어)
- 초고속 (1초 부팅)
- 단일 바이너리 (RISC-V, ARM, x86)
- AI가 95% 코드 생성

**단점**:
- 기능 제한적
- 초기 개발 단계
- 보안 이슈 미해결

**기술 스택**: Go

**비교**:
| | OpenClaw | NanoBot | PicoClaw |
|---|---------|---------|----------|
| RAM | >1GB | >100MB | <10MB |
| 시작 | >500s | >30s | <1s |

---

### 5. TinyClaw
**GitHub**: https://github.com/jlia0/tinyclaw

**개요**: 멀티에이전트, 멀티팀, 멀티채널 24/7 AI 어시스턴트.

**철학**: "400줄 쉘 스크립트. Claude Code + tmux"

**장점**:
- 멀티에이전트 (여러 AI 에이전트 동시 실행)
- 팀 협업 (에이전트끼리 핸드오프)
- TinyOffice (웹 대시보드)
- 팀 시각화 (에이전트 대화 관찰)
- SQLite 큐 (원자적 트랜잭션)

**단점**:
- tmux, jq, Bash 4.0+ 필요
- Claude Code CLI 또는 Codex CLI 필요

**기술 스택**: TypeScript, Shell, tmux

---

### 6. ClawWork
**GitHub**: https://github.com/HKUDS/ClawWork

**개요**: AI 에이전트 벤치마크 프레임워크.

**목적**: 에이전트가 "할 수 있는 것"이 아닌 "벌 수 있는 것" 측정.

**특징**:
- 44개 산업 분야 실제 전문 작업
- 최고 에이전트가 7시간에 $10K 상당 수익

**용도**: 연구/평가용 (실제 사용 X)

---

### 7. ZeroClaw
**GitHub**: https://github.com/zeroclaw-labs/zeroclaw

**개요**: 에이전트 워크플로우를 위한 런타임 운영체제.

**철학**: "모델, 도구, 메모리, 실행을 추상화하여 에이전트를 한 번 빌드하고 어디서든 실행"

**장점**:
- Trait 기반 아키텍처
- 보안 기본값 런타임
- 프로바이더/채널/도구 스왑 가능
- 플러그인 가능한 모든 것
- 린 런타임 (릴리즈 빌드에서 몇 MB)
- Research Phase (응답 생성 전 정보 수집)

**단점**:
- 아직 초기 단계
- 사기 주의 (zeroclaw.org, zeroclaw.net은 공식 아님)

**기술 스택**: Rust

**커뮤니티**: Harvard, MIT, Sundai.Club

---

## 🎯 나만의 OpenClaw 개발을 위한 추천

### 목표별 추천

| 목표 | 추천 프로젝트 | 이유 |
|-----|-------------|------|
| **빠른 시작** | NanoClaw | 코드 작고 이해하기 쉬움 |
| **보안 중시** | IronClaw | WASM 샌드박스 + 크리덴셜 보호 |
| **저사양** | PicoClaw | 10MB RAM, $10 하드웨어 |
| **팀 협업** | TinyClaw | 멀티에이전트 팀 |
| **인프라** | ZeroClaw | 런타임 OS, 스왑 가능 |

### 핵심 인사이트

1. **보안이 최우선**: OpenClaw의 가장 큰 문제는 보안. IronClaw/NanoClaw 방식 참고.

2. **작게 시작**: PicoClaw처럼 최소한으로 시작해서 확장하는 게 좋음.

3. **언어 선택**:
   - **Rust**: 성능 + 안전성 (IronClaw, ZeroClaw)
   - **Go**: 단순함 + 이식성 (PicoClaw)
   - **TypeScript**: 빠른 개발 (OpenClaw, NanoClaw, TinyClaw)

4. **샌드박싱 필수**: 컨테이너(Docker), WASM, 또는 Apple Container 사용.

5. **플러그인 아키텍처**: ZeroClaw처럼 모든 것이 스왑 가능하도록 설계.

---

## 📁 분석된 레포 위치

```
~/workspace/projects/claw-analysis/
├── openclaw/    # 원본
├── nanoclaw/    # 컨테이너 격리
├── ironclaw/    # Rust + WASM
├── picoclaw/    # Go 초경량
├── tinyclaw/    # 멀티에이전트
├── ClawWork/    # 벤치마크
└── zeroclaw/    # 런타임 OS
```

---

*분석 완료: 2026-02-28*
