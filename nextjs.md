## Render
Next는 기본적으로 pre-render한다. 특별한 함수를 작성하지 않아도 서버측에서 렌더링 한 결과물을 클라이언트에 보내주도록 되어있다.

## SEO, Hydrate
next는 페이지가 로딩되면 hydrate를 통해 spa처럼 작동하게 된다.
즉, html이 비어있어서 seo에 좋지 않은 경우가 생길 수 있다.
이를 보완하기 위해 next는 2가지 방식으로 렌더링을 진행한다.
1. Static Generation
  기본적으로 **빌드 시점에 정적 파일을 생성**한다. 다른 컴포넌트는 안되고 pages 컴포넌트만 export async function getStaticProps() {} 함수를 통해 비동기적으로 데이터를 읽어들일때 까지 기다린다.
  아래 코드는 클라이언트 사이드에서 실행되지 않고 서버 사이드에서 실행된다.
  getStaticProps()는 반드시 props 객체를 리턴하고 원래 컴포넌트(HomePage) 에서 해당 props를 통해 DOM을 그려낸다. 이는 모두 **서버 사이드에서 발생하며 SEO에 취약하지 않다.**
  다만, 빌드 타임에 생성되기 때문에 데이터의 최신화가 이루어지지 않는데, 이는 revalidate에 숫자(초) 를 넣음으로써 서버에 재요청 및 데이터 변경, 번들 최신화를 요청할 수 있다.
  또한 정적 파일을 CDN과 같은 캐시를 통해 렌더링 속도를 올릴 수 있는 장점이 있다.
  
  ```
const HomePage = (props) => {
    return <MeetupList meetups={props.meetups} />;
};

export async function getStaticProps() {
    return {
        props: {
            meetups: DUMMY_MEETUPS,
        },
        // 10초에 한번씩 서버에 재요청 및 데이터 변경
        revlaidate: 10,
    };
}

export default HomePage;
  ```




2. Server-side Rendering
  **모든 요청을 반영한다.** 장점이자 단점이 될 수 있다.
  *요청 객체(context.req)에 접속할 필요(인증 등)가 없거나 매번 바뀌는 데이터가 아니라면 캐싱이 되는 Static Generation이 좋다.*
```
const HomePage = (props) => {
    return <MeetupList meetups={props.meetups} />;
};

export async function getServerSideProps(context) {
    const req = context.req;
    const res = context.res;
     
    // fetch data from an API
    // run by server
    // 요청이 들어올때마다 실행됨
    return {
        props: {
            meetups: DUMMY_MEETUPSY
        }
    }
}

export default HomePage;

```

추가적으로 getStaticPaths를 통해 동적 경로에 대한 pre-generate를 지정하고 fallback을 설정할 수 있다.
fallback을 false로 하면 사전에 빌드한 페이지에 해당하는 않는 경로에 404를 띄워주지만 true나 'blocking'으로 하면 요청이 올 때 새로 페이지를 만들어낸다.
true는 바로 화면으로 전환하고 데이터가 그려질때 까지 interaction을 개발자가 구현해야 하고, 'blocking'은 페이지가 다 그려지면 화면을 전환한다.

## api route
pages > api 폴더 안에 javascript 파일은 api route 이다. 서버 사이드에서 동작하며 해당 파일 경로(url)에 접근하면 함수가 실행된다. 이를 활용하여 serverless 웹을 구현할 수 있다.
api 내부 파일 이름이 api url이 되며 클라이언트에서 절대 접근할 수 없는 코드이다.
또한, getStaticprops() 내부에서 활용하기 위해 mongodb와 같은 패키지를 Import 할 때, getStaticProps()는 서버에서만 실행되기 때문에 자동으로 클라이언트 번들에 해당 패키지를 제외한다. (성능, 보안 향상) 
주의할 점은 **getStaticProps이나 getStaticPaths처럼 서버 사이드에서 작동하는 함수 내부에서는 api route를 fetch 하기보단 db에 직접 요청을 하는 방식이 좋다.**

## Image
- Next.js의 Image 태그는 최신 웹용으로 확장된 next/imageHTML img요소이다.
- 브라우저에서 지원하는 경우 JPEG보다 약 30 % 작은 WebP와 같은 최신 이미지 형식으로 이미지를 자동으로 제공한다. (필요에 따라 이미지 최적화)
- 뷰포트를 스크롤하는 동안 특정 임계 값에 도달 한 경우에만 페이지 내부의 이미지를 지연로드한다.
- 동적으로 사용할 다양한 및 사용자 정의 해상도에 대해 다른 이미지 크기를 지정할 수 있다.
- 사진의 품질을 75 %로 설정된 낮은 임계 값으로 자동 변경한다. (각 호출에 대해 변경 가능)
