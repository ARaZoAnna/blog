# Github Action으로 CI/CD 구축

## 배포란?

- 개발을 완료한 후 jar 파일을 만들어 서버(AWS)에 배포하는 것을 말합니다.
- 배포를 통해 서비스를 릴리즈하며 사용자들이 URL을 사용하여 서비스를 이용할 수 있게 됩니다.
- 배포에는 젠킨스,도커 등 다양한 방법이 있지만 많이 사용하는 깃허브 액션을 사용하였습니다.

## Github Action

github에서 공식적으로 제공하는 CI/CD 툴이다. 개발의 work flow를 자동화할 수 있게 도와주는 툴이다.

코어 개념

###### Workflow
    - 자동화된 전체 프로세스이다. 하나의 이상의 Job으로 구성되고, Event에 의해 예약되거나 트리거 될 수 있는 자동화된 절차를 말한다.
    - Workflow 파일은 YAML으로 작성되고, Github Repository의 `.github/workflows` 폴더 아래에 저장된다.
    - Github에서 YAML 파일로 정의한 자동화 동작을 전달하면, Github Actions는 해당 파일을 기반으로 그대로 실행한다.
###### Event
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

###### Github의 배포할 레포지토리에서 settings에 들어갑니다.

    
    ![settings](img/github/settings.jpg)
    

settings → Secrets and variables → Actions → New repository secret

![EC2](img/github/Untitled7.png)

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


![Untitled](Github%20Action%E1%84%8B%E1%85%B3%E1%84%85%E1%85%A9%20CI%20CD%20%E1%84%80%E1%85%AE%E1%84%8E%E1%85%AE%E1%86%A8%20e78c50c996594c57a46311789d992122/Untitled%202.png)

- `gradle.yml` 파일에 코드를 설정 코드를 작성합니다(작성 후 커밋합니다)
    - 현재 코드는 AWS에 로드한 jar 파일을 백그라운드에서 실행시키는 코드도 포함되어 있습니다.

```java
#워크플로의 이름 지정
name: Java CI with Gradle
#워크플로가 시작될 조건 지정
on:
  push: #push 하는 시점마다 job이 실행된다.
    branches: [ "master" ]

jobs:
  build:
    runs-on: ubuntu-latest #해당 job이 어떤 OS에서 실행할지
    permissions:
      contents: read
#job이 가질 수 있는 동작의 나열, 독립적인 프로세스이다
    steps:
#해당 step에서 사용할 액션. {owner}/{repo}@{ref|version} 형태
      - uses: actions/checkout@v4
			#step 이름
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3.1.0

      - name: Grant execute permission for gradlew
      #job에 할당된 컴퓨팅 자원의 shell을 이용하여 커맨드 라인을 실행
        run: chmod +x gradlew

      - name: Build with Gradle Wrapper
        run: ./gradlew clean build

      - name: List build directory
        run: ls -la build/libs

      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        #해당 action에 의해 정의되는 input 파라미터. key/value 페어.
        with: 
          name: build-artifacts
          path: build/libs/*.jar

  dependency-submission:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
      - uses: actions/checkout@v4
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Generate and submit dependency graph
        uses: gradle/actions/dependency-submission@v3.1.0
#CD q부분입니다!
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v3
        with:
          name: build-artifacts

      - name: List artifact directory
        run: ls -la  # 다운로드한 아티팩트의 디렉토리 내용 확인

      - name: Set up SSH key
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.EC2_KEY }}" | tr -d '\r' > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan -H ${{ secrets.EC2_HOST }} >> ~/.ssh/known_hosts

      - name: SCP to EC2
        env:
          EC2_USER: ${{ secrets.EC2_USER }}
          EC2_HOST: ${{ secrets.EC2_HOST }}
        run: |
          scp -i ~/.ssh/id_rsa *.jar $EC2_USER@$EC2_HOST:/home/ubuntu

      - name: RUN java app
        env:
          EC2_USER: ${{ secrets.EC2_USER }}
          EC2_HOST: ${{ secrets.EC2_HOST }}
        run: |
          # PID 파일이 존재하는지 확인
          if ssh -i ~/.ssh/id_rsa $EC2_USER@$EC2_HOST '[ -f /home/ubuntu/pid ]'; then
            echo "PID 파일이 존재합니다. 이전 프로세스를 종료합니다."
            # PID 파일에서 프로세스 ID 읽고 종료
            ssh -i ~/.ssh/id_rsa $EC2_USER@$EC2_HOST 'kill -9 $(< /home/ubuntu/pid)'
          else
            echo "PID 파일이 존재하지 않습니다. 새로운 프로세스를 시작합니다."
          fi
          
          # 새로운 Java 프로세스 시작
          ssh -i ~/.ssh/id_rsa $EC2_USER@$EC2_HOST 'nohup java -jar /home/ubuntu/*.jar >/dev/null 2>&1 & echo $! > /home/ubuntu/pid'
          
          # 프로세스가 잘 시작되었는지 확인
          ssh -i ~/.ssh/id_rsa $EC2_USER@$EC2_HOST 'ps -p $(< /home/ubuntu/pid) && echo "프로세스 시작됨" || echo "프로세스 시작 실패"'

```

