# F-folder-scrapy: 분산 크롤링 및 데이터 처리 시스템

## 1. 프로젝트 개요

### Problem
다양한 웹 소스에서 패션 및 라이프스타일 콘텐츠를 효율적으로 수집하고, 수집된 데이터를 정제하여 서비스에 활용하는 과정에서 다음과 같은 문제에 직면했습니다.
*   **비효율적인 데이터 수집**: 각 웹사이트의 상이한 구조로 인해 크롤링 로직의 재사용성이 낮고, 새로운 소스 추가 시 개발 공수가 컸습니다.
*   **데이터 정제의 복잡성**: 수집된 원시 데이터는 불필요한 정보가 많고, 일관되지 않은 형식으로 인해 후처리 과정이 복잡했습니다.
*   **확장성 및 유지보수성 부족**: 단일 모듈로 구성된 크롤링 시스템은 새로운 웹사이트 추가나 기존 로직 변경 시 전체 시스템에 영향을 미칠 위험이 있었습니다.

### Solution
이러한 문제들을 해결하기 위해 `F-folder-scrapy` 프로젝트는 모듈화된 아키텍처와 파이프라인 기반의 데이터 처리 방식을 도입하여, 유연하고 확장 가능한 분산 크롤링 및 데이터 처리 시스템을 구축했습니다.
*   **모듈화된 크롤링 어댑터**: 각 웹사이트별 특성을 추상화하여 `API` 및 `Pipe` 계층으로 분리함으로써 크롤링 로직의 재사용성을 높이고, 신규 소스 추가를 용이하게 했습니다.
*   **파이프라인 기반 데이터 정제**: 수집된 데이터를 여러 필터 단계를 거쳐 정제하고 가공하는 파이프라인을 구축하여 데이터의 품질을 향상시키고 후처리 과정을 간소화했습니다.
*   **독립적인 서비스 계층**: 각 크롤링 소스 및 데이터 유형별로 독립적인 서비스 계층을 구성하여 시스템의 확장성과 유지보수성을 확보했습니다.
*   **비동기 처리 및 스케줄링**: `aiohttp`와 `APScheduler`를 활용하여 비동기적으로 데이터를 수집하고, 주기적인 크롤링 작업을 자동화하여 효율성을 극대화했습니다.

## 2. 기술 스택 소개

본 프로젝트는 다음과 같은 핵심 기술 스택을 활용하여 안정적이고 효율적인 데이터 수집 및 처리 환경을 구축했습니다. 각 기술의 선택 이유는 시스템의 성능, 확장성, 유지보수성을 고려한 결과입니다.

*   **Python 3.x**: 데이터 처리 및 웹 크롤링에 강력한 라이브러리 생태계를 제공하며, 빠른 개발 속도와 높은 생산성을 위해 선택되었습니다.
*   **FastAPI**: 비동기 웹 프레임워크로, 고성능 API 서버 구축에 적합합니다. 자동 문서화(Swagger UI) 기능을 통해 API 개발 및 테스트 효율성을 높였습니다.
*   **SQLAlchemy (ORM)**: 파이썬 객체와 관계형 데이터베이스를 매핑하여 데이터베이스 작업을 추상화하고, 코드의 가독성 및 유지보수성을 향상시킵니다. `aiomysql`과 연동하여 비동기 데이터베이스 접근을 지원합니다.
*   **aiohttp**: 비동기 HTTP 클라이언트로, 다수의 웹 요청을 동시에 처리하여 크롤링 성능을 극대화합니다.
*   **BeautifulSoup4 / lxml**: HTML/XML 파싱 라이브러리로, 웹 페이지에서 필요한 데이터를 효율적으로 추출하는 데 사용됩니다.
*   **APScheduler**: 유연한 작업 스케줄링 기능을 제공하여 주기적인 크롤링 작업을 자동화하고 관리합니다.
*   **Dependency Injector**: 의존성 주입(DI) 컨테이너로, 모듈 간의 결합도를 낮추고 테스트 용이성 및 코드 재사용성을 높입니다.
*   **Docker / Docker Compose**: 개발 환경의 일관성을 유지하고, 배포 과정을 간소화하기 위해 컨테이너 기반 가상화 기술을 활용합니다. (`devcontainer` 폴더 참조)
*   **GitPython**: Git 저장소와 상호작용하여 코드 버전 관리 및 배포 자동화에 활용됩니다.
*   **Flask / Flask-BasicAuth / Flask-Cors**: 관리자 페이지 또는 특정 유틸리티성 API를 구축하는 데 사용될 수 있으며, 간단한 인증 및 CORS 처리를 지원합니다.
*   **Streamlit / Pandas / Numpy / Altair / Pydeck**: 데이터 분석 및 시각화 도구로, 수집된 데이터의 경향을 파악하고 모니터링하는 데 활용될 수 있습니다.
*   **Langchain / OpenAI / Tiktoken**: LLM(Large Language Model) 기반의 텍스트 처리 및 분석 기능을 통합하여 데이터 정제 및 콘텐츠 생성에 활용될 수 있습니다.
*   **Slack SDK**: 크롤링 작업의 성공/실패 알림, 에러 로깅 등 시스템 운영 관련 알림을 Slack으로 전송하는 데 사용됩니다.

