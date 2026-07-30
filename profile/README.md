# MGRV Project

## 구성 레포

| 레포 | 내용 |
|---|---|
| [customer_app](https://github.com/MGRV-Project/customer_app) | 고객용 앱 |
| [owner_pos_app](https://github.com/MGRV-Project/owner_pos_app) | 점주·직원용 POS 앱 |
| [backend](https://github.com/MGRV-Project/backend) | 백엔드 |
| [shared](https://github.com/MGRV-Project/shared) | 공통 설계 |
| [readme](https://github.com/MGRV-Project/readme) | 프로젝트 개요 |

## 1. 시스템 구성

```mermaid
flowchart LR
    Customer["고객 앱"] --> Backend["Backend"]
    Backend --> POS["점주·직원 앱"]
```

## 2. 레포 구조

```mermaid
flowchart LR
    subgraph MGRV["MGRV-Project org"]
        RM[readme]
        SH[shared]
        BE[backend]
        CA[customer_app]
        OP[owner_pos_app]
    end

    SH -.-> CA
    SH -.-> OP
    BE --> CA
    BE --> OP
    RM -.-> SH
```

## 3. 주문 흐름

```mermaid
stateDiagram-v2
    [*] --> 접수대기
    접수대기 --> 접수완료
    접수완료 --> 준비중
    준비중 --> 준비완료
    준비완료 --> 전달완료
    전달완료 --> [*]
    접수완료 --> 취소
    취소 --> [*]
```
