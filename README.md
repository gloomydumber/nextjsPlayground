# nextjsPlayground

framework. (nextjs) not library (react)

## init

```shell
npx create-next-app@latest --typescript
npx create-next-app@latest
```

## view rendering

`pages` 폴더 내부에서 `export default` 하면 _view rendering_ 이 되는 식

파일 이름으로 _routes_ 되며, 파일 내부에서 `export default` 되는 함수명은 _rendering_ 에 있어서는 중요하지 않음

`.jsx` 문법을 기본적으로 지원하며, `useState`, `useEffect`, _lifecycple method_ 등을 사용하는 것이 아니라면, `react` 를 굳이 `import` 할 필요는 없음

## Pros of Server-Side Rendering

`next.js`는 페이지를 미리 렌더링하는 것이 강점 (_server-side rendering_)

`react.js` 의 경우에는 _clinet-side rendering_ 이고, _html source code_ 를 살펴보면, `root`라는 _id_ 를 가진 비어있는 하나의 `div` 태그로 이루어졌음을 알 수 있음

즉, _client-side javascript_ 가 모든 `rendering` 을 담당함
이 경우, 느린 네트워크 환경 (_e.g: 3G Network_) 등에서는 페이지의 `rendering`이 비교적 느려짐
이미지와 같은 _static contents_ 가 로드되는 것은 받아들여질 수 있으나, _client-side javascript_ 파일 자체의 로드가 느려지기 때문에, 비어있는 흰 화면이 오래 보일 수 있음 (UX적으로 좋지 않다)

이러한 문제점을 `Server-Side Rendering` 의 방식인 `Next.js` 에서는 해결해줌

### Hydration

_pages/index.js_ 를 아래와 같이 작성하고, 렌더링 후 브라우저의 개발자 도구를 열어 보면, *pre-rendering*이 되어 `button` 등의 태그가 `html` 에 잘 반영되어 있는 것을 볼 수 있다

```jsx
// index.js
import { useState } from "react";

export default function Home() {
  const [counter, setCounter] = useState(0);
  return (
    <div>
      <h1>Hello {counter}</h1>
      <button onClick={() => setCounter((prev) => prev + 1)}>+</button>
    </div>
  );
}
```

이렇게 `react.js` 를 _client-side_ 에서 실행하도록 하는 것을 `hydration` 이라 함

`hydraion` 을 쓰면,

- user가 웹사이트에 방문했을 때, 초기 상태의 `component`로 된 미리 생성된 `HTML` 페이지를 볼 수 있음 (흰 화면이 아닌)
- 이후 `react.js`가 로드가 되고, 로드 된 `view`와 결합하고, 상호작용이 일어나는 경우에는 `react.js` 가 동작

이러한 점들로 `SEO` _(Search Engine Optimization)_ 이나, 유저에게도 좋은 UX 를 제공하는 등의 장점을 지님

## Routing

### Link

`routing`을 할 때, `HTML` 태그 `a` 를 사용해서 링크를 하면, `react` 에서의 문제와 같이, 전체 페이지가 재로드 되기 때문에, `Nextjs` 가 제공하는 `Link` 컴포넌트를 이용한다.

이는 `react-router-dom` 의 `Link` 와 이름과 역할이 같은 `Component` 임

```jsx
// components/NavBar.js
import Link from "next/link";

export default function NavBar() {
  return (
    <nav>
      <Link href="/">Home</Link>
      <Link href="/about">About</Link>
    </nav>
  );
}
```

### useRouter

`Nextjs` 에서 `useRouter` 훅도 기본적으로 제공함

```jsx
import Link from "next/link";
import { useRouter } from "next/router";

export default function NavBar() {
  const router = useRouter();
  return (
    <nav>
      <Link
        style={{ color: router.pathname === "/" ? "red" : "blue" }}
        href="/"
      >
        Home
      </Link>
      <Link
        style={{ color: router.pathname === "/about" ? "red" : "blue" }}
        href="/about"
      >
        About
      </Link>
    </nav>
  );
}
```

## CSS Modules

- 단순히 `jsx` 에 작성하기
- `modules` 이용하기
- `tailwind` 이용하기

### modules

`fileName.module.css` 의 패턴으로 파일 작성

_backticks_ 를 이용하거나, _array_ 의 `join()` 을 이용하는 법 두 가지

두 가지 방법 다 번거로운 면이 있음

```css
/* NavBar.module.css */
.link {
  text-decoration: none;
}

.active {
  color: tomato;
}
```

