# Next.js - React, Express.js, SSR

Retrospect겸 노트할만한 것들만 취함

## Next.js

`Next.js` 는 `React`, `Express.js`, `React-Router-Dom`, `Server Side Rendering`의 기능을 하나의 라이브러리로 내재화 함

즉, `Next.js` 는 서버까지 내장되어 있음

## 현재 경로에 Next.js init

```bash
npx create-next-app@latest .
```

## API Route

`Next.js`에서는 _front-end_ 사이드 뿐만 아니라 _pages_ 하위에 _api_ 폴더 하위에서 _Back-end_ 사이드의 `API` 또한 구축할 수 있음

## 환경변수

앞에 접두사 `NEXT_PUBLIC_` 을 붙여서 환경변수 설정을 하게 되면 _front-end_ 상에서도 해당 환경변수에 접근 가능
