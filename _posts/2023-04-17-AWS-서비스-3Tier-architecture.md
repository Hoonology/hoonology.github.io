---
date: 2023-04-16 18:00:00
layout: post
title: AWS 서비스 - 3 Tier Architecture 배포
subtitle: AWS 서비스 VPC에 대하여
description: Storage - 클라우드 컴퓨팅 제공업체를 통해 데이터와 파일을 인터넷에 저장할 수 있는 클라우드 컴퓨팅 모델
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681432465/dev-jeans_%E1%84%87%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB_y5n0eh.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681432465/dev-jeans_%E1%84%87%E1%85%A9%E1%86%A8%E1%84%89%E1%85%A1%E1%84%87%E1%85%A9%E1%86%AB_y5n0eh.png 
category: AWS
tags:
  - AWS
  - cloud
  - EC2
  - 인스턴스
  - 3티어아키텍처
  
author: Hoonology
comments: true

---

앞선 슬라이드를 통해 S3, EC2, RDS에 대한 개념 학습을 완료했습니다. 이번 스프린트에서는 코드 작성이 완료된 애플리케이션을 AWS 서비스를 통해 배포하는 스프린트를 진행하겠습니다.

## Bare Minimum Requirement
- Sprint Repository의 소스코드를 이용하여, 어떤 구조로 구성되어 있는지 확인합니다.
- HTTP 스프린트의 테스트를 모두 통과해야 합니다.
- 웹 애플리케이션이 배포 상태에서 잘 작동해야 합니다.

## Getting Started
- EC2, S3, RDS와 Repository의 소스코드를 가지고 웹 애플리케이션을 배포하기 위해 어떤 아키텍처를 가져야하는지 이해해야 합니다.
  - 아키텍처에 따라 어떤 과정을 먼저 진행해야 할지 확인합니다.
- 먼저 각 ```client``` , ```server``` 디렉토리에서 dependencies를 ```npm install``` 을 통해 설치합니다.
- 클라이언트와 서버 디렉토리에 각각 위치한 ```.env.example``` 파일을 보며 어떤 환경변수들이 정의되어 있는지 확인합니다.
![1](/assets/img/AWS/1.png)
![2](/assets/img/AWS/2.png)


### 클라이언트 배포 (S3)
- S3 콘솔을 통해 버킷을 생성해야 합니다.
  - [AWS 공식문서 설명](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step1-create-bucket-config-as-website)
  ![버킷](/assets/img/AWS/%EB%B2%84%ED%82%B7%EB%A7%8C%EB%93%A4%EA%B8%B0.png)

- 클라이언트 파일을 빌드하고 결과물을 버킷에 업로드 해야 합니다.
  - 클라이언트 디렉토리 안에서 아래 코드 입력 
    ```bash
      npm run build
      ```
    ![runbuild](/assets/img/AWS/run%20build.png)
  - build 디렉토리가 생긴다.

- 정적 웹 호스팅 기능을 이용하여 클라이언트 코드를 배포해야 합니다.
  - [ S3 - 버킷 ] 탭 접근  
  ![버킷2](/assets/img/AWS/%EB%B2%84%ED%82%B72.png)
  - [ 업로드 ] 클릭 후 build 디렉토리를 옮긴다.
    ![업로드](/assets/img/AWS/%EC%97%85%EB%A1%9C%EB%93%9C.png)
- 클라이언트 배포가 완료되면 다음과 같은 화면이 출력됩니다.
  - [ 버킷 - 속성 ] 탭 접근 후 맨 아래로 이동, [ 버킷 웹 사이트 엔드포인트 ]의 밑에 주소 클릭하면 아래와 같이 나온다.
    ![업로드2](/assets/img/AWS/%EC%A0%95%EC%A0%81%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8%ED%98%B8%EC%8A%A4%ED%8C%85.png)

    ![김코딩](/assets/img/AWS/%EA%B9%80%EC%BD%94%EB%94%A9.png)
    [사진] 결과
- S3를 이용한 정적 웹 호스팅을 하기 위한 [레퍼런스](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html)

