---
title : "GitHub 블로그를 검색 엔진에 등록하기"
author: potato
date : 2025-01-14 09:00:00 +0900
categories : [Guide, blog]
tags: [github, blog, guide]
image:
  path: https://github.com/user-attachments/assets/3dcfa13f-9376-47c6-bb35-6e6bd8606311
description: GitHub 블로그 검색창 노출시키기
---

## 검색 엔진 등록이란?

내가 작성한 포스팅들을 구글이나 네이버 등과 같은 검색 엔진에 노출시키도록 해주는 방법입니다.   

저는 구글에만 등록할 것이기 때문에 **Google search Console** 등록 방법에 대해 설명드리겠습니다.
<br/>

***
## Google Search Console
[바로가기](https://search.google.com/search-console/about)   

![스크린샷 2025-01-14 094351](https://github.com/user-attachments/assets/9b929324-f98e-4bf3-b8d6-8721f954e9d3)

시작하기 버튼을 누르면 다음 화면이 표시될 겁니다.

![스크린샷 2025-01-14 094452](https://github.com/user-attachments/assets/a2929d71-b296-4ea5-a36b-bb3e9e4f4bbb)

도메인을 구입하신 분들은 왼쪽으로 진행하시면 되는데, 저는 도메인을 구입하지 않았고 Github가 제공하는 url을 그대로 사용할 것이기 때문에 오른쪽으로 진행합니다.

![image](https://github.com/user-attachments/assets/b41b4b1f-fe33-4cc3-88fc-f283370d1ac2)

내 깃 블로그 주소를 적고 '계속' 을 클릭합니다.

![image](https://github.com/user-attachments/assets/16742bb1-bffe-4aa3-9f6e-33fdf139a711)

순순히 권장 확인 방법을 사용할 겁니다. 1번의 html 파일을 다운 받아 줍니다.
<br>

***
## HTML 파일 추가
다운 받은 html 파일은 우리의 github.io 파일 안의 `_config.yml`이 위치한 곳에 같이 넣어주면 됩니다.   

![image](https://github.com/user-attachments/assets/46b8d4d6-7b31-40cb-8983-219c3673d8c7)

레포지토리에 올리기 전에 로컬 서버로 체크해봐도 되는데, 굳이 안 해도 큰 문제는 없다고 합니다.   

그대로 깃허브에 올려준 뒤 좀 기다려줍니다. (바로 누르면 소유권 확인 실패가 뜹니다...)   

지금인가? 싶을 때 확인 눌러줍니다.   

![image](https://github.com/user-attachments/assets/b2fccb6e-ef01-4852-8a04-4b546d87d1ff)

소유권이 확인되었다네요.
<br/>

***
## sitemap.xml 추가
HTML 파일만 세팅해준다면, 검색의 url 정보를 크롤링 할 수가 없습니다.   

따라서 이러한 작업을 할 수 있도록, `sitemap.xml`을 설정해줄 겁니다.   

아까 HTML 파일을 넣었던 위치에 똑같이 `sitemap.xml` 파일을 만들어줍니다. 그리고 아래의 코드를 복붙해주면 됩니다.   
이건 `sitemap.xml`을 이용하여 google 크롤러가 url을 체크할 수 있게 해주는 코드가 됩니다.

```xml
---
layout: null
---

<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd"
        xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
    {% raw %}{% for post in site.posts %}{% endraw %}
    <url>
        <loc>{% raw %}{{ site.url }}{{ post.url }}{% endraw %}</loc>
        {% raw %}{% if post.lastmod == null %}{% endraw %}
        <lastmod>{% raw %}{{ post.date | date_to_xmlschema }}{% endraw %}</lastmod>
        {% raw %}{% else %}{% endraw %}
        <lastmod>{% raw %}{{ post.lastmod | date_to_xmlschema }}{% endraw %}</lastmod>
        {% raw %}{% endif %}{% endraw %}

        {% raw %}{% if post.sitemap.changefreq == null %}{% endraw %}
        <changefreq>weekly</changefreq>
        {% raw %}{% else %}{% endraw %}
        <changefreq>{% raw %}{{ post.sitemap.changefreq }}{% endraw %}</changefreq>
        {% raw %}{% endif %}{% endraw %}

        {% raw %}{% if post.sitemap.priority == null %}{% endraw %}
        <priority>0.5</priority>
        {% raw %}{% else %}{% endraw %}
        <priority>{% raw %}{{ post.sitemap.priority }}{% endraw %}</priority>
        {% raw %}{% endif %}{% endraw %}

    </url>
    {% raw %}{% endfor %}{% endraw %}
</urlset>
```
<br/>

***
## robots.txt 추가
```
User-agent: *
Allow: /

Sitemap: https://oding01.github.io/sitemap.xml  // 본인 사이트 적어줘야 합니다.
```

똑같이 `_config.yml` 파일이 있는 곳에 `robots.txt` 파일을 만들고 위 코드를 작성해줍니다.   

이제 크롤러가 접근해서 robots.txt를 확인하고, 접근하고 싶은 sitemap을 확인하는 방식입니다.   

Allow에 본인이 원하시는 정보만 입력하거나 제한을 두고싶으신 내용을 입력하면 크롤러가 확인해서 진행해줍니다.   

다 하셨으면 저장소에 푸쉬해줍시다!
<br/>

***
## Sitemap 제출
이제 마지막입니다!   

Google Search Console로 다시 이동하셔서,   

![image](https://github.com/user-attachments/assets/58a1e9cd-c5d0-40d9-9371-a5b7e6c0b45d)

이렇게 내 사이트 속성을 찾아서 클릭합니다. Sitemaps 탭을 찾고, 새 사이트맵 추가에 아래와 같이 sitemap.xml을 입력해줍니다.

![image](https://github.com/user-attachments/assets/1d003930-4755-4ced-93ee-762979974266)

이제 무한한 기다림의 시간이랍니다. 사람마다 걸리는 시간이 다 달라서... 인내심을 가지고 기다려봅시다.

<br />