```jsx
import Link from "next/link";
import { useRouter } from "next/router";
import styles from "./NavBar.module.css";

export default function NavBar() {
  const router = useRouter();
  return (
    <nav>
      <Link
        className={`${styles.link} ${
          router.pathname === "/" ? styles.active : ""
        }`}
        href="/"
      >
        Home
      </Link>
      <Link
        className={[
          styles.link,
          router.pathname === "/about" ? styles.active : "",
        ].join(" ")}
        href="/about"
      >
        About
      </Link>
    </nav>
  );
}
```

### Styles JSX

`next.js`의 버전이 업그레이드 되면서, `Link` 컴포넌트의 `a` 처리 방식이 달라졌기 때문에,

아래의 `css` 는 잘 적용되지 않으나, 문법 자체는 아래와 같음

`tailwind` 를 쓸 것이기 때문에 방법만 알아둠

```js
// components/NavBar.js
import Link from "next/link";
import { useRouter } from "next/router";

export default function NavBar() {
  const router = useRouter();
  return (
    <nav>
      <Link className={router.pathname === "/" ? "active" : ""} href="/">
        Home
      </Link>
      <Link
        className={router.pathname === "/about" ? "active" : ""}
        href="/about"
      >
        About
      </Link>
      <style jsx>{`
        nav {
          background-color: tomato;
        }
        a {
          text-decoration: none;
        }
        .active {
          color: yellow;
        }
      `}</style>
    </nav>
  );
}
```

`global` 로 적용하려면 아래와 같이 `jsx global`로 태그 속성을 작성하면 되나, 해당 페이지 내에서만 작동하므로, `component` 마다 설정해줘야 하므로 불편하다

```js
<style jsx global>{`
  nav {
    background-color: tomato;
  }
  a {
    text-decoration: none;
  }
  .active {
    color: yellow;
  }
`}</style>
```

## App Component

_pages_ 폴더에 `_app.js` 를 생성

`Nextjs` 는 렌더링 시에 `_app.js` 를 우선적으로 확인하고 다른 `components` 를 렌더링 함

즉, `_app.js` 를 처리 후에 `index.js` 등을 렌더링 함

이러한 특성으로 `_app.js` 는 모든 `components`에 대해 청사진 역할을 함

`_app.js` 를 아래와 같이 작성

```js
import NavBar from "../components/NavBar";
import "../styles/globals.css";

export default function App({ Component, pageProps }) {
  return (
    <>
      <NavBar />
      <Component {...pageProps} />
      <style jsx global>{`
        a {
          text-decoration: none;
          color: white;
        }
        .active {
          color: yellow;
        }
      `}</style>
    </>
  );
}
```

## Layout Pattern

`_app.js` 에는 _global_ 로 `import` 해야 할 많은 것들이 있으므로, `components` 폴더에 `Layout.js` 파일을 만들어 _Layout Pattern_ 으로 코드를 작성하는 것이 좋다

`Nextjs` 에서도 `react` 와 마찬가지로 `children` 라는 키워드의 `props` 가 이용 가능하다

```js
// Layout.js
import NavBar from "./NavBar";

export default function Layout({ children }) {
  return (
    <>
      <NavBar />
      <div>{children}</div>
    </>
  );
}
```

이후 `_app.js` 에서, `Layout.js` 를 불러와서 아래와 같이 작성

```js
// _app.js
import Layout from "../components/Layout";
import "../styles/globals.css";

export default function App({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  );
}
```

## Head

`Nextjs` 에서는 `head` 와 같은 기본적인 `component` 의 기능을 제공한다

`head` 는 `react` 에서의 `react-helemt` 과 같은 기능을 하는 그것임

우선, `Seo.js` 라는 _component_ 를 아래와 같이 생성한다

```js
// components/Seo.js
import Head from "next/head";

export default function Seo({ title }) {
  return (
    <Head>
      <title>{title} | Next Movies</title>
    </Head>
  );
}
```

이후 아래와 같이, 각 `page` 에서 불러와 사용

```js
// index.js
import Seo from "../components/Seo";

export default function Home() {
  return (
    <div>
      <Seo title="Home" />
      <h1>Hello</h1>
    </div>
  );
}
```

## Image

`nextjs` 에서 `image` 를 다루는 방법으로 `Prisma`, `Tailwind`, `Planet Scale` 등의 좋은 방법이 있지만, 기본적으로도 `public` 폴더에 있는 경우에는 경로를 상대경로로 상세히 명세하지 않아도 된다

```js
// components/NavBar.js
<img src="/vercel.svg" />
```

## Fetching Data

`react`의 `useEffect` 를 사용하는 것과 다를 것이 없음

