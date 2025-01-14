---
title : "GitHub 블로그를 생성해보자! - Chirpy 테마"
author: potato
date : 2025-01-11 09:00:00 +0900
categories : [Guide, blog]
tags: [github, blog, guide]
image:
  path: https://github.com/user-attachments/assets/2a4f2314-2597-402f-a831-b5f63dbb0d8b
description: Chirpy 테마로 GitHub 블로그 만들기
---

> 1. 이 글은 Github 아이디가 존재하고, **USERNAME.github.io** 레포지토리가 생성되어 있다는 가정하에 진행합니다.
2. 저는 Window에서 VSCode에 WSL을 연결하여 Ubuntu Linux 환경에서 진행합니다.
{: .prompt-danger }

## Velog도, Tistory도 있는데 왜 깃허브로?
1. **무료 호스팅**
- 별도의 서버 비용이 들지 않음
- 도메인도 github.io 무료로 제공

2. **버전 관리 용이**
- Git을 통한 모든 변경사항 추적 가능
- 실수로 게시물을 삭제해도 복구 가능

3. **높은 자유도**
- Jekyll 테마를 통한 디자인 커스터마이징
- HTML, CSS, JavaScript로 원하는 기능 추가 가능

4. **개발자 친화적**
- 코드 공유가 쉬움
- 기술 블로그로 적합
- **포트폴리오로도 활용 가능**

5. **SEO 최적화**
- 정적 사이트로 검색엔진 최적화에 유리
- 빠른 로딩 속도
<br/>



****
## 1. 시작하기
### 1.1. 레포지토리 Clone
일단 본인의 로컬 폴더에 만들어둔 **USERNAME.github.io** 레포지토리를 **Clone**합니다.