## 3. 주요 기능 설명

### 3.1. 분산 크롤링 시스템
*   **기술적 동작 방식**: 각 웹사이트별로 독립적인 `API` 모듈을 구현하여 웹 페이지에 접근하고 원시 데이터를 수집합니다. `aiohttp`를 활용한 비동기 요청으로 여러 웹사이트 또는 페이지를 동시에 크롤링하여 수집 효율을 극대화합니다. `cloudscraper` 및 `fake-useragent`를 통해 봇 감지를 우회하고 안정적인 크롤링을 지원합니다.

### 3.2. 파이프라인 기반 데이터 정제 및 가공
*   **기술적 동작 방식**: 수집된 원시 데이터는 `Pipe` 모듈을 통해 여러 단계의 필터(예: `content_filter`, `link_filters`)를 거쳐 정제됩니다. 이 필터들은 불필요한 HTML 태그 제거, 특정 키워드 필터링, 데이터 형식 변환 등의 역할을 수행하여 데이터의 품질을 높이고 서비스에 적합한 형태로 가공합니다.

### 3.3. 데이터베이스 연동 및 ORM 관리
*   **기술적 동작 방식**: `SQLAlchemy` ORM을 사용하여 수집 및 가공된 데이터를 MySQL 데이터베이스에 저장합니다. `aiomysql`을 통해 비동기 데이터베이스 세션을 관리하며, 데이터의 영속성을 확보하고 효율적인 데이터 접근을 가능하게 합니다. `sqlacodegen`을 활용하여 기존 데이터베이스 스키마로부터 ORM 모델을 자동 생성할 수 있습니다.

### 3.4. 스케줄링 및 자동화
*   **기술적 동작 방식**: `APScheduler`를 사용하여 특정 시간에 주기적으로 크롤링 작업을 실행하거나, 특정 이벤트 발생 시 작업을 트리거합니다. 이를 통해 수동 개입 없이 지속적으로 최신 데이터를 수집하고 업데이트합니다.

### 3.5. 유연한 모듈 구조 (Adapter Pattern)
*   **기술적 동작 방식**: 각 크롤링 대상 웹사이트는 `adapter` 디렉토리 내에 독립적인 모듈로 구성됩니다. 각 어댑터는 해당 웹사이트에 특화된 `API` (데이터 수집) 및 `Pipe` (데이터 정제) 로직을 캡슐화합니다. 이는 새로운 웹사이트를 추가하거나 기존 웹사이트의 크롤링 로직을 변경할 때 다른 모듈에 미치는 영향을 최소화하여 시스템의 확장성과 유지보수성을 크게 향상시킵니다.

