## Cloud Computing Team B Project Guidelines
클라우드 컴퓨팅 팀 B의 프로젝트 가이드라인입니다.

실제 코드를 작성하고 관리, 배포하는 등 전반적인 부분에 대한 내용으로 최대한 지켜주시면 감사하겠습니다.

이는 항시 변경될 수 있으며 변경이 생기는 경우 공지할 예정입니다.

### 1. 프로젝트 구조
- API 서버
- 유저 에뮬레이터 서버
- 스트레스 인스턴스 매니저
- 데이터베이스

하단 다이어그램을 참조해주시길 바랍니다.

### 2. 기술 스택
- 프론트엔드: React + (CSS 프레임워크?)
- API 서버: Spring Boot
- 유저 에뮬레이터 서버: Javascript (Node.js)
- 스트레스 인스턴스 매니저 및 스트레스 인스턴스: Golang
- 데이터베이스: MySQL
- 정적 파일 서빙 및 리버스프록시: Nginx (혹은 Caddy)

내부적으로 서버 간 통신에서 gRPC의 사용을 고려해보았으나 과도하게 복잡성만 높일 것 같아 RESTful한 방식을 취하는것이 좋을 것 같아 보입니다.

정적 파일 서빙 및 리버스프록시의 경우 Nginx가 Caddy에 비해 눈에 띄는 성능 이점을 가집니다. 다만 하단의 이유들로 Caddy 사용을 고려중입니다.
- 자동화된 SSL 인증서 발급 및 갱신
- Nginx에 비해 간단한 컨피그
- 단일 바이너리 형식으로 쉬운 배포 가능
- Nginx를 사용해야할 만큼의 성능 필요성 불확실

### 3. 개발 환경 및 도구
각각의 백엔드 서버에 관해서는 개인이 사용하기 편한 개발환경을 사용해주시면 될 것 같습니다.

프론트엔드의 경우 공동작업이 이루어지기 때문에 두분의 상의 하에 개발환경을 맞춰주시면 될 것 같습니다.

해당 섹션에서는 개발 관리 도구에 관해서만 기술합니다.

- Git: Github Organization 아래의 web-client, api, user-emulator, stress-instance-manager, stress-instance, deploy 레포지토리에서 관리합니다. Git 관련 내용에 관해서는 하단에 자세히 기술할 예정입니다.
- CI/CD: Github Actions
- Deployment: Docker + Terraform

### 4. 프로젝트 관리
위에서 언급한 것처럼 Git을 통해 프로젝트를 관리할 계획입니다. 

하단 내용들을 지켜주시면 감사하겠습니다.

#### 4.1. 브랜치
브랜치는 main, dev 브랜치를 사용합니다.

- main 브랜치는 배포 가능한 상태를 유지합니다. **무조건** 배포가 가능한 상태여야 하며 최종적으로 프로덕션에 배포될 코드가 들어가야 합니다.
- dev 브랜치는 개발 중인 기능들을 모아두는 브랜치입니다. 배포가 이뤄지지 않는, 개발 중인 코드가 들어갈 브랜치이며 배포가 불가능한 상태여도 관계없습니다. 배포가 가능한 시점, 배포를 원하는 시점에서 main 브랜치에 merge 합니다.

추가적으로 .gitignore 파일을 통해 시크릿 및 개발 도구 설정 파일, 빌드 캐시 등을 **필수적으로** 제외합니다.

최대한 모든 파이프라인을 자동화할 계획이므로 main 브랜치에 대한 사항은 필수적으로 시켜주시면 감사하겠습니다.

#### 4.2. 커밋 메시지
커밋 메시지는 기본적으로 하단의 규칙을 따릅니다.

(type): (subject)

(type)에는 하단의 타입을 사용합니다.
- feat: 새로운 기능 추로
- fix: 버그 수정
- refactor: 코드 리팩토링
- test: 테스트 코드 추가
- misc: 기타 사항

(subject)에는 커밋 내용을 기술합니다.

예시:
- feat: user 라우터 핸들러 추가
- fix: 버그 수정
- refactor: function1 함수 성능 계선

#### 4.3. 코드 가이드라인
코드 가이드라인은 각 언어에 맞게 자유롭게 적용해주시면 될 것 같습니다. 

#### 4.4. 테스트
##### 4.4.1. 유닛 테스트
유닛 테스트는 각 언어에 맞는 유닛 테스트 프레임워크를 사용해주시면 될 것 같습니다.

코드 작성 -> 유닛 테스트 작성의 과정을 반복하며 진행하기를 권장드리며 최소 80%의 커버리지를 유지하는 것을 목표로 합니다.

##### 4.4.2. API 엔드포인트 테스트
API Specification이 완성되는데로 API 서버 테스트용 프로그램을 작성할 예정입니다.

해당 프로그램은 API 서버, 유저 에뮬레이터, 스트레스 인스턴스 매니저 각각에 대해서 작성할 예정이며 하단과 같은 방법으로 사용해주시면 됩니다.

Linux/MacOS:
```bash
./test api-server http://localhost:8080
./test user-emulator http://localhost:3000
./test stress-instance-manager http://localhost:4000
```

Windows:
```bash
test.exe api-server http://localhost:8080
test.exe user-emulator http://localhost:3000
test.exe stress-instance-manager http://localhost:4000
```

#### 4.5. 환경 변수
백엔드 서버들의 모든 컨피그 정보들은 환경 변수로부터 받아와야 합니다.

예시
- 기타 백엔드 서버 주소
- 데이터베이스 정보
- 시크릿 키
- 포트 번호

도커로 모든 백엔드의 서버들을 패키징할 예정이기에 꼭 지켜주셔야 합니다.

프로젝트에서 사용하는 환경 변수들은 .env 파일에 예시 값과 함께 정의해주시면 됩니다.

.env:
```
# 기타 백엔드 서버 주소
OTHER_SERVER_ADDRESS=http://localhost:8080

# 데이터베이스 정보
DB_HOST=localhost
DB_PORT=3306

# 시크릿 키
SECRET_KEY=your_secret_key

# 포트 번호
PORT=8080
```