![cloneroot](https://github.com/user-attachments/assets/3b9b130c-54a0-4f1f-97aa-9b06bc42c3ae)

```bash
git clone https://github.com/USERNAME/USERNAME.github.io.git
```

클론을 실행한 경로로 가서 clone이 잘 되었는지 확인합니다.

![cloneConfirm](https://github.com/user-attachments/assets/91b3d82d-c557-4c97-9b10-b4288ab2f403)

잘 되었네요.

### 1.2. 테마 다운로드
이제 테마를 다운받을 시간입니다.   
> [레포지토리 다운로드](http://jekyllthemes.org/)   
[테마 다운로드 페이지](http://jekyllthemes.org/)

접속해서 마음에 드는 테마를 고릅니다. 해당 테마의 레포지토리로 이동한 후,   
여기선 선택지가 2개가 있습니다.

1. 레포지토리 fork
2. 레포지토리 Download Zip

> 만약 1번. fork 했다면, 아마 master 브랜치라고 되어 있을텐데 바꿔줍니다. 저장소 이름도 `USERNAME.github.io`로 바꿔줘야 합니다.   
![스크린샷 2025-01-12 032136](https://github.com/user-attachments/assets/3fe3502a-aac5-49d4-9cfe-0726903e4f47)
![스크린샷 2025-01-12 032157](https://github.com/user-attachments/assets/d3dec64a-2969-4f0f-aa63-a607790616d1)

1번은 간단하지만, 이게 블로그 글 작성 후 commit을 하게 되면 잔디가 심어지지 않는다하더라구요...   
이미 gihub.io 레포지토리가 있기도 하고 그래서 전 2번으로 갔습니다.   


![cloneroot](https://github.com/user-attachments/assets/3b9b130c-54a0-4f1f-97aa-9b06bc42c3ae)

레포지토리를 다운 받고 압축을 풀어준 뒤, 안에 있는 파일을 모두 내 github.io 폴더에 복붙해줍니다.

![스크린샷 2025-01-11 230435](https://github.com/user-attachments/assets/a71483e0-952c-4b4b-a0ab-6d348f2b84da)

~~_파일이 더럽게 많습니다._~~   
<br/>

****
## 2. Ruby 설치하기
jekyll을 사용하려면 Ruby를 설치해야 합니다.   
> MAC의 경우에는 기본적으로 2.x버전의 ruby가 설치되어 있다고 하는데, jekyll을 사용하려면 3버전 이상의 ruby를 설치해야 합니다.
윈도우는 [루비 다운로드](https://rubyinstaller.org/) 에 가서 다운로드 해주세요.
{: .prompt-warning }

여기서 아래의 명령어로 ruby의 버전 리스트를 확인합니다. (윈도우는 rbenv가 아닌 ruby 명령어로 ruby prompt를 실행시켜 [3. Jekyll 설치하기](#3-jekyll-설치하기)로 바로 넘어가주세요.)
```bash
rbenv install -l
```

3버전 이상을 설치해야 하는데, 저는 3.2.6을 설치해주었습니다.
```bash
rbenv install 3.2.6
rbenv global 3.2.6
rbenv rehash
```
<br/>





****
## 3. Jekyll 설치하기
ruby가 정상적으로 설치되었다면, 이제 `gem` 명령어를 사용할 수 있습니다.   
gem으로 jekyll을 설치해줍시다.
```bash
gem install jekyll
```
설치되었다면 아래 명령어로 bundle을 설치해줍니다.
```bash
gem install bundler
bundle install
```
> webirck 관련 에러가 난다면
`gem install webrick`

> node.js 모듈을 설치하지 않으면 `assets/js/dist/*.min.js Not Found` 에러 발생과 함께 블로그 기능이 정상적으로 동작하지 않습니다.(css가 적용되지 않는다는 등)
{: .prompt-warning}

따라서 npm을 통해 node.js 모듈을 설치해줍니다.
```bash
npm install
npm run build
```

이제 거의 다 왔습니다! 로컬에서 내 블로그를 한 번 봅시다.
```bash
bundle exec jekyll serve
혹은
jekyll serve
```
![스크린샷 2025-01-11 235625](https://github.com/user-attachments/assets/3daceaa1-4e28-4dff-bd0a-0f782bf89759)

홈페이지는 브라우저 창에 `http://127.0.0.1:4000/`을 입력하거나, 터미널 창의 `Server address : http://127.0.0.1:4000/` 주소를 `ctrl + 클릭` 하면 접속할 수 있습니다.   

> 만약 실행 시에, ```
Deprecation: You appear to have pagination turned on, but you haven't included the `오류 발생 플러그인` gem. Ensure you have `plugins: [오류 발생 플러그인]` in your configuration file. ```등의 오류가 난다면   
> 1. `Gemfile`에 오류가 발생한 플러그인을 추가합니다.
```
gem '오류 발생 플러그인'
```
> 2. `_config.yml`에 추가한 플러그인을 작성해줍니다.
```yml
plugins:
  - 오류 발생 플러그인
```
> 3. `bundle install` 로 번들을 다시 설치한 후 서버를 실행해주세요.
{: .prompt-danger}

![스크린샷 2025-01-12 000303](https://github.com/user-attachments/assets/e5b5083f-43ee-4d00-9af3-96d2f5db77bb)
_오류 없이 잘 실행됐다면 볼 수 있는 화면_

<br />

## 번외. 초기화
테마의 글 등 초기화를 진행하고 싶으신분
```
bash tools/init.sh
```


****

## 4. _config.yml 파일 수정해주기
지금이야 로컬에서 `http://127.0.0.1:4000/` 로 돌아가고 있다고는 하지만, Github에서는 다른 사람들처럼 `USERNAME.github.io` 로 연결돼야 하지 않겠습니까?   
`_config.yml` 파일을 찾아서 들어가줍니다.

저는 주석도 지우고 많은 걸 변경해줬지만, 이와 비슷한 코드를 볼 수 있을 겁니다.
```yml
theme: jekyll-theme-chirpy  

baseurl: ''   # 사용자 페이지를 만들었을 경우, 빈칸으로 둡니다. 프로젝트 페이지를 만든 경우 프로젝트 명을 적어줍니다.

lang: en    # 사용하는 언어 설정을 진행합니다. http://www.lingoes.net/en/translator/langcode.htm 로 접속하여 확인가능합니다.

timezone: Asia/Seoul    # timezone 설정정

title: 코딩하는 감자    # 블로그 이름입니다. 상단 브라우저 창에도 반영됩니다.

tagline: 우당탕탕 개발일지    # title 밑에 표시되는 tagline입니다.

description: >-   # seo를 위한 것들입니다. (내 블로그를 어떤 검색어로 들어올지)
  github, vscode, javascript, react,
  깃허브 블로그, Github 블로그

url: "https://oding01.github.io"    # 내 Git 레포지토리 주소입니다. 꼭 적읍시다.

github:
  username: oding01   # 본인 github username

# twitter:
#   username: ""    # 저는 트위터가 없어서 주석처리했습니다.

social:
  name: 이어진
  email: slimin92@naver.com
  links:
    - https://github.com/oding01    # 다른 social이 없어서 github만 적어주었습니다.

theme_mode: # [light | dark] 비워두면 다른 사용자의 default 설정에 따라 바뀝니다.

# cdn: "https://chirpy-img.netlify.app"   avatar가 바뀌지 않아 주석처리 해줬더니 해결했습니다.

avatar: "/assets/img/profile.png"    # title 위에 대표이미지입니다.

toc: true   # 포스팅 글 옆의 목차를 자동으로 생성해주는 애입니다.
            # 모든 포스팅에서 목차 생성을 하지 않겠다하면 false로 바꾸면 됩니다.

paginate: 10
...
```
<br/>




****
## 5. GitHub에 배포하기
### 5.4. 배포 전 (트러블 슈팅)
#### 🔒 문제
_로컬에서 잘 작동하니까 이제 레포지토리에 배포만 하면 되겠지?_   

다 끝난 줄 알고 냅다 푸쉬했습니다.

![image](https://github.com/user-attachments/assets/610fc101-eac3-41fd-a698-ef7b49bcfa78)
_commit 내역_

![스크린샷 2025-01-12 001607](https://github.com/user-attachments/assets/335d936c-313d-4073-a892-9858699ea871)
_Action 탭 내역_

날 반겨주는 무수한 오류들... 반갑다 ㅋ... ~~여기선 4시간 썼습니다. 진짜 개어렵네~~   

수시간의 구글링이 시작됐습니다.   

1. `_config.yml`에서 `theme` 주석처리   
어떤 블로그에서,
> " 원래는 theme을 주석처리 하지 않고 사용하고 있었는데 이제는 주석처리하고 theme 이던 remote_theme 이던 둘다 사용하지 않아야 한다고 한다.   
이유는.. 무슨 서버를 이제 사용하지 않는다고? 하는거 같은데.."   
라고 해서 바로 주석처리했더니 여전히 오류가 났습니다. (2021년 글이었음)

2. 제가 파일들 전체 복붙을 안 했더군요. 없는 파일들이 있었습니다.   
➡️ 추가해주어서 build 및 deploy 오류는 해결됐지만 이 친구가 절 반겨줬습니다.
![image](https://github.com/user-attachments/assets/250e191f-c86f-4c7c-ab79-e30524057866)
_홈페이지 접속_


#### 🗝️ 해결
~~got you~~~ 또 수시간의 구글링으로 답을 찾았습니다.  

> Linux 외 환경은 모두 아래 명령어 실행 후 진행해주세요.
```console
bundle lock --add-platform x86_64-linux
```
{: .prompt-warning}

1. 배포 전 아래와 같이 Settings - Pages - Build and deployment 에서 소스를 GitHub Actions로 변경합니다.   
![image](https://github.com/user-attachments/assets/5cbb5530-c611-44f9-92fd-6f2eb131f49e)

2. Configure를 선택합니다.   
![image](https://github.com/user-attachments/assets/040913b7-1c11-4df1-9fc7-442dd169dd2f)

3. Commit changes...를 선택 후, Commit changes를 클릭합니다.   

> 본인이 설치한 버전과 맞는지 확인해주세요!   
저는 가상 환경이고 ruby도 3.2.6을 깔아서 변경 후 진행했습니다.
```yml
jobs:
  # Build job
  build:
    runs-on: ubuntu-22.04   # 변경 부분
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Ruby
        uses: ruby/setup-ruby@8575951200e472d5f2d95c625da0c7bec8217c42 # v1.161.0
        with:
          ruby-version: "3.2"   # 변경 부분
          bundler-cache: true
          cache-version: 0
      - name: Setup Pages
  ...
  ...
  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-22.04   # 변경 부분
    needs: build
```
{: .prompt-danger }


![image](https://github.com/user-attachments/assets/f955d17a-4488-4452-a9f2-9a1bebb5a56b)
![스크린샷 2025-01-12 005830](https://github.com/user-attachments/assets/4bed8c5b-5bc2-4993-a740-e9d3d8987027)

4. Github에서 jekyll.yml을 생성했으니 로컬과 동기화를 해줍니다.   
```bash
git pull
```

5. 그 외

> 구글링하며 하라고 권장했던 동작들입니다. 전 문제 없이 동작해서 해주지 않았지만, 오류가 난다면 시도해보세요.
{: .prompt-tip }

- `.gihub > workflow` 디렉토리 내에서 기존 배포 방식(Deploy form a branch)에 사용되던 파일을 삭제합니다.
- `.gitignore` 내 `assets/js/dist` 디렉토리 내 파일들의 Push가 무시되도록하는 설정을 주석처리 합니다.

### 5.5. 해치웠나?
이제 레포지토리에 배포해봅시다 !   

```bash
git add . 
git commit -m "first commit"
git push origin main
```

> 저는 브랜치 이름을 main이라고 rename 해주었습니다. master로 되어있다면,   
`git branch -m main`으로 리네임해서 사용합시다.
{: .prompt-tip }

🎉🎉 Action 탭에서 워크 플로우가 정상적으로 동작하고, 블로그 기능이 정상적으로 실행되는 것을 볼 수 있습니다!!

![image](https://github.com/user-attachments/assets/9d3eed06-c2a8-404a-92e7-316bf206970d)
![image](https://github.com/user-attachments/assets/326f1a7e-21b8-48fe-979b-2dd077ae8436)

<br />



****

수고하셨습니다 ! 이제 본인만의 깃허브 블로그를 생성했습니다.   
벨로그 글 옮길 생각하면 어지럽네요... 그래도 열심히 해야죠...    

Github 블로그는 잘 노출되게 해주려면 검색엔진 등록 작업을 해야합니다.   

다음 글은 블로그를 검색엔진에 등록하는 방법을 올려보겠습니다.   
<br/>

➕ 추가   
VSCode Extension에서 Dev Containers를 깔고

![image](https://github.com/user-attachments/assets/296a266b-a19c-43d1-808b-4656777000d7)

컨테이너에서 다시 열기를 클릭하면 Docker 환경에서 작성할 수 있는 것 같습니다.   
의존성 등 여러 환경이 자동으로 설치되어 동작합니다.   
Chirpy 개발자도 권장하는 사항이긴 했는데 시도해보세요.

<br />
[chirpy README.md](https://github.com/cotes2020/jekyll-theme-chirpy)   
[window chirpy 테마 적용](https://a3magic3pocket.github.io/posts/jekyll-theme-chirpy-with-gem/)   
[Mac OS chirpy 테마 적용](https://jinozblog.tistory.com/126)