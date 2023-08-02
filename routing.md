## Defining Routes
아래 사진처럼 폴더내에 page파일을 생성하면 해당 폴더의 이름으로 routing가능하다.
<img width="645" alt="image" src="https://github.com/endmoseung/NextJSDocs/assets/103626175/50462ba7-1421-4f4a-8041-26f78219cf98">

## Pages and Layouts
전체적인 Layout을 지정해줄 수 있고, 페이지별로 layout도 설정해줄 수 있다.<br>
app폴더 내에 있는 layout(Required 필수)파일은 전체의 layout으로 지정돼고 내가 페이지로 등록한 폴더내에 layout파일이 있다면 해당 페이지별로 routing을 적용 가능하다.(NestingLayout)
<br>
<img width="644" alt="image" src="https://github.com/endmoseung/NextJSDocs/assets/103626175/69ea390a-f40c-430d-9d97-c24f78734d79">
### RootLayout에서 알면 좋을것들
- 앱 디렉터리에는 루트 레이아웃이 포함되어야 합니다.
- Next.js는 <html> 및 <body> 태그를 자동으로 생성하지 않기 때문에 루트 레이아웃은 이러한 태그를 정의해야 합니다.
- 내장된 SEO 지원 기능을 사용하여 <head> HTML 요소(예: <title> 요소)를 관리할 수 있습니다.
- 루트 레이아웃은 기본적으로 서버 구성 요소이므로 클라이언트 구성 요소로 설정할 수 없습니다.

### NestingLayout에서 알면 좋을것들
- 오직 RootLayout만 <html> 이나 <body> 태그를 가질 수 있다.
- RootLayout은 NestingLayout로 뎦어쓰여진다.

## Linking and Navigating
### <Link>태그와 useRouter훅
- Link태그와 useRouter훅의 차이는 ssr인지 csr차이인것 같다.
- useRouter은 push라는 method를 통해 navigating을 할 수 있다.
- useRouter또한 prefetch기능을 이용해서 ssr같은 효과를 낼 수 있다.

## Route Groups
Route Groups를 통해 생성되어야 할 페이지와 아닌 페이지를 구분해서 만들 수 있다.<br>
<img width="646" alt="image" src="https://github.com/endmoseung/NextJSDocs/assets/103626175/59521fbe-29ef-4f44-ae2a-54bc5f3d8f77">
<br>
### Creating multiple root layouts
아래 사진처럼 두개 이상의 rootLayout을 만들수 있고 그렇게 한다면 기존의 rootLayout을 지워줘야한다.<br>
<img width="626" alt="image" src="https://github.com/endmoseung/NextJSDocs/assets/103626175/58bc36b1-6a9f-42c5-9690-0096552e2790">
<br>
- 경로 그룹의 이름 지정은 조직에 대한 것 외에 특별한 의미가 없습니다. URL 경로에는 영향을 미치지 않습니다.
- 경로 그룹이 포함된 경로는 다른 경로와 동일한 URL 경로로 확인되지 않아야 합니다. 예를 들어, 경로 그룹은 URL 구조에 영향을 미치지 않으므로 (마케팅)/about/page.js 및 (shop)/about/page.js가 모두 /about으로 확인되고 오류가 발생합니다.
- 최상위 layout.js 파일 없이 루트 레이아웃을 여러 개 사용하는 경우에는 app/(마케팅)/page.js 파일을 경로 그룹 중 하나에 정의해야 합니다.s 파일을 정의해야 합니다.
- 여러 루트 레이아웃에서 탐색하면 전체 페이지 로드가 발생합니다(클라이언트 측 탐색과는 반대). 예를 들어 app/(shop)/layout.js를 사용하는 /cart에서 app/(마케팅)/layout.js를 사용하는 /blog로 탐색하면 전체 페이지 로드가 발생합니다. 이는 여러 루트 레이아웃에만 적용됩니다.

## dynamic Routes
dynamic Routing은 [id] or [slug]로 가능하다.

## UI Loading and Streaming
### Loading
layout과 마찬가지로 loading.tsx가 최상단에 있을경우 rootLoading이 되고, 폴더내에 있을경우 부분적으로 적용이되며 root를 덮는다.<br>
<img width="662" alt="image" src="https://github.com/endmoseung/NextJSDocs/assets/103626175/80ff5efd-c54c-4171-a857-eb0e54bdef48">
<br>
### Streaming
**스트리밍을 사용하면 페이지의 HTML을 더 작은 청크로 분해**하고 해당 청크를 서버에서 클라이언트로 점진적으로 보낼 수 있습니다.<br>
<img width="643" alt="image" src="https://github.com/endmoseung/NextJSDocs/assets/103626175/a36039f2-77a5-4c1b-b828-caad36d6ac2f"> 
<br>
아래처럼 suspense를 넣어서 Streaming을 동작하고 수동으로 loading을 적용할 수 있다.

```tsx
import { Suspense } from 'react'
import { PostFeed, Weather } from './Components'
 
export default function Posts() {
  return (
    <section>
      <Suspense fallback={<p>Loading feed...</p>}>
        <PostFeed />
      </Suspense>
      <Suspense fallback={<p>Loading weather...</p>}>
        <Weather />
      </Suspense>
    </section>
  )
}
```

## Error handling
아래처럼 Error handling이 가능하다. error컴포넌트는 무조건 client 컴포넌틀가 돼야한다.<br>


```tsx
'use client' // Error components must be Client Components
 
import { useEffect } from 'react'
 
export default function Error({
  error,
  reset,
}: {
  error: Error
  reset: () => void
}) {
  useEffect(() => {
    // Log the error to an error reporting service
    console.error(error)
  }, [error])
 
  return (
    <div>
      <h2>Something went wrong!</h2>
      <button
        onClick={
          // Attempt to recover by trying to re-render the segment
          () => reset()
        }
      >
        Try again
      </button>
    </div>
  )
}
```
### nesting
loading과 layout과 같이 nesting이 적용되지만 rootError도 적용하고 싶다면 루트 디렉터리에 있는 error.js호출 의 변형을 사용해야한다 ``.app/global-error.jsapp``<br>