### 3.6. 알림 시스템
*   **기술적 동작 방식**: `slack-sdk`를 활용하여 크롤링 작업의 시작/종료, 성공/실패 여부, 발생한 에러 등 주요 시스템 이벤트를 Slack 채널로 실시간 전송합니다. 이를 통해 시스템 운영자는 크롤링 상태를 즉각적으로 파악하고 문제 발생 시 신속하게 대응할 수 있습니다.

## 4. 시스템 아키텍처

본 프로젝트는 계층형 아키텍처를 기반으로 하며, 각 계층은 명확한 역할과 책임을 가집니다.

```
+---------------------+       +---------------------+       +---------------------+
|      Scheduler      |       |      FastAPI        |       |      External       |
| (APScheduler)       |------>| (Web API)           |       |      Websites       |
+---------------------+       +---------------------+       +---------------------+
          |                             |                             ^
          |                             |                             |
          v                             v                             | (HTTP/HTTPS)
+---------------------+       +---------------------+       +---------------------+
|    Application      |       |    Controller       |       |      Adapter        |
| (Entry Point)       |       | (Request Handling)  |       | (API & Pipe)        |
+---------------------+       +---------------------+       +---------------------+
          |                             |                             |
          |                             |                             |
          v                             v                             v
+---------------------+       +---------------------+       +---------------------+
|    Configuration    |       |    Service Layer    |       |    Database         |
| (DI, Logger, DB)    |       | (Business Logic)    |       | (SQLAlchemy, MySQL) |
+---------------------+       +---------------------+       +---------------------+
```

*   **Scheduler**: `APScheduler`를 통해 주기적인 크롤링 작업을 트리거합니다.
*   **FastAPI (Web API)**: 외부 요청을 처리하는 RESTful API 엔드포인트를 제공합니다. 관리자 기능, 데이터 조회 등을 담당할 수 있습니다.
*   **Application (Entry Point)**: `app.py` 파일로, FastAPI 애플리케이션을 초기화하고 미들웨어, 데이터베이스, 컨트롤러 등을 설정합니다. 배포 모드에서는 스케줄러를 시작합니다.
*   **Controller**: FastAPI 라우터를 통해 HTTP 요청을 받아 적절한 서비스 계층으로 전달합니다. 각 크롤링 소스별 엔드포인트를 관리합니다.
*   **Adapter (API & Pipe)**:
    *   **API**: 특정 웹사이트로부터 데이터를 수집하는 로직을 캡슐화합니다. `aiohttp`를 사용하여 비동기 웹 요청을 수행합니다.
    *   **Pipe**: 수집된 원시 데이터를 정제하고 가공하는 파이프라인을 정의합니다. 여러 필터(예: `content_filter`, `link_filters`)를 포함할 수 있습니다.
*   **Service Layer**: 비즈니스 로직을 담당하며, 어댑터로부터 데이터를 받아 가공하고 데이터베이스에 저장하는 역할을 수행합니다.
*   **Database**: `SQLAlchemy` ORM을 통해 MySQL 데이터베이스와 상호작용합니다. 데이터의 영속성을 관리하고, 효율적인 데이터 접근을 제공합니다.
*   **Configuration**: `Dependency Injector`를 활용하여 의존성을 관리하고, 로거 설정, 데이터베이스 연결 설정 등 애플리케이션 전반의 설정을 담당합니다.

## 5. API 또는 핵심 모듈 구조 설명

### 5.1. `src/adapter` 모듈
`src/adapter`는 각 크롤링 대상 웹사이트에 대한 인터페이스를 추상화하고 구현하는 핵심 모듈입니다. 각 서브 디렉토리(예: `eyesmag`, `fashionchingu`)는 특정 웹사이트에 대한 어댑터 역할을 수행합니다.