`useEffect`의 2번 실행 해결은 역시 `react`에서의 마찬가지로 `next.config.js`에서 `strictMode`를 `false` 로 설정 후 서버 재시작

```js
// index.js
import { useEffect, useState } from "react";
import Seo from "../components/Seo";

const API_KEY = "75e68d6387b1bcd7a45c31a4b2278f8e";

export default function Home() {
  const [movies, setMovies] = useState();
  useEffect(() => {
    (async () => {
      const { results } = await (
        await fetch(
          `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`
        )
      ).json();

      console.log(results);
      setMovies(results);
    })();
  }, []);
  return (
    <div>
      <Seo title="Home" />
      {!movies && <h4>Loading...</h4>}
      {movies?.map((movie) => (
        <div key={movie.id}>
          <h4>{movie.original_title}</h4>
        </div>
      ))}
    </div>
  );
}
```

## next.config.js

### redirects

아래와 같이 `next.config.js` 내부에 `redirects` 함수를 정의하는 방법으로 `Nextjs` 에서 _redirects_ 를 할 수 있다

```js
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: false,
  async redirects() {
    return [
      {
        source: "/old-blog/:path*",
        destination: "/new-blog/:path*",
        permanent: false,
      },
    ];
  },
};

module.exports = nextConfig;
```

### rewrites

`client-side js` 에서의 `fetch` 를 `masking` 하여, `API KEY` 등 의 노출 등을 막아줌

`next.config.js` 에서 `rewrites` 함수를 정의하는 방법으로 *rewrites*를 할 수 있다

```js
// next.config.js
/** @type {import('next').NextConfig} */
const API_KEY = process.env.API_KEY;

const nextConfig = {
  reactStrictMode: false,
  async redirects() {
    return [
      {
        source: "/old-blog/:path*",
        destination: "/new-blog/:path*",
        permanent: false,
      },
    ];
  },
  async rewrites() {
    return [
      {
        source: "/api/movies",
        destination: `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`,
      },
    ];
  },
};

module.exports = nextConfig;
```

```js
// index.js
const [movies, setMovies] = useState();
useEffect(() => {
  (async () => {
    const { results } = await (await fetch("/api/movies")).json();
    // 여기서의 fetch가 rewrites 된다
    console.log(results);
    setMovies(results);
  })();
}, []);
```

## .env

아래를 참고

- .env 파일 : 모든 환경에서 공통적으로 적용할 디폴트 환경변수를 정의한다. 가장 우선순위가 낮다.
- .env.development 파일: 개발 환경(process.env.NODE_ENV === 'development') 에서 적용된다.
- .env.production 파일: 배포/빌드 환경(process.env.NODE_ENV === 'production') 에서 적용된다.
- .env.test 파일: 테스트 환경(process.env.NODE_ENV === 'test') 에서 적용된다.
- .env.local 파일 : 가장 우선순위가 높다. 다른 파일들에 정의된 값들을 모두 덮어쓴다.(오버라이드)

[Next.js 환경변수 사용법](https://curryyou.tistory.com/503)

## Server-Side Rendring

### get server-side props

앞서 `Hydration` 파트에서 언급했듯이, `Server-Side Rendring` 덕분에, 이용자는 완전히 로딩이 되기 전에 `html` 을 볼 수 있다

이 때, 가령 _<div>Loading..</div>_ 과 같이 로드 상태를 표시하는 `html` 태그도 함께 전송되는데 이를 해결하고자 한다

이를 위해서 `getServerSideProps` 라는 함수를 사용한다

이 함수는 렌더링 전에 백엔드 서버에서 먼저 동작하고, 실행의 결과를 `pageProps` 로 전달한다

함수 내에서는, 다른 원격 서버나, `DB` 로부터 데이터를 `fetch` 하여 불러오거나 하는 동작이 수행됨

이렇게 처리하면 렌더링 후의 `html`을 제공하기 때문에, `SEO` 최적화를 할 수 있음

```js
// _app.js
import Layout from "../components/Layout";
import "../styles/globals.css";

export default function App({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} /> // pageProps
    </Layout>
  );
}
```

```js
// index.js
import { useEffect, useState } from "react";
import Seo from "../components/Seo";

export default function Home({ results }) {
  return (
    <div className="container">
      <Seo title="Home" />
      {results?.map((movie) => (
        <div className="movie" key={movie.id}>
          <img src={`https://image.tmdb.org/t/p/w500/${movie.poster_path}`} />
          <h4>{movie.original_title}</h4>
        </div>
      ))}
      <style jsx>{`
        .container {
          display: grid;
          grid-template-columns: 1fr 1fr;
          padding: 20px;
          gap: 20px;
        }
        .movie img {
          max-width: 100%;
          border-radius: 12px;
          transition: transform 0.2s ease-in-out;
          box-shadow: rgba(0, 0, 0, 0.1) 0px 4px 12px;
        }
        .movie:hover img {
          transform: scale(1.05) translateY(-10px);
        }
        .movie h4 {
          font-size: 18px;
          text-align: center;
        }
      `}</style>
    </div>
  );
}

