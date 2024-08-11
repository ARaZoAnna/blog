# Github Action으로 CI/CD 구축

## 배포란?

- 개발을 완료한 후 jar 파일을 만들어 서버(AWS)에 배포하는 것을 말합니다.
- 배포를 통해 서비스를 릴리즈하며 사용자들이 URL을 사용하여 서비스를 이용할 수 있게 됩니다.
- 배포에는 젠킨스,도커 등 다양한 방법이 있지만 많이 사용하는 깃허브 액션을 사용하였습니다.

## Github Action

github에서 공식적으로 제공하는 CI/CD 툴이다. 개발의 work flow를 자동화할 수 있게 도와주는 툴이다.

코어 개념

- Workflow
    - 자동화된 전체 프로세스이다. 하나의 이상의 Job으로 구성되고, Event에 의해 예약되거나 트리거 될 수 있는 자동화된 절차를 말한다.
    - Workflow 파일은 YAML으로 작성되고, Github Repository의 `.github/workflows` 폴더 아래에 저장된다.
    - Github에서 YAML 파일로 정의한 자동화 동작을 전달하면, Github Actions는 해당 파일을 기반으로 그대로 실행한다.
- Event
    - Workflow를 트리거(실행)하는 특정 활동이나 규칙이다.
    - 예를 들어, 누군가가 커밋을 레포지토리에 푸시하거나 풀 요청이 생성 될 때 Github에서 활동이 시작될 수 있다.
    
    ```java
    #워크플로가 시작될 조건(push 할 때) 지정
    on:
        push:
          branches: [ master, dev ]
        pull_request:
          branches: [ master ]
          paths:
            - "**.js"
          paths-ignore:
            - "doc/**"
    ```
    

## CI/CD란?

### CI(지속적 통합)

- 개발자를 위해 빌드와 테스트를 자동화합니다.
- 변경 사항을 자동으로 테스트해 애플리케이션에 문제 없음을 보장합니다.
- 코드를 정기적으로 빌드하고 테스트하므로 여러명이 동시에 작업하는 경우 충동을 방지하고 모니터링합니다.
- 테스트, 빌드, Dockerizing, 저장소에 전달하는 것까지 프로덕션 환경으로 서비스를 배포할 수 있도록 준비하는 프로세스이다.

### CD(지속적 배포)

- CI 작업을 끝낸 다음 실행하는 작업입니다. 배포 준비가 된 코드를 자동으로 서버에 배포하는 작업을 자동화합니다.
- 개발자가 수작업으로 배포하지 않아도 자동으로 배포하니 편리합니다.

## 배포 과정

### 사전준비

- 깃허브에 코드 작성이 완료되어 모두 커밋되었습니다.
- AWS EC2를 생성합니다. (public IP 필요)

### 깃허브 액션

- 깃허브에서 제공하는 서비스입니다.
- 누군가 코드를 작성해 깃허브에 업데이트하면 해당 코드에 문제가 없는지 자동으로 빌드, 테스트한 이후 배포까지 할 수 있습니다.

### CI/CD 구현

- Github의 배포할 레포지토리에서 settings에 들어갑니다.

    
    ![settings](img/github/settings.jpg)
    

settings → Secrets and variables → Actions → New repository secret

![EC2](img/github/ec2.png)

EC2_HOST : AWS IP주소

EC2_USER : AWS 운영체제 계정. AWS 기본 디폴트 계정이 ubuntu라서 ubuntu를 기재

EC2_KEY : AWS의 pem키를 넣습니다.

<aside>
💥 AWS에서 pem키 찾는 법
- wsl에 접속
- 홈으로 이동 : cd ~
- pem키 : cat ./[이름].pem

</aside>

- IntelliJ 코드 내 프로젝트 최상단에 `.github` 디렉터리를 만듭니다. 그 안에 `workflows` 디렉터리를 다시 반들고, `gradle.yml`  파일을 생성합니다.