```
src/adapter/
├── abstract/
│   ├── abstract_dto.py       # 데이터 전송 객체(DTO) 추상화
│   └── abstract_pipe.py      # 파이프라인 추상화
├── database/
│   ├── orm.py                # SQLAlchemy ORM 모델 정의
│   └── session/              # DB 세션 관리
├── slack.py                  # Slack 알림 어댑터
├── utils.py                  # 어댑터 공통 유틸리티
├── eyesmag/                  # Eyesmag 크롤링 어댑터
│   ├── api/                  # Eyesmag API 인터페이스
│   │   └── eyesmag_api.py
│   └── pipe/                 # Eyesmag 데이터 정제 파이프라인
│       └── eyesmag_pipe.py
└── ... (다른 웹사이트 어댑터들)
```

*   **`abstract`**: 모든 어댑터가 따르는 추상 클래스 및 인터페이스를 정의하여 일관된 구조를 강제하고 확장성을 보장합니다.
*   **`database`**: 데이터베이스 관련 ORM 모델 및 세션 관리 로직을 포함합니다.
*   **`[website_name]/api`**: 특정 웹사이트의 데이터를 수집하는 API 로직을 구현합니다. 웹 요청, 응답 파싱 등을 담당합니다.
*   **`[website_name]/pipe`**: 특정 웹사이트에서 수집된 데이터를 정제하고 가공하는 파이프라인 로직을 구현합니다. 여러 필터들을 조합하여 사용합니다.

### 5.2. `src/application/controller` 모듈
`src/application/controller`는 FastAPI 애플리케이션의 엔드포인트를 정의하고 HTTP 요청을 처리하는 모듈입니다. 각 파일은 특정 크롤링 소스 또는 기능에 대한 컨트롤러 역할을 합니다.

```
src/application/controller/
├── entry.py                  # 애플리케이션 진입점 관련 컨트롤러
├── eyesmag.py                # Eyesmag 관련 API 엔드포인트
├── fashionchingu.py          # Fashionchingu 관련 API 엔드포인트
├── posts.py                  # 게시물 관련 공통 API 엔드포인트
└── ... (다른 웹사이트 컨트롤러들)
```

*   각 컨트롤러는 FastAPI의 라우터를 사용하여 특정 URL 경로에 대한 HTTP 메서드(GET, POST 등)를 정의하고, 요청을 받아 서비스 계층으로 전달하는 역할을 합니다.

## 6. 폴더 구조

```
.
├── .DS_Store
├── .gitignore
├── README.md
├── F-folder-scrapy/
│   ├── makefile
│   ├── readme.md
│   ├── requirements.txt
│   ├── devcontainer/
│   │   └── docker-compose.yml
│   ├── document/
│   │   ├── admin.service
│   │   ├── nginx.conf
│   │   └── ADR/
│   │       └── 20231110 - refactor '복수 카테고리 크롤링 모듈'.md
│   ├── sqls/                 # 데이터베이스 스키마 및 초기 데이터 SQL 파일
│   │   ├── eyesmag.sql
│   │   ├── fashionchingu.sql
│   │   └── ...
│   └── src/
│       ├── __init__.py
│       ├── app.py            # 애플리케이션 진입점
│       ├── adapter/          # 외부 시스템 연동 (크롤링 API, DB, Slack 등)
│       │   ├── abstract/
│       │   ├── database/
│       │   ├── eyesmag/
│       │   ├── fashionchingu/
│       │   └── ...
│       ├── application/      # FastAPI 애플리케이션 계층 (Controller, Router)
│       │   ├── controller/
│       │   └── routers.py
│       ├── config/           # 애플리케이션 설정 (DI, Logger, Scheduler)
│       │   ├── container/
│       │   ├── logger/
│       │   └── scheduler.py
│       ├── libs/             # 공통 라이브러리 및 유틸리티
│       │   ├── resource/     # 리소스 관리 (예: 이미지, 파일)
│       │   └── utils/        # 공통 유틸리티 함수
│       └── tests/            # 테스트 코드
```

## 7. 데이터 흐름 정리

본 시스템의 데이터 흐름은 크게 **크롤링 및 수집**, **정제 및 가공**, **저장 및 활용**의 세 단계로 나눌 수 있습니다.

