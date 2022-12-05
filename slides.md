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

```js{0|all}
const React = require('react');
const renderReact = require('hypernova-react').renderReact;

function MyComponent(props) {
  return <div>Hello, {props.name}!</div>;
}

module.exports = renderReact('MyComponent.js', MyComponent);
```
</div>

---

# Airbnb 사례: Hypernova

1. 클라이언트 코드가 서버 코드로부터 분리될 수 있음
2. 점진적으로 적용 가능함

실제로 구현된 사례를 확인함으로써 아키텍처 설계에 대한 힌트를 얻게 되었다!

---

# New 프로그래머스 프론트엔드 아키텍처 - Core Concept

- 웹 애플리케이션 manifest를 Rails 템플릿에 임베딩
  - [Webpack Manifest](https://webpack.js.org/concepts/manifest/)
- Vue/React/Anything in Rails

---

# Webpack Manifest

Webpack으로 빌드된 애플리케이션이 포함하는 코드

1. 개발자가 작성한 소스 코드
2. 작성한 소스 코드가 의존하는 라이브러리 및 벤더(vendor) 코드
3. Webpack runtime 및 **manifest**

**Manifest**

> As the compiler enters, resolves, and maps out your application, it keeps detailed notes on all your modules. This collection of data is called the "Manifest," and it's what the runtime will use to resolve and load modules once they've been bundled and shipped to the browser. 

---

# Webpack Manifest Plugin

Webpack manifest를 추출할 수 있게 해주는 플러그인

```ts{all|8-10}
// webpack.config.ts
import { WebpackManifestPlugin } from 'webpack-manifest-plugin';

const productionConfig: Configuration = {
  mode: 'production',
  output: { filename: '[contenthash].js' },
  module: { ... },
  plugins: [
    new WebpackManifestPlugin(),
  ],
};
```

```json{0|all}
// dist/manifest.json
{
  "main.js": "/b815c1d65ebb736ed574.js",
  "175.css": "/175.67ac9023a9a76244ce98.css",
  "175.css.map": "/175.67ac9023a9a76244ce98.css.map",
  "js": "/02067bc9c4a3d115e4c4.js",
  "js.map": "/02067bc9c4a3d115e4c4.js.map",
  "index.html": "/index.html",
}
```

---

# 기존 프로그래머스 프론트엔드 아키텍처

<figure class="flex items-center justify-center">
  <img src="/programmers-frontend-architecture-old.png" width=600 />
</figure>

---

# 개선된 프로그래머스 프론트엔드 아키텍처

<figure class="flex items-center justify-center">
  <img src="/programmers-frontend-architecture-v1.png" width=600 />
</figure>

---

# 개선된 아키텍처의 장점 - (1) Scalability

기존 Rails 코드베이스의 수정 없이 새로운 웹 애플리케이션 개발 및 배포가 가능

<figure class="flex items-center justify-center">
  <img src="/programmers-frontend-architecture-v1-pros.png" width=720 />
</figure>

---

# 개선된 아키텍처의 장점 - (2) Independency

애플리케이션의 의존성을 독립적으로 관리가 가능

<figure class="flex items-center justify-center">
  <img src="/programmers-frontend-architecture-v1-pros-2.png" width=720 />
</figure>

---
layout: center
---

## 그렇게 프로그래머스 프론트엔드 개발자들은 행복하게 잘 살았습니다

---
layout: center
class: "text-center"
---

## 그렇게 프로그래머스 프론트엔드 개발자들은 행복하게 잘 살았습니다

## ...가 또 불편한 점이 생겼습니다

---

# 개선된 아키텍처의 불편한 점

각 애플리케이션에서 의존하는 코드가 새로 릴리즈되면 해당 사항을 반영하기 위해 모든 앱을 다시 배포해야 함

<figure class="flex items-center justify-center">
  <img src="/programmers-frontend-architecture-v1-cons.png" width=600 />
</figure>

---

# 개선된 아키텍처의 불편한 점

각 애플리케이션에서 의존하는 코드가 새로 릴리즈되면 해당 사항을 반영하기 위해 모든 앱을 다시 배포해야 함

<figure class="flex items-center justify-center">
  <img src="/programmers-frontend-architecture-v1-cons-2.png" width=600 />
</figure>

---
layout: center
class: "text-center"
---

앱을 다시 배포하지 않고 의존하는 코드의 변경사항을 런타임에 바로 적용할 수 없을까?

---
layout: center
class: "text-center"
---

앱을 다시 배포하지 않고 의존하는 코드의 변경사항을 런타임에 바로 적용할 수 없을까?

## Webpack 5 Module Federation

---

# Webpack 5 Module Federation - Core Concept

- 빌드된 애플리케이션 = 컨테이너
  - 호스트(Host) 컨테이너: 모듈을 외부로 노출(expose)
  - 리모트(Remote) 컨테이너: 외부에 노출된 모듈을 불러와 사용
- Webpack 5버전 부터 포함된 Container Plugin을 사용하여 적용 가능
  - Container Plugin을 추상화 한 [Module Federation Plugin](https://webpack.js.org/plugins/module-federation-plugin/)을 사용하면 더 쉽게 적용 가능

---
layout: two-cols
---
# Module Federation Plugin
<br />
<p>Host 컨테이너의 webpack config</p>

```ts{all|5-12}
import { container } from 'webpack';

const config: Configuration = {
  // ...생략
  plugins: [
    new container.ModuleFederationPlugin({
      remotes: {
        remoteApp: 
          `remoteApp@${process.env.APP_URL}/remoteEntry.js`,
      },
    }),
  ],
}
```

::right::

<br />
<br />


<div class="pl-[40px]">
  <p class="pt-[30px]">Remote 컨테이너의 webpack config</p>

```ts{0|all|5-13}
import { container } from 'webpack';

const config: Configuration = {
  // ... 생략
  plugins: [
    new container.ModuleFederationPlugin({
      name: 'remoteApp',
      filename: 'remoteEntry.js',
      exposes: {
        './Header': './src/components/Header.tsx',
      },
    }),
  ],
};
```

</div>

---

# Module Federation Example

#### 외부 모듈 사용 예시

```tsx{all|4,6,7,9,11,12}
import { LoadingSpinner } from '@/components/LoadingSpinner'
import { Suspense } from 'react';

const Header = lazy(() => import('remoteApp/Header'));

function CustomHeader() {
  return (
    <Suspense fallback={<LoadingSpinner />} >
      <Header />
    </Suspense>
  );
}
export default CustomHeader;
```

---

# 개선된 프론트엔드 아키텍처(현재)

<figure class="flex items-center justify-center">
  <img src="/programmers-frontend-architecture-v2.png" width=620 />
</figure>

---
layout: center
class: "text-center"
---

그래서 우리는 얼마나 빠르게 제품을 개발하고 배포할 수 있게 되었을까요?

---
layout: center
class: "text-center"
---

#### 프로덕션 릴리즈 횟수

<br />

2022년 3월: 46회

### 2022년 11월: 173회

---
layout: center
class: "text-center"
---

#### 프론트엔드 프로덕션 배포 소요 시간

<br />

기존: 30 ~ 40분

### 현재: < 5분

---
layout: center
class: "text-center"
---

#### Closed Pull Request 개수

<br />

기존 repo: 6년 4개월 간 5500여개 (72 PRs / month)

### 신규 repo: 8개월 간 1400여개 (175 PRs / month)

---

# Next Steps

- Rails Webpacker를 [Shakapacker](https://github.com/shakacode/shakapacker)로 마이그레이션
  - Webpack 5 의 기능을 사용할 뿐만 아니라 babel 외의 빌드 도구(swc, esbuild)도 사용 가능
- Rails 애플리케이션에서 Webpack 의존성을 분리
  - Rails는 마치 API 서버처럼 동작
- 개발 경험 개선
  - remote module의 type checking
  - 앱 간의 커뮤니케이션 컨벤션 정의
  - Best practice 탐구

---
layout: center
---

### 프로그래머스의 프론트엔드 개발 경험을 개선하기 위한 모험은 계속됩니다

---

# Recap