export async function getServerSideProps() {
  const { results } = await (
    await fetch("http://localhost:3000//api/movies")
  ).json();
  return {
    props: {
      results,
    },
  };
}
```

`Nextjs`는 _front-end_ 뿐만 다루는 것이 아니라, 이러한 _server_ 관련한 기능도 많다

위와 같은 `getServerSideProps` 를 이용하는 방법으로 데이터를 _fetch_ 하고 그 데이터가 유효할 때 화면에 렌더링 하는 것이 좋을지, 우선은 _loading_ 중임을 나타내는 UI를 보여준 후에, `reactjs`가 로딩되고, `fetch`한 데이터가 유효할 때 렌더링 하는 것이 좋을지 선택하여 구현할 수 있다

(전자는 _Server-Side Only Rendering_, 후자는 *Server-Side Rendering*에 *Client-Side Rendering*의 요소를 도입)

## Dynamic Routes

`react` 에서는 `react-router-dom` 이 아래와 같은 방식으로 _routing_ 함

> /datas/:id

`Nextjs` 에서는 `router` 가 없으므로 폴더 및 파일이름으로 *routing*을 구현한다

가령, 아래와 같은 _routing_ 을 구현하고 싶다면,

> /movies/all

`pages` 폴더에 하위 폴더로 _movies_ 라는 폴더를 생성하고, _all.js_ 파일을 작성하는 방식으로 _routing_ 한다

또, 이 경우 아래 경로는

> /movies

_movies_ 폴더 내에 _index.js_ 파일에서 구현하면 된다

즉, _routes_ 되는 페이지가 하나 밖에 없다면, 최상위 폴더인 `pages` 폴더에서 작성하면 되고, 여러 페이지라면 폴더를 만들고 그 폴더 내부에서 구현하면 되는 식이다

### URL with variables

앞서 언급한 `react-router-dom` *id*가 포함된 URL을 `Nextjs`에서 _routing_ 하려면,

`[]` 키워드로 파일이름을 작성하면 간단하게 해결된다

가령, 아래와 같은 경로로 _routing_ 이 필요하다면,

> /movies/:id

마찬가지로, `pages` 폴더 내에 _movies_ 폴더를 만들고, _[id].js_ 와 같은 파일명으로 생성하면 쉽게 처리가 가능하다

## Router & Link

페이지 이동을 위해서, `Link` 태그를 사용하는 법도 있고,

`useRouter` 훅을 사용하는 법도 있다.

```js
const router = useRouter();
const onClick = (id) => {
  router.push(`movies/${id}`);
};

return (
  <div onClick={() => onClick(movie.id)} className="movie" key={movie.id}>
    contents...
  </div>
);
```

또, `useRouter`나 `Link` 나 모두 `as` 를 이용하면 URL `masking` 과 `query`로 데이터 전달이 가능하다

```js
const router = useRouter();
const onClick = (id) => {
  router.push(
    {
      pathname: `movies/${id}`,
      query: {
        title,
      },
    },
    `movies/${id}`
  );
};

return (
  <div onClick={() => onClick(movie.id)} className="movie" key={movie.id}>
    contents...
  </div>
);
```

## Catch All URL

앞서 `[]` 키워드로 URL에서의 _variables_ 를 처리 했다면,

`[...]` 키워드로는 URL에 여러 문자열 정보가 들어간 상태에서도 처리가 가능하다

가령, 아래의 사례에서 쓰일 수 있다

> /movies/154325

위의 경우를 영화 제목도 URL에 포함시켜서, 좀 더 `SEO` 최적화하고 싶은 경우 아래와 같이, _routing_ 하고 싶다면,

> /movies/welcome+to+dongmarkgol/154325

_movies_ 폴더 내부에 파일 이름을 _[...params].js_ 로 선언하고 작성하면 된다

```js
// [...params].js
export default function Detail({ params }) {
  const [title, id] = params;

  return (
    <div>
      <h4>{title}</h4>
    </div>
  );
}

export function getServerSideProps({ params: { params } }) {
  return {
    props: { params },
  };
}
```
