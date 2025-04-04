# Autodeploy Backend CICD Service

이 레포지토리는 자동화된 CI/CD 파이프라인을 관리하기 위한 임시 데모 백엔드 서비스입니다. 사용자 요구사항 입력부터 GitLab 커밋, Jenkins 빌드, GitOps 태그 갱신, ArgoCD 배포까지 전체 CI/CD 파이프라인을 관리합니다.

## 데모 프로젝트 정보

이 프로젝트는 개념 증명(POC)을 위한 임시 데모 프로젝트로, 실제 프로덕션 환경에서의 사용을 위해서는 추가적인 개발과 보안 검토가 필요합니다.

## 기능 개요

- **요구사항 관리**: 사용자 요구사항을 입력 받고 관리합니다.
- **소스코드 관리**: 요구사항에 따라 소스코드 처리를 자동화합니다.
- **Git 통합**: 타겟 프로젝트 레포지토리에 코드 변경사항을 자동으로 커밋합니다.
- **Jenkins 연동**: 자동화된 빌드 프로세스를 Jenkins를 통해 실행합니다.
- **GitOps 업데이트**: Kustomization 파일의 이미지 태그를 최신 버전으로 업데이트합니다.
- **ArgoCD 배포**: 애플리케이션을 ArgoCD를 통해 Kubernetes 클러스터에 배포합니다.
- **배포 모니터링**: 전체 배포 프로세스를 실시간으로 모니터링합니다.

## 기술 스택

- **Java 17**
- **Spring Boot**
- **Spring Data JPA**
- **PostgreSQL(임의)**
- **Maven**
- **JGit** (Git 연동)
- **Jenkins API 클라이언트**
- **RESTful API**

## 프로젝트 구조

프로젝트는 도메인 중심 아키텍처를 채택하여 각 CI/CD 구성 요소를 독립적인 도메인으로 구성합니다.

```
autodeploy-backend-cicd/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── autodeploy/
│   │   │           ├── common/            # 공통 모듈
│   │   │           │   ├── config/        # 공통 설정
│   │   │           │   ├── exception/     # 예외 처리
│   │   │           │   ├── util/          # 공통 유틸리티
│   │   │           │   └── dto/           # 공통 DTO
│   │   │           │
│   │   │           ├── requirement/       # 요구사항 도메인
│   │   │           │   ├── controller/    # 요구사항 API
│   │   │           │   ├── service/       # 비즈니스 로직
│   │   │           │   ├── repository/    # 데이터 액세스
│   │   │           │   ├── entity/        # 엔티티 모델
│   │   │           │   └── dto/           # 요청/응답 모델
│   │   │           │
│   │   │           ├── git/               # Git 도메인
│   │   │           │   ├── controller/    # Git API
│   │   │           │   ├── service/       # Git 서비스
│   │   │           │   ├── config/        # Git 설정
│   │   │           │   ├── util/          # Git 유틸리티
│   │   │           │   └── dto/           # Git DTO
│   │   │           │
│   │   │           ├── jenkins/           # Jenkins 도메인
│   │   │           │   ├── controller/    # Jenkins API
│   │   │           │   ├── service/       # Jenkins 서비스
│   │   │           │   ├── config/        # Jenkins 설정
│   │   │           │   ├── util/          # Jenkins 유틸리티
│   │   │           │   └── dto/           # Jenkins DTO
│   │   │           │
│   │   │           ├── gitops/            # GitOps 도메인
│   │   │           │   ├── controller/    # GitOps API
│   │   │           │   ├── service/       # GitOps 서비스
│   │   │           │   └── dto/           # GitOps DTO
│   │   │           │
│   │   │           ├── argocd/            # ArgoCD 도메인
│   │   │           │   ├── controller/    # ArgoCD API
│   │   │           │   ├── service/       # ArgoCD 서비스
│   │   │           │   ├── config/        # ArgoCD 설정
│   │   │           │   ├── util/          # ArgoCD 유틸리티
│   │   │           │   └── dto/           # ArgoCD DTO
│   │   │           │
│   │   │           └── deployment/        # 배포 도메인
│   │   │               ├── controller/    # 배포 API
│   │   │               ├── service/       # 배포 오케스트레이션
│   │   │               ├── repository/    # 배포 데이터 액세스
│   │   │               ├── entity/        # 배포 엔티티
│   │   │               └── dto/           # 배포 DTO
│   │   │
│   │   └── resources/
│   │       └── application.yml            # 애플리케이션 설정
│   └── test/                              # 테스트 코드
└── pom.xml                                # Maven 프로젝트 설정
```