1.  **크롤링 작업 트리거**:
    *   `APScheduler`에 의해 주기적으로 크롤링 작업이 시작되거나, FastAPI 엔드포인트(`Controller`)를 통해 수동으로 크롤링 요청이 들어옵니다.
    *   요청은 `Application` 진입점을 거쳐 해당 크롤링 소스에 맞는 `Service` 계층으로 전달됩니다.

2.  **데이터 수집 (Adapter - API)**:
    *   `Service` 계층은 특정 웹사이트에 해당하는 `Adapter`의 `API` 모듈을 호출합니다.
    *   `API` 모듈은 `aiohttp`를 사용하여 대상 웹사이트에 HTTP 요청을 보내고, `BeautifulSoup4` 등을 활용하여 응답 HTML에서 원시 데이터를 추출합니다.
    *   수집된 원시 데이터는 `abstract_dto.py`에 정의된 DTO(Data Transfer Object) 형태로 캡슐화되어 반환됩니다.

3.  **데이터 정제 및 가공 (Adapter - Pipe)**:
    *   `Service` 계층은 수집된 DTO를 해당 `Adapter`의 `Pipe` 모듈로 전달합니다.
    *   `Pipe` 모듈은 `content_filter`, `link_filters` 등 여러 필터 단계를 거쳐 데이터를 정제하고 가공합니다. 이 과정에서 불필요한 정보 제거, 형식 변환, 유효성 검사 등이 수행됩니다.
    *   LLM(Langchain, OpenAI)이 통합될 경우, 이 단계에서 텍스트 요약, 키워드 추출, 카테고리 분류 등의 고급 정제 작업이 추가될 수 있습니다.

4.  **데이터 저장 (Service -> Database)**:
    *   정제된 데이터는 `Service` 계층에서 `SQLAlchemy` ORM을 통해 MySQL 데이터베이스에 저장됩니다.
    *   데이터베이스 세션은 `async_session_factory.py`를 통해 비동기적으로 관리됩니다.

5.  **데이터 활용 (FastAPI - Controller)**:
    *   저장된 데이터는 FastAPI 엔드포인트(`Controller`)를 통해 외부 시스템이나 프론트엔드 애플리케이션에 제공될 수 있습니다.
    *   관리자 페이지 등에서 데이터를 조회하거나, 분석 및 시각화 도구(Streamlit)를 통해 활용될 수 있습니다.

6.  **알림 및 로깅**:
    *   크롤링 과정에서 발생하는 주요 이벤트(시작, 완료, 에러)는 `slack.py` 어댑터를 통해 Slack으로 알림이 전송됩니다.
    *   `sql_logger.py`를 통해 데이터베이스 관련 로그가 기록되어 시스템 운영 및 디버깅에 활용됩니다.

## 8. 프로젝트를 만들며 고민한 점 (기술적 의사결정 기록)

### 8.1. 복수 카테고리 크롤링 모듈 리팩토링 (ADR: 20231110)
초기 `krazemagazine` 웹사이트 크롤링 모듈 개발 시, `get_content_list` 메소드 내에서 `_endpoint` 변수를 통해 카테고리를 구분하는 방식을 채택했습니다. 이는 개발 초기 단계에서 복잡성을 줄이는 데 효과적이었으나, 다음과 같은 문제점을 야기했습니다.

*   **유지보수성 저하**: `krazemagazine`의 각 카테고리 페이지가 일관성 없는 HTML 태그 구조를 가지고 있어, 향후 웹사이트 구조 변경 시 수정 가능성이 높았습니다. 단일 메소드 내에서 모든 카테고리를 처리하는 방식은 변경에 취약했습니다.
*   **데이터 구분 모호성**: 수집된 데이터가 일괄적으로 처리되어 서비스 로직 내에서 카테고리별로 데이터를 명확하게 구분하고 활용하기 어려웠습니다.

이러한 문제 해결을 위해 `filter`, `api`, `service` 계층에서의 카테고리 분리 방안을 심층적으로 검토했습니다.