```java
#워크플로우 이름정의
name: Java CI with Gradle
#develpo으로 pr시 워크플로우 실행 (배포 test임. 추후 main으로 배포할것)
on:
  pull_request:
    branches: [ develop ]

jobs:
  build:
    #작업(build) 를 ubuntu 환경에서 실행 & 읽기 권한을 가진다.
    runs-on: ubuntu-latest
    permissions:
      contents: read
    #단계 : Github의 checkout 액션을 사용하여 소프코드 체크
    steps:
      - uses: actions/checkout@v4
        #JDK 17을 설정(Temurin 배포판 사용)
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
      #Gradle 설정
      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3.1.0
      #gradlew에 실행 권한 부여
      - name: Grant execute permission for gradlew
        run: chmod +x gradlew
      #Gradle Wrapper를 사용하여 빌드(클린빌드)
      - name: Build with Gradle Wrapper
        run: ./gradlew clean build
      #빌드 디렉토리의 내용을 나열한다
      - name: List build directory
        run: ls -la build/libs
      #빌드된 아티팩트를 업로드한다. (이름: build-artifacts, 경로: build/libs/*.jar)
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-artifacts
          path: build/libs/*.jar
  #작업(dependency-submission): ubuntu-latest 환경에서 실행. 쓰기 권한을 가짐.
  dependency-submission:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    #GitHub의 checkout 액션을 사용하여 소스 코드를 체크아웃
    steps:
      - uses: actions/checkout@v4
      #JDK 17을 설정. (Temurin 배포판을 사용)
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
      #의존성 그래프를 생성하고 제출
      - name: Generate and submit dependency graph
        uses: gradle/actions/dependency-submission@v3.1.0

  #배포(deploy): build 작업이 완료되어야 실행된다. ubuntu-latest 환경에서 실행
  deploy:
    needs: build
    runs-on: ubuntu-latest
    #빌드된 아티팩트를 다운로드함(이름: build-artifacts)
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v3
        with:
          name: build-artifacts
      #아티팩트 디렉토리의 내용을 나열
      - name: List artifact directory
        run: ls -la  # 다운로드한 아티팩트의 디렉토리 내용 확인
      #ssh키 설정
      #~/.ssh 디렉토리를 생성하고, EC2 키를 설정
      #SSH 키에 적절한 권한을 부여하고, 호스트를 known_hosts에 추가
      - name: Set up SSH key
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.CRANE80_EC2_KEY }}" | tr -d '\r' > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan -H ${{ secrets.CRANE80_EC2_HOST }} >> ~/.ssh/known_hosts
      #SCP를 사용하여 빌드된 JAR 파일을 EC2 인스턴스로 전송
      #EC2 사용자와 호스트 정보는 secrets에서 가져옴
      - name: SCP to EC2
        env:
          EC2_USER: ${{ secrets.CRANE_EC2_USER }}
          EC2_HOST: ${{ secrets.CREANE80_EC2_IP }}
        run: |
          scp -i ~/.ssh/id_rsa *.jar $EC2_USER@$EC2_HOST:/home/ubuntu

      - name: RUN java app
        env:
          EC2_USER: ${{ secrets.CRANE_EC2_USER }}
          EC2_HOST: ${{ secrets.CRANE80_EC2_HOST }}
        run: |
          # PID 파일이 존재하는지 확인
          if ssh -i ~/.ssh/id_rsa $EC2_USER@$EC2_HOST '[ -f /home/ubuntu/pid ]'; then
            echo "PID 파일이 존재합니다. 이전 프로세스를 종료합니다."
            # PID 파일에서 프로세스 ID 읽고 종료
            ssh -i ~/.ssh/id_rsa $EC2_USER@$EC2_HOST 'kill -9 $(< /home/ubuntu/pid)'
          else
            echo "PID 파일이 존재하지 않습니다. 새로운 프로세스를 시작합니다."
          fi
          
          # 새로운 Java 프로세스 시작
          ssh -i ~/.ssh/id_rsa $EC2_USER@$EC2_HOST 'nohup java -jar /home/ubuntu/*.jar >/dev/null 2>&1 & echo $! > /home/ubuntu/pid'
          
          # 프로세스가 잘 시작되었는지 확인
          ssh -i ~/.ssh/id_rsa $EC2_USER@$EC2_HOST 'ps -p $(< /home/ubuntu/pid) && echo "프로세스 시작됨" || echo "프로세스 시작 실패"'
```

