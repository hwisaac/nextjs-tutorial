# NextJs 프레임워크 튜토리얼

## NextJs 프로젝트 시작하기

- `npx create-next-app@latest` 최신버전으로 nextjs 프로젝트를 생성한다.
- 프로젝트명, typescript 템플릿 여부, eslint 여부 등 옵션을 선택하면 끝

## 초기화

1. `/pages` 폴더에 있는 걸 전부 삭제
   - `/pages/index.js` 를 만들자. home component 에 넣은 값이 화면에 보인다.

## Pages

1. 프로젝트의 페이지를 추가할 때는 `react 컴포넌트를 export default 하는 파일`을 **pages 폴더** 에 넣는다.
   > 단순히 export 가 아닌, export default 로 해야 한다.
2. 그러면 Next 가 `파일 이름`을 `url 이름`으로 사용한다. (즉 컴포넌트의 이름 자체는 중요하지 않다.)
3. `index.js` 파일명을 가지는 컴포넌트는 홈url(`/`)이다.
4. .jsx 를 쓰면 React.js 를 import 할 필요가 없다.

```javascript
// pages/index.js
// react 를 import 하지 않아도 정상작동
export default function Home() {
  return (
    <div>
      <h1>Hello</h1>
    </div>
  );
}
```

## Static Pre Rendering

- nextjs 의 장점중 하나는 앱에 있는 페이지들이 미리 랜더링(pre-rendering)된다는 것이다.
- 이 페이지들은 정적(static)으로 생성된다.

| create-react-app                          | create-next-app                                                              |
| ----------------------------------------- | ---------------------------------------------------------------------------- |
| client-side 랜더를 하는 앱을 만든다.      | nextjs가 정적요소를 pre-rendering 한다.                                      |
| js로 유저가 보는 모든걸 브라우저가 만든다 | 실제 html 요소가 들어있다.(볼수 있는 데이터가 들어있다.) <br> SEO에 매우좋다 |

## Routing

- 링크는 `a` 태그가 아닌 `Link` 컴포넌트로한다
- 현재 페이지에 있는지 확인하려면 `router 훅`으로 `router.pathname` 을 체크하면 된다

```javascript
// components/NavBar.js
import Link from "next/link";
import { useRouter } from "next/router";

export default function NavBar() {
  const router = useRouter();
  return (
    <nav>
      <Link href='/'>
        // Link 태그의 children 으로 a 태그를 넣으면 안된다! 에러!
        <a style={{ color: router.pathname === "/" ? "red" : "blue" }}>Home</a>
      </Link>
      <Link href='/about'>
        <a style={{ color: router.pathname === "/about" ? "red" : "blue" }}>
          About
        </a>
      </Link>
    </nav>
  );
}
```

```javascript
import Link from "next/link";
import { useRouter } from "next/router";

export default function NavBar() {
  const router = useRouter();
  console.log(router);

  return (
    <nav>
      <Link href='/' style={{ color: "red" }} className='hello'>
        {" "}
        //링크컴포넌트에 style 과 className 도 정할수 있음 Home
      </Link>
      <Link href='/about'>About</Link>
    </nav>
  );
}
```

```javascript
// console.log(router) 로 찍히는 내용
{
    "pathname": "/",
    "route": "/",
    "query": {},
    "asPath": "/",
    "components": {
        "/": {
            "styleSheets": [],
            "props": {
                "pageProps": {}
            },
            "route": "/",
            "query": {},
            "resolvedAs": "/"
        },
        "/_app": {
            "styleSheets": []
        },
        "/about": {
            "styleSheets": [],
            "props": {
                "pageProps": {}
            },
            "route": "/about",
            "query": {},
            "resolvedAs": "/about"
        }
    },
    "isFallback": false,
    "basePath": "",
    "isReady": true,
    "isPreview": false,
    "isLocaleDomain": false,
    "events": {}
}
```

## CSS module

### NextJs 에서 스타일 입히기

1. `.module.css` 로 스타일 입히기

