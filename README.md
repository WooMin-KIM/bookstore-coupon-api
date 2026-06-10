# BookStore Coupon API

## 프로젝트 소개
NHN 아카데미 팀 프로젝트 **BeanSolid** 온라인 도서 쇼핑몰의 **Coupon API** 모듈입니다.

쿠폰 정책 생성·관리, 사용자 발급·사용·조회 기능을 전담 개발했으며, **DDD 기반 설계**와 **RabbitMQ 비동기 처리 구조**를 적용하여 확장성과 안정성을 강조했습니다.

- **개발 기간**: 2025.06 ~ 2025.07 (2개월)
- **팀 규모**: 7명
- **담당 역할**: Coupon API 전체 설계 및 구현

---

## 기술 스택

**Backend**
- Java 21
- Spring Boot 3.3.4
- Spring Data JPA
- Spring Cloud
- Spring Security

**Database & Cache**
- MySQL
- Redis

**Message Queue**
- RabbitMQ

**Infra & DevOps**
- Docker
- GitHub Actions (CI/CD)
- SonarQube

**기타**
- Swagger, JPA, Lombok

---

## 담당 주요 기능

- **쿠폰 정책 관리** (생성, 수정, 삭제, 조회)
- **사용자 쿠폰 발급·사용·조회** API
- **주문 서비스 연동** 할인 금액 계산 로직
- **예외 처리 체계화** (12개 케이스별 에러 코드 및 사용자 안내 메시지 통일)

---

## 데이터베이스 설계 (ERD)

NHN 아카데미 팀 프로젝트 **BeanSolid**의 데이터베이스 설계입니다.  
DDD를 적용하여 도메인별로 테이블을 분리하고, 특히 **쿠폰과 주문 서비스 연동**에 중점을 두었습니다.

![BeanSolid BookStore 전체 ERD](./docs/images/coupon-erd.png)

**쿠폰 관련 핵심 테이블**
- `coupons` — 쿠폰 정책 관리
- `user_coupon_list` — 사용자 쿠폰 발급 및 사용 이력
- `coupon_books`, `coupon_categories` — 대상 도서/카테고리 연동

## Trouble Shooting

### 1. 쿠폰 발급 요청 집중으로 인한 서버 부하 문제

**문제**  
특정 시간대(이벤트 시즌)에 쿠폰 발급 요청이 폭주하여 시스템 부하 증가 및 응답 지연 발생 우려

**해결**  
- RabbitMQ를 활용한 **메시지 큐 기반 비동기 처리 구조** 설계 및 구현
- 발급 요청을 Queue에 적재 후 백그라운드에서 처리

**결과**  
- 요청 분산 처리로 **시스템 안정성 향상**
- 부하 테스트에서 안정적인 대규모 트래픽 대응 가능 확인

---

## 코드 품질

- **Test Coverage**: 84.1%
- **Duplications**: 0.0%
- **Reliability**: A
- **Security**: A
- **Maintainability**: A

(SonarQube 기준)

---

## 회고 및 학습

이번 쿠폰 서비스 개발을 통해 **단순 기능 구현**을 넘어 **확장성, 유지보수성, 트래픽 대응**을 고려한 설계의 중요성을 깊이 깨달았습니다.

특히 RabbitMQ를 활용한 비동기 처리 경험은 대규모 서비스에서 반드시 필요한 역량임을 실감한 계기가 되었으며, 팀원들과의 코드 리뷰와 협업을 통해 **더 나은 아키텍처**를 함께 만들어가는 경험을 할 수 있었습니다.

---

## 앞으로의 목표
- MSA 환경에서의 **고가용성·분산 처리** 경험 추가
- 더 복잡한 비즈니스 로직과 대용량 트래픽 환경에서의 안정적인 서비스 개발
