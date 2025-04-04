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

```
autodeploy-backend-cicd/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── autodeploy/
│   │   │           ├── common
│   │   │			 	 ├── config/     # 설정 클래스
│   │   │           └── util/           # 유틸리티 클래스
│   │   │           ├── exception/      # 예외 처리
│   │   │           ├── controller/     # REST 컨트롤러
│   │   │           ├── domain/         # 도메인 모델
│   │   │           │   ├── entity/     # JPA 엔티티
│   │   │           │   └── repository/ # 데이터 레포지토리
│   │   │           ├── dto/            # 요청/응답 데이터 모델

│   │   │           ├── service/        # 비즈니스 로직

│   │   └── resources/
│   │       └── application.yml         # 애플리케이션 설정
│   └── test/                           # 테스트 코드
└── pom.xml                             # Maven 프로젝트 설정
```

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

- `GET /api/requirements` - 모든 요구사항 조회
- `GET /api/requirements/{id}` - 특정 요구사항 조회
- `POST /api/requirements` - 새 요구사항 생성
- `PUT /api/requirements/{id}` - 요구사항 업데이트
- `DELETE /api/requirements/{id}` - 요구사항 삭제
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