*   **Filter 계층**: 수집 단계에서 `kdrama`와 `tvmovies`는 동일한 방식으로 추출 가능했으나, 차후 유지보수를 위해 분리 필요성을 인지했습니다. 현재는 기존 방식을 유지하되, 웹사이트 변형이 클 경우 새로운 모듈로 재구성하는 확장성을 고려했습니다.
*   **API 계층**: 수집된 데이터에 대한 명시적인 구분을 위해 `get_content_list`를 `get_kdramas_content_list`, `get_tvmovies_content_list`로 분리하여 데이터의 출처를 명확히 했습니다.
*   **Service 계층**: 수집된 데이터의 가공 및 유스케이스에서도 독립성을 보장하기 위해 `fetch__content_list`를 `fetch_kdrama_content_list`, `fetch_tvmovies_content_list`로 분리했습니다.

**결과**: 이 리팩토링을 통해 각 카테고리별 크롤링 및 데이터 처리 로직의 독립성을 확보하고, 웹사이트 구조 변경에 대한 유연성을 높여 시스템의 유지보수성과 확장성을 크게 개선했습니다.

### 8.2. 비동기 처리 도입
대량의 웹 요청을 효율적으로 처리하고 시스템의 응답성을 높이기 위해 `aiohttp`, `aiomysql` 등 비동기 라이브러리를 적극적으로 도입했습니다. 이는 I/O 바운드 작업이 많은 크롤링 시스템의 특성을 고려한 결정으로, 동기 방식 대비 월등한 성능 향상을 가져왔습니다.

### 8.3. 의존성 주입 (Dependency Injection) 활용
`Dependency Injector`를 사용하여 모듈 간의 의존성을 명시적으로 관리했습니다. 이는 각 컴포넌트의 독립성을 보장하고, 단위 테스트를 용이하게 하며, 코드 재사용성을 높이는 데 기여했습니다. 특히, 데이터베이스 세션이나 API 클라이언트와 같은 핵심 객체들을 컨테이너를 통해 관리함으로써 애플리케이션의 유연성을 확보했습니다.

### 8.4. ORM (SQLAlchemy) 도입
데이터베이스와의 상호작용을 추상화하고 객체 지향적인 방식으로 데이터를 다루기 위해 `SQLAlchemy` ORM을 도입했습니다. 이를 통해 SQL 쿼리를 직접 작성하는 대신 파이썬 객체를 통해 데이터를 조작할 수 있게 되어 개발 생산성을 높이고 SQL 인젝션과 같은 잠재적 보안 취약점을 줄였습니다.

## 9. 설치 / 실행 방법

### 9.1. 개발 환경 설정 (DevContainer)
본 프로젝트는 VS Code DevContainer를 지원하여 일관된 개발 환경을 쉽게 구축할 수 있습니다.

1.  **Docker 설치**: 시스템에 Docker Desktop이 설치되어 있어야 합니다.
2.  **VS Code 확장 설치**: VS Code에서 "Remote - Containers" 확장을 설치합니다.
3.  **DevContainer 열기**:
    *   VS Code에서 프로젝트 폴더를 엽니다.
    *   하단 좌측의 "Remote Indicator"를 클릭하거나, `Ctrl+Shift+P` (Windows/Linux) 또는 `Cmd+Shift+P` (macOS)를 눌러 Command Palette를 엽니다.
    *   "Remote-Containers: Reopen in Container" 또는 "Remote-Containers: Open Folder in Container..."를 선택하고 프로젝트 폴더를 지정합니다.
    *   DevContainer가 빌드되고 실행되면, 필요한 모든 의존성이 자동으로 설치됩니다.

### 9.2. 수동 설치 및 실행

#### 9.2.1. 의존성 설치
```bash
cd F-folder-scrapy
pip install -r requirements.txt
```

#### 9.2.2. 환경 변수 설정
프로젝트 루트(`F-folder-scrapy`)에 `.env` 파일을 생성하고 다음 환경 변수를 설정합니다.

