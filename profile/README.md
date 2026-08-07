<div align="center">

<h1>MGRV Project</h1>

<p>매장 픽업 주문 도메인을 다루는 멀티 클라이언트 · 백엔드 시스템</p>

<p>
  <a href="#repositories">Repositories</a>
  ·
  <a href="#research">Research</a>
  ·
  <a href="#architecture">Architecture</a>
  ·
  <a href="#order-pipeline">Order Pipeline</a>
  ·
  <a href="#scope">Scope</a>
</p>

</div>

---

## What is this, really?

고객용 클라이언트와 매장 운영용 클라이언트가 동일한 주문 파이프라인을 공유한다. 백엔드가 상태 전이와 접근 제어의 단일 진실 공급원(source of truth) 역할을 하고, 두 클라이언트는 같은 도메인 모델과 계약을 공유하되 각자의 화면과 운영 흐름은 독립적으로 갖는다.

## Stuff this system does

- 고객 주문을 접수와 동시에 매장 운영 클라이언트에 실시간으로 반영한다
- 주문 상태는 백엔드가 강제하는 단일 파이프라인을 따른다 — 클라이언트가 상태를 직접 바꾸지 않는다
- 클라이언트 간 공유 계약(shared contracts)으로 도메인 모델 일관성을 유지한다

---

## Platforms

| 클라이언트 | 대상 | 플랫폼 |
|---|---|---|
| Customer | 주문자 | Web/PWA · iOS · Android |
| Owner/Staff POS | 매장 운영 | iPadOS |

## Repositories

| 레포 | 역할 |
|---|---|
| [customer_app](https://github.com/MGRV-Project/customer_app) | 고객용 클라이언트 |
| [owner_pos_app](https://github.com/MGRV-Project/owner_pos_app) | 매장 운영용 POS 클라이언트 |
| [backend](https://github.com/MGRV-Project/backend) | 백엔드 |
| [shared](https://github.com/MGRV-Project/shared) | 클라이언트 간 공유 설계 자산 |
| [docs](https://github.com/MGRV-Project/docs) | API/RPC/이벤트 등 인터페이스 계약 스펙 |
| [readme](https://github.com/MGRV-Project/readme) | 프로젝트 개요 원문 |

---

## Research

시스템을 만들기 전에 "누구를 위한 시스템인가"와 "어디에 놓을 것인가"를 조사한 리서치 레포. 코드가 아니라 의사결정 근거를 담는다.

| 레포 | 역할 |
|---|---|
| [cafe-manager-research-audit](https://github.com/MGRV-Project/cafe-manager-research-audit) | 카페 매니저 직무 리서치 + AX(자동화) 전환 시 자동화되는 업무/남는 업무 매핑 |
| [raw_data](https://github.com/MGRV-Project/raw_data) | 1호점 입지 상권·생활인구·대중교통 데이터 |

---

## Architecture

```mermaid
flowchart LR
    Customer["Customer\nClient"] -->|order request| Backend["Backend\nSource of Truth"]
    Backend -->|state sync| POS["Owner/Staff\nPOS Client"]
    POS -->|status update| Backend
    Backend -->|realtime push| Customer
```

```mermaid
flowchart LR
    subgraph MGRV["MGRV-Project"]
        RM[readme]
        SH[shared]
        DOCS[docs]
        BE[backend]
        CA[customer_app]
        OP[owner_pos_app]
    end

    SH -.->|shared contracts| CA
    SH -.->|shared contracts| OP
    DOCS -.->|API/RPC/event spec| CA
    DOCS -.->|API/RPC/event spec| OP
    BE -->|API surface| CA
    BE -->|API surface| OP
    RM -.-> SH
```

## Order Pipeline

실제 백엔드가 강제하는 주문 상태 전이. 결제 완료(`paid`) 전까지는 시스템이 자동 처리하고, 그 이후(`accepted` 이후 준비·픽업과 취소 예외)는 매장이 직접 판단한다.

```mermaid
stateDiagram-v2
    [*] --> draft
    draft --> payment_pending
    payment_pending --> paid
    payment_pending --> payment_failed
    paid --> accepted: 점주 주문 확인
    paid --> cancelled: 고객 취소(정책별 자동/승인)
    accepted --> preparing
    accepted --> cancelled: 승인 필요 매장은 예외 처리
    preparing --> ready
    ready --> completed
    payment_failed --> [*]
    cancelled --> [*]
    completed --> [*]
```

---

## Scope

| In scope | Out of scope (MVP) |
|---|---|
| 매장 픽업 주문 단일 플로우 | 배달, 락커 연동, 멤버십, 공용시설, 입주민 인증 |

## What it is not

- 배달 플랫폼이 아니다
- 멤버십·구독 시스템이 아니다
- 다점포 운영 도구는 아직 아니다 — 후속 단계

---

<p align="center"><sub>MGRV Project</sub></p>