- 클래스명을 여러개 쓸때 백틱(`) 으로 합치거나, 배열을 만들어놓고 .join(' ') 메소드를 사용하는 방식이 자주 쓰인다.

```javascript
// components/NavBar.js

import Link from "next/link";
import { useRouter } from "next/router";
import styles from "./NavBar.module.css";

export default function NavBar() {
  const router = useRouter();
  console.log(router);
  return (
    <nav>
      <Link
        href='/'
        className={`${styles.link} ${
          router.pathname === "/" ? styles.active : ""
        }`}>
        Home
      </Link>
      <Link
        href='/about'
        className={[
          styles.link,
          router.pathname === "/about" ? styles.active : "",
        ].join(" ")}>
        About
      </Link>
    </nav>
  );
}
```

2. style jsx 로 스타일 입히기
   - 컴포넌트 밑에 `<style>` 태그를 사용해서 스타일 입힐 곳을 감싸준다.
   - 이 방식도 스타일이 독립적으로 작용되며, 클래스이름짓기도 아무거나 해도 상관없다.
   - 파일을 분리해서 만들지 않아도 된다

> Link 컴포넌트에는 `<style>` 태그로 정의한 스타일이 적용이 안된다.

> 스타일이 적용될 div 태그를 하나 더 만들어서 거기다가 css를 입히자.

```javascript
import Link from "next/link";
import { useRouter } from "next/router";

export default function NavBar() {
  const router = useRouter();
  console.log(router);
  return (
    <nav>
      <Link href='/'>
        <div className={router.pathname === "/" ? "active" : ""}>home</div>
      </Link>
      <Link href='/about'>
        <div className={router.pathname === "/about" ? "active" : ""}>
          About
        </div>
      </Link>
      <style jsx>{`
        nav {
          background-color: tomato;
          /* font-size: 100px; */
        }
        a {
          background-color: green;
          /* text-decoration: none; */
        }
        div {
          color: green;
        }
        .active {
          color: yellow;
        }
      `}</style>
    </nav>
  );
}
```

- jsx 말고 global 프롭을 추가하면 전역적으로도 적용된다.

```javascript
// pages/index.js
// style 태그에 global 프롭을 넣으면 NavBar의 a 태그에도 해당스타일이 적용됨
import NavBar from "../components/NavBar";

export default function Home() {
  return (
    <div>
      <NavBar />
      <h1>Hello</h1>
      <style jsx global>{`
        a {
          font-size: 40px;
        }
      `}</style>
    </div>
  );
}
```

## Custom App component (추천)

- `App component` 는 일종의 어떤 컴포넌트의 청사진이다. (NextJs 가 모든 페이지를 랜더링 할수 있게 하는)

### 사용방법

1. `/pages`폴더에 `_app.js` 파일을 만든다. (무조건 이 이름이어야 함. NextJs 는 페이지들을 랜더링 하기 전에 `_app.js` 파일을 을 보기 떄문)
2. 해당 파일에서 컴포넌트를 작성한다.
   - 해당 컴포넌트에 매개변수로 `{Component, pageProps }` props 를 전달한다.
   - `Component` 는 NextJs 가 랜더링 해줄 페이지 컴포넌트를 넣고
   - `pageProps` 에는 그 컴포넌트에 전달해줄 프롭스를 넣어준다.
3. 글로벌 스타일은 `_app.js` 에 넣어주는 게 좋다

> `NextJs` 에서는 `globals.css` 같은 일반 css 파일을 `import`할 수없다. 오직 `.module.css` 만 `import` 가능
> 하지만 커스텀 앱 컴포넌트가 있는 `_app.js` 에서는 `globals.css` 파일도 import 할수 있다.

```javascript
// pages/_app.js
import NavBar from "../components/NavBar";
import "../styles/globals.css";

export default function MyApp({ Component, pageProps }) {
  return (
    <>
      <NavBar /> // NavBar 를 랜더링 하게 한다.
      <Component {...pageProps} />
    </>
  );
}
```

- `_app.js` 는 청사진을 제공해주는데 그 안에 `<NavBar />` 컴포넌트가 들어있다는 말은, `<NavBar />` 는 모든 페이지에 전부 포함된다는 의미이다.
- 따라서, 다음과 같이 `index.js` 나 `about.js` 페이지에서 `<NavBar />` 컴포넌트를 제거해도 항상 NavBar 가 랜더링 된다.

```javascript
// pages/about.js
export default function Potato() {
  return (
    <div>
      <h1>About</h1>
    </div>
  );
}
```

```javascript
// pages/index.js
import NavBar from "../components/NavBar";

export default function Home() {
  return (
    <div>
      <h1>Hello</h1>
    </div>
  );
}
```

<hr>

# Practice Project

## Patterns

- Next 를 사용할떄 따르는 패턴

1. components/Layout.js 를 만든다.

```javascript
// Components/Layout.js
// 레이아웃을 리턴한다
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

2. `pages/_app.js` 에서 레이아웃 컴포넌트로 감싸준다.
   - `<Layout>`으로 감싼 부분이 Layout 컴포넌트의 `children` 으로 들어가게 된다.

```javascript
// pages/_app.js
// <Component {...pageProps} /> 가 레이아웃의 children 에 들어간다.
import Layout from "../components/Layout";
import "../styles/globals.css";

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  );
}
```

> 사람들은 보통 \_app.js 파일을 원하지는 않는다 <br> > \_app.js 에는 구글 Analytics라던지 검색엔진에 관한 것등 import 할 것이 많다
> 그렇게 되면 \_app.js 가 큰 컴포넌트가 되기 떄문에 레이아웃에 관한 사항은 Layout 컴포넌트로 따로 관리하는 것이다.

#### head 컴포넌트

- Next 가 제공하는 head 컴포넌트이다.
- title 같은 것을 변경할 수 있는 컴포넌트이다.

```javascript
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

- 아래와 같이 Seo 컴포넌트에 title 프롭을 전달하여 타이틀을 바꿀수 있다.

```javascript
// pages/about.js
import Seo from "../components/Seo";

export default function Potato() {
  return (
    <div>
      <Seo title='About' />
      <h1>About</h1>
    </div>
  );
}
```

## Fetching Data

### public 폴더

- public 폴더에 넣어두면 ./public/어쩌구 할 필요 없이 바로 src="/vercel.svg" 하여 이미지파일을 가져올 수 있다.

```javascript
// pages/index.js
import { useEffect, useState } from "react";
import Seo from "../components/Seo";

const API_KEY = "10923b261ba94d897ac6b81148314a3f";

export default function Home() {
  const [movies, setMovies] = useState();
  useEffect(() => {
    (async () => {
      const { results } = await (
        await fetch(
          `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`
        )
      ).json();
      setMovies(results);
    })();
  }, []);
  return (
    <div>
      <Seo title='Home' />
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

> <img> 대신에 next 에서 제공하는 imgage 컴포넌트를 쓰는것을 더 추천

### NextJs 로 API key 숨기기

> 아래와 같이 코드를 짜면 api key 가 노출이 된다.

```javascript
import { useEffect, useState } from "react";
import Seo from "../components/Seo";

const API_KEY = "10923b261ba94d897ac6b81148314a3f";

export default function Home() {
  const [movies, setMovies] = useState();
  useEffect(() => {
    (async () => {
      const { results } = await (
        await fetch(
          `https://api.themoviedb.org/3/movie/popular?api_key=${API_KEY}`
        )
      ).json();
      setMovies(results);
    })();
  }, []);
  return (
    <div>
      <Seo title='Home' />
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

> api 키를 숨기는 방법은 Next 에서 제공한다 : REWRITE 기능을 사용하자

1. `next.config.js` 파일을 연다
2. `async redirects(){}` 함수를 추가해준다.
3. `redirects` 함수는 리턴할때 `{source, destination,permanent }` 이 담긴 `배열`을 리턴
   - `source` 주소로 접근하면
   - `destination` 주소로 redirect 시켜라
   - `permanent` 리다이렉션(정보)이 영구적인지 여부
4. `async rewrite(){}` 함수를 추가해준다. 이 함수도 `{source, destination}` 를 리턴한다.
   - `source` 주소로 접근하면 `destination` 주소로 rewrite 시켜라
5. `next.config.js` 파일은 API_KEY 를 외부에서 받아오게 한다.
6. `.env` 파일을 만들고 `.gitignore` 에 `.env` 를 추가한다.
7. .env 파일에 숨기려던 API_KEY 를 저장한다.

```javascript
const API_KEY = process.env.API_KEY;

// next.config.js
const nextConfig = {
  reactStrictMode: true,
  async redirects() {
    return [
      {
        source: "/old-blog/:path*", // * 는 아무거나 붙어도 된다는것
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
```

```javascript
// .env
API_KEY=10923b261ba94d897ac6b81148314a3f
```

> redirect 는 유저가 어디로 가는지 주소를 알수 있지만 rewrite 는 주소를 바꾸지 않기 때문에 유저로부터 주소(API포함)를 숨길 수 있다.

> next.config.js 를 수정하면 서버를 껏다가 다시 켜주자.

### Server Side Rendering

- Next 는 어떤 함수를 제공해주는데, 우리가 SSR 을 하게 도와준다.

#### 리로드할때 일어나는 일

- Next 가 page를 html을 export 하거나 pre-render 한다
- html 은 app의 초기 state 로 만들어진다.
- 그리고 데이터를 가져오는 동안 로딩상태를 유저에게 띄워 보여준다.

> 이 로딩을 보여주는 것조차 마음에 들지 않고, 서버에서 일어나는 모든 작업을 마치고 난 뒤에 정보를 보내는 방법이 Server Side Rendering 이다.<br>

#### getServerSideProps 함수

> `getServerSideProps` 라는 이름의 함수를 export 한다

```javascript
// pages/index.js

import { useEffect, useState } from "react";
import Seo from "../components/Seo";

export default function Home({ results }) {
  return (
    <div className='container'>
      <Seo title='Home' />
      {results?.map((movie) => (
        <div className='movie' key={movie.id}>
          <img src={`https://image.tmdb.org/t/p/w500/${movie.poster_path}`} />
          <h4>{movie.original_title}</h4>
        </div>
      ))}
    </div>
  );
}

