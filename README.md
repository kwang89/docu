# 동시성 제어 정보 블로그
- https://velog.io/@c1madang/Concurrency-Control
  - 동시성 제어의 정의/목적/방법
  - Lock의 종료
  - Lock별로 장단점

# 동시성 문제 해결 전략
- https://brewagebear.github.io/concurrency-distributed-transaction-with-tcc/
  - TCC 패턴
 
# 최종 일관성
- https://www.msaschool.io/operation/integration/integration-four/
  - TCC 패턴보다 구현이 쉽다고 이야기함

# Saga Pattern의 트랜잭션 정리
- https://velog.io/@daehoon12/%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4-%ED%8C%A8%ED%84%B4-4.-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EA%B4%80%EB%A6%AC-%EC%82%AC%EA%B0%80#432-%EB%B9%84%EA%B2%A9%EB%A6%AC-%EB%8C%80%EC%B1%85


안녕하세요! 상품 관리에 대한 데이터베이스 설계를 요청하셨는데요, 비즈니스 유연성과 성능을 고려하여 다음과 같은 데이터 모델을 제안드립니다. 

## 데이터베이스 설계

### 1. **엔티티 개요**

- **Product (상품)**
  - `ProductID`: 고유 식별자 (Primary Key, UUID 또는 Auto-Increment Integer)
  - `ProductName`: 상품 이름
  - `ProductDescription`: 상품 설명
  - `Price`: 상품 가격
  - `StockQuantity`: 재고 수량
  - `CategoryID`: 상품 카테고리 (Foreign Key, `Category` 테이블 참조)
  - `CreatedAt`: 생성일시
  - `UpdatedAt`: 수정일시
  - **비즈니스 유연성:** 이 구조는 기본적인 상품 정보와 관련된 다양한 기능을 유연하게 관리할 수 있게 해줍니다. 추가 필드가 필요하면 쉽게 확장할 수 있습니다.

- **Category (카테고리)**
  - `CategoryID`: 고유 식별자 (Primary Key, UUID 또는 Auto-Increment Integer)
  - `CategoryName`: 카테고리 이름
  - `ParentCategoryID`: 부모 카테고리 (Self-referencing Foreign Key, `Category` 테이블 참조)
  - **비즈니스 유연성:** 카테고리 구조를 계층적으로 관리할 수 있어, 상품 분류를 다양하게 관리할 수 있습니다. 

- **ProductAttribute (상품 속성)**
  - `AttributeID`: 고유 식별자 (Primary Key, UUID 또는 Auto-Increment Integer)
  - `ProductID`: 상품 ID (Foreign Key, `Product` 테이블 참조)
  - `AttributeName`: 속성 이름 (예: 색상, 사이즈)
  - `AttributeValue`: 속성 값 (예: 빨강, XL)
  - **비즈니스 유연성:** 이 구조는 상품의 특성(속성)을 동적으로 관리할 수 있게 해줍니다. 예를 들어, 특정 상품에 색상, 크기 등의 속성을 추가할 수 있습니다.

- **ProductImage (상품 이미지)**
  - `ImageID`: 고유 식별자 (Primary Key, UUID 또는 Auto-Increment Integer)
  - `ProductID`: 상품 ID (Foreign Key, `Product` 테이블 참조)
  - `ImageURL`: 이미지 경로
  - `AltText`: 대체 텍스트
  - **비즈니스 유연성:** 각 상품에 대해 여러 이미지를 관리할 수 있습니다. 이미지가 변경되거나 추가되는 상황에 유연하게 대응할 수 있습니다.

### 2. **성능 고려 사항**

- **인덱싱:** 
  - `ProductID`, `CategoryID`, `AttributeID`와 같은 주요 키 필드에 인덱스를 생성하여 검색 성능을 향상시킵니다.
  - 자주 사용되는 검색 조건(예: `Price`나 `CategoryID`)에 대해 추가 인덱스를 설정합니다.

- **파티셔닝 (Partitioning):**
  - 대규모 데이터를 처리할 경우, `CreatedAt`을 기준으로 테이블 파티셔닝을 고려할 수 있습니다. 이렇게 하면 최신 데이터를 빠르게 조회할 수 있습니다.

- **캐싱 (Caching):**
  - 조회 빈도가 높은 상품 정보는 캐시 시스템을 활용하여 응답 시간을 줄입니다.

### 3. **유연성 고려 사항**

- **확장성:** 
  - 새로운 속성이나 카테고리 추가 시 기존 구조를 변경하지 않고도 확장할 수 있도록 설계하였습니다. 
  - 예를 들어, 상품 속성은 속성 테이블을 통해 동적으로 관리되므로 다양한 형태의 상품을 다룰 수 있습니다.

- **유지보수 용이성:**
  - 테이블을 명확하게 분리하여 각 기능이 독립적으로 유지보수되도록 설계했습니다. 예를 들어, 카테고리 구조 변경 시 다른 테이블에 영향을 주지 않고도 변경이 가능합니다.

### 4. **ERD (Entity-Relationship Diagram)**
- ERD는 위 설명한 엔티티들과 관계를 시각화한 다이어그램입니다. 관계형 데이터베이스 관리 시스템(RDBMS)을 이용해 설계 시 이 ERD를 기반으로 구현할 수 있습니다.

---

이 데이터 모델은 다양한 비즈니스 요구사항에 유연하게 대응할 수 있으며, 데이터베이스의 성능을 최적화하는 방향으로 설계되었습니다. 질문이나 추가 요구사항이 있으면 언제든지 말씀해주세요!