- ```.env.example``` 파일을 ```.env``` 파일로 생성하여 ```REACT_APP_API_URL```에 EC2에 배포한 서버 주소로 설정합니다.
- S3를 통해 ```client``` 디렉토리에 있는 소스코드를 먼저 정적 웹 호스팅 방식으로 배포합니다.
- ```.env``` 파일의 테스트에 필요한 환경변수를 채워넣습니다.
- ```client``` 디렉토리에서 ```npm run test1``` 명령을 사용해 테스트를 전부 통과하는지 확인하고, 웹 애플리케이션이 정상적으로 배포되어 작동하는지 확인합니다.
- 테스트가 모두 통과되면 제출하고, 다음 HTTPS 스프린트로 넘어갑니다.

### 서버 배포 ( EC2 )
- EC2 콘솔을 통해 EC2 인스턴스를 생성한다.
  ![인스턴스시작](/assets/img/AWS/%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4%EC%8B%9C%EC%9E%91.png)
    - 이름 설정 - OS 설정(Ubuntu - arm 아키텍처 선택) - pem 키 생성
  - EC2 인스턴스 생성 후 [SSH 프로토콜을 통해 인스턴스에 접속](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html#AccessingInstancesLinuxSSHClient)합니다.
    - SSH 클라이언트를 사용하여 Linux 인스턴스에 연결하려면 다음 프로시저를 사용하세요. 
      - 터미널 창에서 ssh 명령을 사용하여 인스턴스에 연결합니다. 프라이빗 키(.pem)의 경로와 파일 이름, 인스턴스의 사용자 이름 및 인스턴스의 퍼블릭 DNS 이름 또는 IPv6 주소를 지정합니다. 
      - (퍼블릭 DNS) 인스턴스의 퍼블릭 DNS 이름을 사용하여 연결하려면 다음 명령을 입력합니다.  
      ```ssh -i /path/key-pair-name.pem instance-user-name@instance-public-dns-name```  
      =  ```ssh -i /path/key-pair-name.pem ec2-user@<인스턴스 ID>```
      ```bash
      ssh -i "devops04-ec2.pem" ubuntu@ec2-3-27-74-212.ap-southeast-2.compute.amazonaws.com
      ```


  - SSH 프로토콜 사용시 ```.pem```파일의 권한을 확인하고, 적절하게 권한을 부여해야 합니다. ( .pem이 있는 폴더에서 실행해야함 )
    ```bash
    # 권한 부여
    chmod 400 devops04-ec2.pem
    ssh -i "devops04-ec2.pem" ubuntu@ec2-3-27-74-212.ap-southeast-2.compute.amazonaws.com
    ```

    ![ssh](/assets/img/AWS/ssh2.png)

  - 인스턴스에 접속한 후, 필요한 개발 환경(git, npm, node) 등을 구축합니다.  
  
    치트키
      ```bash
      sudo apt-get update
      curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
      export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
      [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
      nvm install --lts
      # sleep 5
      sudo apt-get install npm -y
      ```




  - EC2에 브라우저를 통해 접근하려면 어떤 주소로 접근해야 하나요?
  - client 폴더의 ```.env``` 환경설정의 ```REACT_APP_API_URL``` 과 EC2의 주소는 어떤 관계가 있나요?
- 간단한 서버 애플리케이션을 생성하고 EC2 인스턴스에 코드를 가져와야 합니다.
- 소스코드와 테스트 코드를 참고하여, 서버 코드가 어떻게 구성되어 있는지 확인합니다.
- 서버를 실행시키고 브라우저에서 서버에 접속할 수 있어야 합니다.
  - 애플리케이션 레이어에서 어떤 프로토콜을 사용하고 있나요?
  - 터미널을 종료해도 서버가 계속 돌아가게 하려면 어떻게 해야하나요?
  - ```listen EACCES: permission denied 0.0.0.0:80``` 에러메세지를 만났다면, 1024번 이하의 포트에 대해서 학습하세요.
  - 보안그룹은 어떤 역할을 하고, 어떻게 설정해야 하나요?
- 브라우저에서 서버에 접속하면 다음과 같이 출력됩니다.
  ![hello](/assets/img/AWS/helloworld.png)