export async function getServerSideProps() {
  const { results } = await (
    await fetch(`http://localhost:3000/api/movies`)
  ).json();
  return {
    props: {
      results,
    },
  };
}
```

- `getServerSideProps` 함수에서 작성한 코드는 server에서 실행된다.
- 이를 이용하면 API 키도 숨길수 있다. client 에서 실행되지 않으니까
- 이 함수는 `object`를 `리턴`하는데 `props` 라는 키(혹은 property)가 들어 있다.
  - props 에는 원하는 데이터를 담아아 리턴하고 (예시- `results`)
  - 그걸 랜더링 하는 컴포넌트에 해당 props 를 파라미터로 받는다. `function Home({ results }) {}`
- Next가 클라이언트에서 랜더링 하는 단계에서 `_app.js` 에서 전달되는 `pageProps` 에 담겨서 해당 컴포넌트에 들어가게 된다 : `<Component {...pageProps} />`

> getServerSideProps() 함수의 로직에서 url 을 사용할 때는 `절대경로`를 사용해야 한다.<br>
> 'app/movies' 라고만 하면 이는 클라이언트단에서는 이해할 수 있지만 서버에서는 base url 이 없기 때문이다.

### Dynamic Routes

- `creact-react-app` 에서는 `/movies/:id` 로 URL 을 특정하고 id 를 변수로 인식한다.
- Next 에는 router 가 없기 떄문에 라우트를 설정하진 않는다.

#### Next 에서 `/movies` 라우트 만들기

1. `pages/movies/index.js` 파일을 만든다
2. `export default` 로 컴포넌트를 내보낸다

```javascript
//pages/movies/index.js
// /movies  주소에 들어가면 랜더링된다.
export default function All() {
  return "movie index";
}
```

#### Next 에서 /movies/:id 라우트 만들기 : url 에 변수 넣기

1. `pages/movies/[id].js` 파일을 만든다
2. `export default` 로 컴포넌트를 내보낸다

```javascript
// pages/movies/[id].js
import { useRouter } from "next/router";

