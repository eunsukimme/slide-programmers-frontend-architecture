---
# try also 'default' to start simple
# theme: seriph
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
# class: "text-center"
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS
css: unocss
fonts:
  sans: "Noto Sans KR, Roboto"
  serif: "Noto Sans KR, Roboto"
  local: "Noto Sans KR, Roboto"
---

<h1>프로그래머스 프론트엔드<br /> 아키텍처 변천사</h1>

## :좋은 개발 경험을 찾아서

---

# 프로그래머스

개발자 성장 주기에 맞는 교육, 평가, 채용 서비스를 제공하고 있습니다

<img src="/programmers_hompage_old.jpeg" />

---

# 프로그래머스 기술 스택(~2021)

- Ruby on Rails
- Vue(by Webpacker)

<div class="mt-[100px] flex justify-center items-center gap-[40px]">
<!-- Ruby on Rails -->
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/62/Ruby_On_Rails_Logo.svg/1200px-Ruby_On_Rails_Logo.svg.png" class="max-w-[300px]" />
  <!-- Webpack -->
  <img src="https://raw.githubusercontent.com/webpack/media/master/logo/icon.png" class="max-w-[120px]" />
  <!-- Vue -->
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png" class="max-w-[140px]" />
</div>

---

# 프로그래머스 프론트엔드 아키텍처(~2021)

<img src="/programmers-frontend-architecture-old.png" />

---

# 프론트엔드 개발자가 겪는 문제들

- Vue 코드만 수정했는데 배포하는데 너무 오래 걸려요
- 서로 다른 팀이 하나의 package.json을 공유해서 의존성 관리가 힘들어요
- Rails Webpacker에 강한 의존성이 생겼어요

결론적으로,, 개발 경험이 안좋아요😭

---

# 프론트엔드 개발자가 원하는 것

- 🚀 빠르게 개발하고 배포하는 것
- 🪄 의존성 관리도 팀별로 알아서 척척
- ⚛️ Vue말고 React도 쓰고싶다!

---
layout: center
---

# 그래서 우리는 프론트엔드 아키텍처를 개선하기로 했습니다

---

# New 프론트엔드 아키텍처 설계

- ✅ Do
  - 프론트엔드를 Rails 애플리케이션과 독립적으로 개발/빌드/배포 가능
  - 프론트엔드 코드베이스는 모노레포로 관리
    - 전사 공통 패키지 외에는 팀에서 자유롭게 의존성 도입
  - 기존 아키텍처와 새로운 아키텍처가 공존하고, 점진적(Progressive)으로 마이그레이션 가능

- ❌ Don't
  - 아키텍처를 바꾸느라 제품 개발 프로세스가 멈추면 안됨

--- 

# 선행 조사

프로그래머스가 처한 상황과 비슷한 사례가 있을까?

- Airbnb 사례 - [Hypernova](https://github.com/airbnb/hypernova)

- 오늘의집 사레 - [오늘의집 MSA Phase 1. 프론트엔드 분리작업](https://www.bucketplace.com/post/2021-12-03-%EC%98%A4%EB%8A%98%EC%9D%98%EC%A7%91-msa-phase-1-%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C-%EB%B6%84%EB%A6%AC%EC%9E%91%EC%97%85/)


---
layout: two-cols
---

# Airbnb 사례: Hypernova

> A service for server-side rendering your JavaScript views

Server

```js {all|5-10}
var hypernova = require('hypernova/server');

hypernova({
  devMode: true,
  getComponent(name) {
    if (name === 'MyComponent.js') {
      return require('./app/assets/javascripts/MyComponent.js');
    }
    return null;
  },
  port: 3030,
});
```

::right::

<br />
<br />
<br />


<div class="pl-[40px]">
<p class="pt-[10px]">MyComponent.js</p>

```js
const React = require('react');
const renderReact = require('hypernova-react').renderReact;

function MyComponent(props) {
  return <div>Hello, {props.name}!</div>;
}

module.exports = renderReact('MyComponent.js', MyComponent);
```
</div>

---

# Airbnb 사례: Hypernova(Key Points)

1. 클라이언트 코드가 서버 코드로부터 분리될 수 있음
2. 점진적으로 적용 가능함

실제로 구현된 사례를 확인함으로써 아키텍처 설계에 대한 힌트를 얻게 되었다!

---

