## SEO, Hydrate
next는 페이지가 로딩되면 hydrate를 통해 spa처럼 작동하게 된다.
즉, html이 비어있어서 seo에 좋지 않은 경우가 생길 수 있다.
이를 보완하기 위해 next는 2가지 방식으로 렌더링을 진행한다.
1. Static Generation
  기본적으로 **빌드 시점에 정적 파일을 생성**한다. 다른 컴포넌트는 안되고 pages 컴포넌트만 export async function getStaticProps() {} 함수를 통해 비동기적으로 데이터를 읽어들일때 까지 기다린다.
  아래 코드는 클라이언트 사이드에서 실행되지 않고 서버 사이드에서 실행된다.
  getStaticProps()는 반드시 props 객체를 리턴하고 원래 컴포넌트(HomePage) 에서 해당 props를 통해 DOM을 그려낸다. 이는 모두 **서버 사이드에서 발생하며 SEO에 취약하지 않다.**
  다만, 빌드 타임에 생성되기 때문에 데이터의 최신화가 이루어지지 않는데, 이는 revalidate에 숫자(초) 를 넣음으로써 서버에 재요청 및 데이터 변경, 번들 최신화를 요청할 수 있다.
  
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