export default function Detail() {
  const router = useRouter();
  console.log(router); // router.query.id 여기에 변수가 저장된다.
  return "detail";
}
```

#### Next 에서 /movies/all 라우트 만들기

1. `pages/movies/index.js` 와 `pages/movies/all.js` 를 넣어준다.
2. /movies 로 들어가면 movies 폴더의 index.js 가 랜더링되고 /movies/all 주소로 들어가면 all.js 가 랜더링된다.

### Movie Detail

#### URL 로 state보내는 방법

1. 쿼리로 보내기 : router 훅을 사용한다.

```javascript
// pages/index.js

const onClick = (id, title) => {
  router.push({
    pathname: `/movies/${id}`,
    query: {
      title, // movies/55555?title=avengers  의 주소로 push 해준다.
    },
  });
};
```

> 하지만 정보를 url 에 담길 원하지 않는 경우<br>
> 브라우저의 url 을 마스킹 한다.<br> > `router.push({pathname, query}, maskingURL)` 에서 두번째 인자로 마스킹할 인자를 적고 <br> > `Link` 에서는 `<Link href={~~} as={maskingURL}>` 처럼 `as` 프롭으로 마스킹한다.

```javascript
// pages/index.js

const onClick = (id, title) => {
  router.push(
    {
      pathname: `/movies/${id}`,
      query: {
        title,
      },
    },
    `/movies/${id}` // 마스킹하여 브라우저 주소창에 실제보이는 주소를 설정함
  );
};
```

- 위와같이 작성해도 router.query 에 원하는 데이터가 담겨있다.
- Link 컴포넌트를 사용해서 데이터를 담아보낼 수도 있다

```javascript
<Link href ={{ pathname: `/movies/${movie.id}`,
               query: {
                  title: movie.original_title,
               }}}
               as = {`/movies/${movie.id}`} // 마스킹하여 실제보이는 주소
               >
