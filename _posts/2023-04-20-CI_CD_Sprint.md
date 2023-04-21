---
date: 2023-04-20 03:00:30
layout: post
title: (Sprint) Github Actions를 이용한 빌드 및 테스트 자동화
subtitle: Github Actions에 대하여
description: 유닛 테스트 통과 -> Node.js CI 적용
image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png
optimized_image: https://res.cloudinary.com/dvqcvocet/image/upload/v1681954803/eoe0iiqoeiq9ghldrltc.png 
category: CI/CD
tags:
  - Github Action
  
author: Hoonology
comments: true

---


# Github Actions
[출처](https://www.daleseo.com/github-actions-basics/)  
GitHub Actions는 코드 저장소(repository)로 유명한 GitHub에서 제공하는 CI(Continuous Integration, 지속 통합)와 CD(Continuous Deployment, 지속 배포)를 위한 비교적 최근에 추가된 서비스입니다. 당연히 GitHub에서 코드를 관리하고 있는 소프트웨어 프로젝트에서 사용할 수 있으며 개인은 누구나 GitHub에서 코드 저장소를 무료로 만들 수 있기 때문에 다른 CI/CD 서비스 대비 진입장벽이 낮은 편입니다.

## Workflows
GitHub Actions에서 가장 상위 개념인 워크플로우(Workflow, 작업 흐름)는 쉽게 말해 자동화해놓은 작업 과정이라고 볼 수 있습니다. 워크플로우는 코드 저장소 내에서 .github/workflows 폴더 아래에 위치한 YAML 파일로 설정하며, 하나의 코드 저장소에는 여러 개의 워크플로우, 즉 여러 개의 YAML 파일을 생성할 수 있습니다.

이 워크플로우 YAML 파일에는 크게 2가지를 정의해야하는데요. 첫번째는 on 속성을 통해서 해당 워크플로우가 언제 실행되는지를 정의합니다.  


**Workflow**란 레포지토리에 추가할 수 있는 일련의 자동화된 커맨드 집합입니다.

하나 이상의 Job으로 구성되어 있으며, Push나 PR과 같은 이벤트에 의해 실행될 수도 있고 특정 시간대에 실행될 수도 있습니다.

빌드, 테스트, 배포 등 각각의 역할에 맞는 Workflow를 추가할 수 있고, ```.github/workflows``` 디렉토리에 **YAML** 형태로 저장합니다.



```GitHub Actions```를 사용하면 자동으로 코드 저장소에서 어떤 이벤트(event)가 발생했을 때 특정 작업이 일어나게 하거나 주기적으로 어떤 작업들을 반복해서 실행시킬 수도 있습니다.   
예를 들어, 누군가가 코드 저장소에 **Pull Request**를 생성하게 되면 GitHub Actions를 통해 해당 **코드 변경분에 문제가 없는지 각종 검사**를 진행할 수 있고요. 어떤 새로운 코드가 메인(main) 브랜치에 유입(push)되면 GitHub Actions를 통해 소프트웨어를 **빌드(build)하고 상용 서버에 배포(deploy)할 수**도 있습니다. 뿐만 아니라 매일 밤 특정 시각에 그날 하루에 대한 통계 데이터를 수집시킬 수도 있습니다.



# 1. 유닛 테스트 통과
테스트 주도 개발(**TDD**)을 연습 - ```test/app.test.js```를 수정하여 통과하지 않는 테스트를 모두 통과

1. 애플리케이션은 node.js로 작성되어 있습니다. node.js LTS 버전을 준비합니다.
2. 먼저 애플리케이션의 의존성(dependency)을 설치해야 합니다. ```npm install``` 명령을 이용해 의존성을 설치합니다.
3. 테스트가 통과하는지 확인하려면 ```npm test``` 명령을 이용합니다. 다음과 같이 테스트가 통과하지 않는 것을 먼저 확인하세요.
4. ```test/app.test.js``` 파일을 열어 통과하지 않는 테스트를 수정하세요. ```FILL_ME_IN```이라고 적힌 곳에 기댓값을 적어주면 됩니다.

![npmtest](/assets/img/CICD/npmtest.png)

# 2. GitHub Action을 이용해서 Node.js CI를 적용하세요.
- node 버전은 16 버전으로 반드시 지정해야 합니다.
- 다음 상황에서 GitHub Action이 트리거되어야 합니다.
  - master로 push 했을 경우
  - pull request를 보낸 경우
- Pull Request로 제출하세요.

## Getting Started
- node.js 프로그램의 테스트를 위해서는 npm test 명령어를 CI, 즉 GitHub Action 상에서 자동으로 실행해줘야 합니다.
- 먼저 공식 문서를 통해 GitHub Action의 사용방법을 알아봅시다.
  - [Creating a starter workflow](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization)
  - [Using starter workflow](https://docs.github.com/en/actions/using-workflows/using-starter-workflows)
- ```Actions``` 탭을 클릭하여 workflow가 제대로 작동했는지를 확인해 봅시다.
- 레퍼런스를 참고하여 GitHub Action Node.js workflow를 만들고, 테스트가 통과하는지 확인하세요.

## Workflow

1. ```.github/workflows```폴더를 생성 후 ```ci.yaml``` 생성
2. 아래 내용 입력을 ```ci.yaml```에 입력

![src](/assets/img/CICD/src.png)

3. ```Github Actions```에 ```add``` - ```commit``` - ```push```


4. Actions 확인

![src](/assets/img/CICD/src2.png)

5. build 확인  
![src](/assets/img/CICD/src3.png)

6. 이제 누군가가 master branch에 Pull Request 를 하게 되면 내가 설정해놓은 것을 기반으로 merge를 할 것인지 말 것인지 알아서 판단하게 된다.

- test/devops 브랜치를 만든 뒤 연결하고 ci.yaml에 내용을 조금 추가해본다.


```bash
git switch -c test/devops
```

add - commit - push를 한다.

- add check node version process 라는 이름으로 커밋을 한 것의 Pull Request가 전달됐다.  

![commit](/assets/img/CICD/branch.png)


  그대로 Merge pull request를 눌러본다.
  > 아래와 같은 문구가 나온다는 것은  
  내가 설정해놓은 값에 이상이 없어 받아들여도 된다는 의미로 해석된다.


![merge](/assets/img/CICD/merge0.png)

Merge pull request를 누른다.

![merge](/assets/img/CICD/merge.png)


성공적으로 commit 완료 ! 

![merge](/assets/img/CICD/commit.png)
