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