```

> 영화 타이틀 같은 경우는 구글 seo 에 좋기 때문에 숨길 필요없다

### catch-all URL

> 링크를 이동하며 데이터를 넘겨 받아 랜더링 하면, 해당 주소로 즉시 이동했을 때 링크를 클릭하지 않았기 떄문에 원하는 데이터를 받을 수가 없다. <br> > `movies/1234/영화제목아무거나` 의 형태도 해당 영화의 상세페이지를 보내줄 수 있도록 catch-all URL 을 설정해두면 해당 URL 을 통해 영화 제목의 정보를 받을 수 있다

#### catch-all URL 만드는 방법

> 파일명을 `[id].js` 가 아닌 `[...id].js` 로 변경한다.<br>
> 이렇게 되면 `query.id` 는 단순히 `string` 이 아니라 `배열`이 된다. <br>
> url 이 `movies/1234/avengers/2022` 식이면 쿼리에 담긴 데이터는 `id = ['1234','avengers','2022']` 와 같이 된다.

- 배열쿼리로 만든 후 위 코드 수정

```javascript
// pages/index.js
//수정전
export default function Home({ results }) {
  const router = useRouter();
  const onClick = (id, title) => {
    router.push(
      {
        pathname: `/movies/${id}`,
        query: {
          title,
        },
      },
      `/movies/${id}`
    );
  };
  return (

// 수정후
export default function Home({ results }) {
  const router = useRouter();
  const onClick = (id, title) => {
    router.push(`/movies/${title}/${id}`);
  };
  return (
```

```javascript
// pages/movies/[id].js -> [...params]
// 수정전
import { useRouter } from "next/router";

export default function Detail() {
  const router = useRouter();
  return (
    <div>
      <h4>{router.query.title || "Loading..."}</h4>
    </div>
  );
}

  // 수정후
import Seo from "../../components/Seo";
import { useRouter } from "next/router";

export default function Detail({ params }) {
  const router = useRouter();
  const [title, id] = params || []; // params가 없을때 에러처리
  return (
    <div>
      <Seo title={title} />
      <h4>{title}</h4>
    </div>
  );
}

export function getServerSideProps({ params: { params } }) {
  // 서버에서 params 를 처리해서 보내준다.
  return {
    props: {
      params,
    },
  };
}

```

## 404 page

- 유효하지 않은 페이지로 들어갈 경우를 처리하자
- 단순히 pages/404.js 를 만들어주면 NextJs 가 알아서 처리해준다
-

```javascript
export default function NotFound() {
  return "404";
}
```
