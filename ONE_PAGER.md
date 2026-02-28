# 🦞 OpenClaw 대안 한눈에 보기

## 비교 테이블

| 프로젝트 | 언어 | 코드 | 핵심 특징 | 대상 |
|---------|------|------|----------|------|
| **OpenClaw** | TS | 435K | 풀스택, 다채널, 스킬 마켓 | 일반 |
| **NanoClaw** | TS | 30K | 컨테이너 격리, 미니멀 | 개발자 |
| **IronClaw** | Rust | 149K | WASM 샌드박스, 자기확장 | 보안 |
| **PicoClaw** | Go | 210 | <10MB RAM, $10 HW | IoT |
| **TinyClaw** | TS | 10K | 멀티에이전트 팀 | 협업 |
| **ZeroClaw** | Rust | 234K | 런타임 OS, 플러그인 | 인프라 |

## 한줄 요약

- **OpenClaw**: 원본. 풍부하지만 복잡하고 보안 우려
- **NanoClaw**: 8분에 읽는 코드. Docker 격리. Claude Code로 셋업
- **IronClaw**: Rust + WASM. 시크릿 절대 노출 안 함
- **PicoClaw**: 10MB RAM, 1초 부팅, $10 하드웨어
- **TinyClaw**: AI 에이전트들이 팀으로 협업
- **ZeroClaw**: 모든 것이 스왑 가능한 런타임

## 나만의 OpenClaw 만들 때

```
┌─────────────────────────────────────────────┐
│ 빠른 시작 → NanoClaw 참고                    │
│ 보안 중시 → IronClaw (WASM) 참고             │
│ 저사양   → PicoClaw (Go) 참고               │
│ 팀 협업  → TinyClaw 참고                    │
│ 인프라   → ZeroClaw 참고                    │
└─────────────────────────────────────────────┘
```

## 핵심 교훈

1. **작게 시작** - PicoClaw처럼 최소한으로
2. **샌드박싱 필수** - Docker, WASM, Apple Container
3. **Rust 추천** - 성능 + 안전성
4. **플러그인 설계** - 모든 것이 스왑 가능하게

---
*레포 위치: ~/workspace/projects/claw-analysis/*