```dotenv
MODE=DEV # 또는 DEPLOY
MYSQL_USER=your_mysql_user
MYSQL_PASSWORD=your_mysql_password
MYSQL_NAME=your_mysql_database_name
MYSQL_HOST=localhost
MYSQL_PORT=3306
# Slack 알림을 위한 토큰 (선택 사항)
SLACK_BOT_TOKEN=xoxb-your-slack-bot-token
SLACK_CHANNEL_ID=your-slack-channel-id
```

#### 9.2.3. 데이터베이스 설정
`sqls/` 디렉토리의 SQL 파일을 사용하여 MySQL 데이터베이스를 초기화합니다.

```bash
mysql -u your_mysql_user -p your_mysql_database_name < sqls/post.sql
mysql -u your_mysql_user -p your_mysql_database_name < sqls/post_history.sql
# 필요한 경우 다른 SQL 파일들도 실행
```

#### 9.2.4. 애플리케이션 실행
```bash
cd F-folder-scrapy/src
uvicorn app:server --host 0.0.0.0 --port 8000 --reload
```
*   `--reload` 옵션은 개발 환경에서 코드 변경 시 자동으로 서버를 재시작합니다.
*   배포 환경에서는 `gunicorn`과 같은 WSGI 서버를 사용하는 것이 권장됩니다.
    ```bash
    gunicorn -w 4 -k uvicorn.workers.UvicornWorker src.app:server --bind 0.0.0.0:8000
    ```

## 10. 향후 개선 계획과 TODO

*   **크롤링 대상 웹사이트 확장**: 현재 지원하는 웹사이트 외에 다양한 패션/라이프스타일 웹사이트를 추가하여 데이터 소스를 확장할 계획입니다.
*   **LLM 기반 데이터 정제 및 콘텐츠 생성 고도화**: `Langchain`, `OpenAI` 등을 활용하여 수집된 텍스트 데이터의 요약, 키워드 추출, 감성 분석, 새로운 콘텐츠 생성 등 LLM 기반의 고급 데이터 처리 기능을 통합할 예정입니다.
*   **데이터 시각화 및 모니터링 대시보드 구축**: `Streamlit`, `Altair`, `Pydeck` 등을 활용하여 크롤링 현황, 데이터 수집량, 에러 발생률 등을 실시간으로 모니터링하고 시각화하는 대시보드를 구축할 계획입니다.
*   **에러 처리 및 재시도 로직 강화**: 네트워크 오류, 웹사이트 구조 변경 등 크롤링 과정에서 발생할 수 있는 다양한 예외 상황에 대한 견고한 에러 처리 및 자동 재시도 로직을 구현하여 시스템의 안정성을 높일 것입니다.
*   **프록시 관리 및 로테이션**: 봇 감지 우회를 위한 프록시 서버 관리 및 자동 로테이션 기능을 도입하여 크롤링의 안정성과 효율성을 더욱 향상시킬 것입니다.
*   **컨테이너 오케스트레이션 (Kubernetes)**: 대규모 분산 환경에서의 배포 및 관리를 위해 Kubernetes와 같은 컨테이너 오케스트레이션 도구 도입을 검토할 예정입니다.

## 11. 기여자 또는 참고 자료

*   **Author**: [작성자 이름] (본인 이름 또는 팀 이름)
*   **References**:
    *   [FastAPI Documentation](https://fastapi.tiangolo.com/)
    *   [SQLAlchemy Documentation](https://docs.sqlalchemy.org/en/14/)
    *   [APScheduler Documentation](https://apscheduler.readthedocs.io/en/stable/)
    *   [Dependency Injector Documentation](https://python-dependency-injector.ets-labs.org/)
    *   [BeautifulSoup4 Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
    *   [aiohttp Documentation](https://docs.aiohttp.org/en/stable/)
    *   [VS Code Dev Containers](https://code.visualstudio.com/docs/devcontainers/containers)