- `build.gradle`  살짝 수정

jar 파일이 생성될 때 plain 파일과 일반 jar파일이 생성되는데 일반 jar만 생성되도록 하는 설정 변경입니다.

```java
jar{
	enabled = false
}
```

###### 코드 수정 후 git commit & psuh 합니다

###### CI/CD완료
    - 에러가 난다면 yml의 문제일 가능성이 큽니다.

![Untitled](Github%20Action%E1%84%8B%E1%85%B3%E1%84%85%E1%85%A9%20CI%20CD%20%E1%84%80%E1%85%AE%E1%84%8E%E1%85%AE%E1%86%A8%20e78c50c996594c57a46311789d992122/Untitled%203.png)

![Untitled](Github%20Action%E1%84%8B%E1%85%B3%E1%84%85%E1%85%A9%20CI%20CD%20%E1%84%80%E1%85%AE%E1%84%8E%E1%85%AE%E1%86%A8%20e78c50c996594c57a46311789d992122/Untitled%204.png)

- AWS 서버에 접속하면 jar 파일이 올라간 것을 알 수 있습니다

ssh -i [키이름].pem ubuntu@[AWS서버IP]

![Untitled](Github%20Action%E1%84%8B%E1%85%B3%E1%84%85%E1%85%A9%20CI%20CD%20%E1%84%80%E1%85%AE%E1%84%8E%E1%85%AE%E1%86%A8%20e78c50c996594c57a46311789d992122/Untitled%205.png)

![Untitled](Github%20Action%E1%84%8B%E1%85%B3%E1%84%85%E1%85%A9%20CI%20CD%20%E1%84%80%E1%85%AE%E1%84%8E%E1%85%AE%E1%86%A8%20e78c50c996594c57a46311789d992122/Untitled%206.png)

- 로드한 jar 파일을 직접 실행하는 코드입니다

nohup java -jar /home/ubuntu/*.jar?/dev/null 2>&1 &

![Untitled](Github%20Action%E1%84%8B%E1%85%B3%E1%84%85%E1%85%A9%20CI%20CD%20%E1%84%80%E1%85%AE%E1%84%8E%E1%85%AE%E1%86%A8%20e78c50c996594c57a46311789d992122/Untitled%207.png)

<aside>
💥 nohup : 백그라운드에서 실행하는 명령어
& : jar 파일을 실행
jps : 프로세스 중인 파일을 보여줌

</aside>

---

 

## 참조

- CI/CD 구현 시

[Github Action에 대한 소개와 사용법](https://velog.io/@ggong/Github-Action에-대한-소개와-사용법)

- CI/CD 안될때

[Github Actions, Slack 연동하여 Gradle 빌드 결과받기](https://blog.aaronroh.org/112)

- nohub

[[Linux] nohup : 세션이 끝나도 프로세스는 돌아가도록](https://calm-lee.tistory.com/215)

## 다양한 배포 방식 공부하기

- 도커로 배포

https://www.youtube.com/watch?v=XPtu0i3Mjag

- AWS 코드디플로이로 배포

https://velog.io/@vector13/Springboot-프로젝트-Github-Action을-이용해서-배포-자동화하기