## 도메인 설계 원칙

본 프로젝트는 다음과 같은 도메인 중심 설계 원칙을 따릅니다:

1. **도메인 독립성**: 각 도메인은 자체 컨트롤러, 서비스, 저장소 등을 가지며 독립적으로 기능합니다.
2. **도메인별 응집도**: 특정 CI/CD 도구와 관련된 모든 코드는 해당 도메인 패키지 내에 존재합니다.
3. **인터페이스를 통한 통신**: 도메인 간 통신은 잘 정의된 인터페이스를 통해 이루어집니다.
4. **오케스트레이션**: `deployment` 도메인이 전체 CI/CD 프로세스를 조율합니다.

## 워크플로우

1. **요구사항 입력**: 프론트엔드에서 요구사항 정보를 입력 받습니다.
2. **소스코드 생성**: 요구사항에 따라 대상 프로젝트의 소스코드를 생성합니다.
3. **GitLab 커밋**: 생성된 소스코드를 GitLab 레포지토리에 커밋합니다.
4. **Jenkins 빌드**: Jenkins 파이프라인을 통해 컴파일, 테스트, 이미지 빌드를 수행합니다.
5. **GitOps 업데이트**: 생성된 이미지 태그로 GitOps 레포지토리의 Kustomization 파일을 업데이트합니다.
6. **ArgoCD 배포**: 새 버전을 ArgoCD를 통해 Kubernetes 클러스터에 배포합니다.

## 설정 및 실행 방법

### 사전 요구사항

- JDK 17
- Maven
- PostgreSQL 데이터베이스(임의)
- Git, Jenkins, ArgoCD 접근 권한

### 환경 변수 설정

다음 환경 변수를 설정하세요:

```
GIT_USERNAME=your-git-username
GIT_TOKEN=your-git-token
JENKINS_USERNAME=your-jenkins-username
JENKINS_TOKEN=your-jenkins-token
ARGOCD_USERNAME=your-argocd-username
ARGOCD_PASSWORD=your-argocd-password
```

### 애플리케이션 실행

```bash
# Maven을 사용한 빌드
mvn clean install

# 애플리케이션 실행
mvn spring-boot:run
```

또는 JAR 파일을 직접 실행:

```bash
java -jar target/autodeploy-backend-cicd-0.0.1-SNAPSHOT.jar
```

## API 문서

아래는 주요 API 엔드포인트 목록입니다:

### 요구사항 API
- `GET /api/requirements` - 모든 요구사항 조회
- `GET /api/requirements/{id}` - 특정 요구사항 조회
- `POST /api/requirements` - 새 요구사항 생성
- `PUT /api/requirements/{id}` - 요구사항 업데이트
- `DELETE /api/requirements/{id}` - 요구사항 삭제

### Git API
- `POST /api/git/commit` - 코드 변경사항 커밋
- `GET /api/git/status/{projectKey}` - Git 저장소 상태 조회

### Jenkins API
- `POST /api/jenkins/build` - 빌드 작업 시작
- `GET /api/jenkins/builds/{buildId}` - 빌드 상태 조회
- `GET /api/jenkins/builds/{buildId}/logs` - 빌드 로그 조회

### GitOps API
- `POST /api/gitops/update-tag` - 이미지 태그 업데이트

### ArgoCD API
- `POST /api/argocd/sync/{applicationName}` - 애플리케이션 동기화
- `GET /api/argocd/status/{applicationName}` - 애플리케이션 상태 조회

### 배포 API
- `POST /api/deployments/{requirementId}` - 배포 시작
- `GET /api/deployments/{id}` - 배포 상태 조회
- `GET /api/deployments/{id}/logs` - 배포 로그 조회
- `POST /api/deployments/{id}/cancel` - 배포 취소

## 추가 개발 예정 기능

- 실시간 로그 스트리밍
- 다중 환경 배포 지원 (개발, 테스트, 운영)
- 배포 이력 및 버전 관리
- 알림 시스템 통합

## 주의사항

이 프로젝트는 데모/POC 용도로만 사용됩니다. 프로덕션 환경 배포 전에 다음 사항을 고려해야 합니다:

- 보안 검토 및 강화
- 에러 처리 개선
- 성능 최적화
- 철저한 테스트

## 라이선스

이 프로젝트는 내부 데모 목적으로 개발되었으며, 모든 권리는 회사에 있습니다.