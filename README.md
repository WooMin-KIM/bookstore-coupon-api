# 💳 BeanSolid BookStore - Coupon API 모듈

NHN 아카데미 팀 프로젝트 **BeanSolid(온라인 도서 쇼핑몰)** 서비스의 핵심 도메인인 **Coupon API** 마이크로서비스 모듈입니다.

쿠폰 정책의 생성·관리부터 대규모 사용자를 위한 발급·사용·조회 기능을 전담 개발했으며, **DDD(Domain-Driven Design) 기반 설계**와 **RabbitMQ 비동기 메시징 구조**를 적용하여 대용량 트래픽 환경에서의 확장성과 안정성을 확보하는 데 집중했습니다.

- **전체 프로젝트 기간**: 2025.06.02 ~ 2025.07.25 (약 2개월)
- **전체 팀 규모**: 백엔드 및 프론트엔드 총 7명 (MSA 구조)
- **해당 모듈 작업 인원**: **김우민 (Main Architecture & Core Logic 개발 주도)**, 하정수 (서브 기능 지원)
- **전체 프로젝트 저장소**: [nhnacademy-be10-BeanSolid](https://github.com/nhnacademy-be10-BeanSolid)

---

## 🛠️ 기술 스택 (Tech Stacks)

- **Backend**: Java 21, Spring Boot 3.3.4, Spring Data JPA, Spring Cloud, Spring Security
- **Database & Cache**: MySQL, Redis
- **Message Queue**: RabbitMQ
- **DevOps & Quality**: Docker, GitHub Actions, SonarQube
- **Tools**: Swagger, Lombok

---

## 📌 담당 주요 기능 (Key Features)

- **쿠폰 정책 관리**: 다양한 이벤트(Welcome 쿠폰, 생일 쿠폰, 특정 도서/카테고리 제한 쿠폰)에 대응하는 정책 CRUD 구현
- **고성능 쿠폰 발급/사용**: RabbitMQ를 활용한 비동기 발급 흐름 제어 및 응답 처리
- **주문 연동 할인 계산**: 주문 서비스와의 느슨한 결합을 유지하며 복합적인 할인 금액 및 제한 조건 계산 로직 구현
- **예외 처리 표준화**: 비즈니스 예외 상황에 따른 12개 케이스별 custom 에러 코드 및 사용자 메시지 체계화

---

## 📐 시스템 아키텍처 (System Architecture)

![System Architecture](docs/RabbitMQ.png)

대규모 선착순 이벤트 등 트래픽이 일시에 밀려드는 상황에서도 쿠폰 서비스가 전체 시스템의 병목이 되지 않도록 고가용성(HA) 구조를 설계했습니다.

1. **Gateway 기반 로드 밸런싱**: `Spring Cloud Gateway`를 진입점으로 두어 보안을 강화하고, 내부 `Coupon-API` 인스턴스를 다중화(Port 10352, 10353)하여 부하를 분산했습니다.
2. **RabbitMQ 비동기 큐잉**: 발급 요청을 Message Queue에 안전하게 적재한 후 백그라운드에서 순차적으로 처리함으로써 코어 서버의 자원 고갈을 방지했습니다.

---

## 🗄️ 데이터베이스 설계 (ERD)

**DDD(Domain-Driven Design)** 관점을 반영하여 쿠폰 도메인의 책임을 명확히 분리하고, 타 도메인과의 결합도를 대폭 낮췄습니다.

![BeanSolid BookStore ERD](docs/coupon-erd.png)

- **`coupons`**: 쿠폰 마스터 정책 테이블
- **`user_coupon_list`**: 유저별 쿠폰 상태(`ACTIVE`, `USED`, `EXPIRED`) 및 이력 관리
- **`coupon_books` / `coupon_categories`**: 특정 대상에 매핑되는 확장 테이블 구조 설계

---

## 🚨 Trouble Shooting

### 1. 선착순 쿠폰 발급 요청 집중으로 인한 서버 부하 및 동시성 문제

- **문제 상황**: 이벤트 시작 시점에 발급 요청이 폭주하면 DB Connection Pool 고갈 및 스레드 차단으로 인해 전체 쇼핑몰 시스템이 마비될 리스크 존재.
- **해결 방안**: 
  - `Coupon-API` 인스턴스를 Multi-Port로 스케일 아웃(Scale-out)하여 가용성을 늘렸습니다.
  - 요청을 디스크 DB에 직접 쓰지 않고 `RabbitMQ`를 통해 비동기로 받아 처리 속도를 비약적으로 상승시켰습니다.
- **결과**: 요청이 집중되는 순간에도 안정적인 CPU/메모리 자원 점유율을 유지하며 누락 없는 발급 흐름을 테스트 환경에서 검증 완료.

---

## 📈 코드 품질 관리 (Code Quality)

정적 분석 도구인 **SonarQube**를 인프라에 직접 도입하여 클린 코드 지표를 엄격하게 관리했습니다.

![SonarQube 지표](docs/coupon-API.png)

- **Test Coverage**: **84.1%** 확보 (Service 핵심 로직 및 12개 예외 케이스 완벽 검증)
- **Duplications**: **0.0%** (공통 구조 리팩토링을 통해 중복 코드 제로화 성공)
- **3대 지표 올 A 등급**: Security(`A`), Reliability(`A`), Maintainability(`A`) 달성

---

## 📝 회고 및 학습

단순히 기능 조건에 맞춰 CRUD를 짜는 것을 넘어, **"실제 서비스에 대규모 사용자가 몰리면 어떻게 버텨야 할까?"**에 대한 답을 찾아가는 과정이었습니다. 

RabbitMQ와 Redis 같은 미들웨어를 직접 엮어보고, 인스턴스 분할 환경을 경험하면서 인프라 레벨과 결합된 백엔드 아키텍처 설계 능력을 크게 키울 수 있었던 프로젝트였습니다.
