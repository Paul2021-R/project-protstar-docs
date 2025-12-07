# 2025-12-01 작업 내역: 프론트엔드 아키텍처 및 레이아웃 고도화

금일 작업은 단순한 UI 수정을 넘어, **확장 가능하고 유지보수가 용이한 프론트엔드 아키텍처**를 수립하는 데 초점을 맞추었다. 프론트엔드 개발이 익숙하지 않은 분들도 이해할 수 있도록, 사용된 핵심 개념과 해당 코드가 위치한 파일을 매핑하여 정리했다.

## 1. 프로젝트 구조 (Project Structure)

`src` 디렉토리를 중심으로 기능별로 명확하게 분리된 구조를 채택했다.

```plaintext
src
├── app
│   ├── (public)       # 로그인 불필요 페이지 (Landing, Login, Register)
│   │   ├── layout.tsx # Header + Footer 레이아웃
│   │   └── ...
│   ├── (protected)    # 로그인 필요 페이지 (Dashboard, Profile)
│   │   ├── layout.tsx # Sidebar + Header 레이아웃
│   │   └── ...
│   ├── layout.tsx     # Root Layout (Global Styles, Fonts)
│   └── globals.css
├── components
│   ├── ui             # shadcn/ui 기본 컴포넌트 (Sidebar, Button, Card 등)
│   ├── layout         # 레이아웃 구성요소 (AppSidebar, Header, PageTitle)
│   └── features       # 기능별 컴포넌트 (ChatInterface)
├── lib
│   ├── store.ts       # Zustand 상태 관리 (Auth, UI)
│   └── utils.ts
└── services
    ├── api.ts         # API 인터페이스 정의
    └── mock           # Mock 데이터 및 서비스 (Auth, Chat)
```

## 2. 핵심 프론트엔드 개념 및 구현 (Key Concepts & Implementation)

이 프로젝트에 적용된 주요 기술 개념과, 실제 코드가 어디에 있는지 설명한다.

### 2.1. Route Groups (라우트 그룹)

* **개념**: 폴더 이름에 괄호 `( )`를 치면 URL 경로에는 포함되지 않지만, **레이아웃을 분리**하는 용도로 사용할 수 있다. "URL은 깔끔하게 유지하되, 디자인은 다르게 가고 싶을 때" 사용한다.
* **구현 파일**:
  * `src/app/(public)/layout.tsx`: 상단 헤더와 하단 푸터만 있는 레이아웃.
  * `src/app/(protected)/layout.tsx`: 좌측 사이드바가 포함된 레이아웃.
* **효과**: `/login` (public)과 `/dashboard` (protected)가 서로 완전히 다른 화면 구성을 가지게 되었다.

### 2.2. Server Component vs Client Component

* **개념**: Next.js 15는 기본적으로 모든 컴포넌트를 서버에서 미리 렌더링(Server Component)하여 성능을 높인다. 하지만 클릭 이벤트(`onClick`), 상태 관리(`useState`) 등 **사용자 상호작용**이 필요한 곳에는 `'use client'`를 명시해야 한다.
* **구현 파일**:
  * `src/app/(public)/page.tsx`: 단순 정보 전달 페이지이므로 **Server Component** (기본값).
  * `src/components/features/chat/ChatInterface.tsx`: 채팅 입력, 전송 버튼 클릭 등 상호작용이 많으므로 **Client Component** (`'use client'` 명시).

### 2.3. Dynamic Routing (동적 라우팅)

* **개념**: URL의 일부가 변수처럼 변할 때 대괄호 `[ ]`를 사용한다.
* **구현 파일**:
  * `src/app/(protected)/dashboard/chat/[id]/page.tsx`: `/dashboard/chat/1`, `/dashboard/chat/2` 등 채팅방 ID에 따라 다른 내용을 보여준다.
  * 코드 내용: `params`를 통해 URL에 있는 `id` 값을 받아와서, 해당 채팅방의 대화 내용을 불러온다.

### 2.4. Zustand (전역 상태 관리)

* **개념**: 리액트의 기본 데이터 흐름은 부모 -> 자식으로만 흐르지만, **로그인 정보**나 **사이드바 열림/닫힘** 같은 정보는 앱 전체에서 필요하다. 이를 위해 사용하는 아주 가볍고 쉬운 도구다.
* **구현 파일**:
  * `src/lib/store.ts`: `useAuthStore`(로그인 정보), `useUIStore`(UI 상태)를 정의한 곳.
  * 사용 예시: "로그인했니?"를 확인하기 위해 여러 컴포넌트에서 `useAuthStore`를 가져다 쓴다.

### 2.5. shadcn/ui & Tailwind CSS (스타일링)

* **개념**:
  * **Tailwind CSS**: `class="flex p-4 bg-red-500"` 처럼 미리 정의된 클래스 이름을 조합해서 스타일을 입히는 방식. CSS 파일을 따로 만들지 않아도 돼서 빠르다.
  * **shadcn/ui**: Tailwind CSS로 만들어진, 복사해서 쓰는 고품질 컴포넌트 모음.
  * **cn() 함수**: 조건에 따라 클래스 이름을 합치거나 뺄 때 쓰는 유틸리티 함수.
* **구현 파일**:
  * `src/components/ui/sidebar.tsx`: `cn()`을 사용하여 모바일인지 PC인지에 따라 사이드바의 너비(`w-64` vs `w-full`)를 다르게 적용했다.

## 3. 사이드바 및 반응형 로직 상세

### 3.1. 모바일 (Mobile, < 1024px)

* **전체 화면 오버레이**: 좁은 화면에서는 사이드바가 화면 전체(`w-[100vw]`)를 덮는다.
* **Backdrop**: 사이드바 뒤에 검은 반투명 배경(`bg-black/50`)을 깔아 뒤쪽 본문을 가린다.

### 3.2. PC/태블릿 (Desktop, >= 1024px)

* **Overlay Behavior**: 사이드바가 펼쳐져도 본문이 밀려나지 않고(Shift X), 본문 위로 뜬다(Overlay).
* **Fixed Spacer**: `SidebarInset` 옆에 투명한 공간(Spacer)을 항상 '아이콘 너비'만큼만 잡아두어, 사이드바가 커져도 본문 위치는 그대로 유지된다.

## 4. 결론

이 프로젝트는 **Next.js의 최신 기능(App Router)**과 **실용적인 라이브러리(Zustand, Tailwind)**를 조합하여, 복잡한 요구사항(반응형 사이드바)을 깔끔하게 해결했다. 이 문서는 향후 개발자가 코드를 수정할 때 "이 코드가 왜 여기에 있는지"를 알려주는 지도가 될 것이다.
