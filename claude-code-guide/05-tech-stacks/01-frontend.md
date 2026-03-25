# 프론트엔드 기술스택 가이드

> Claude Code를 활용한 프론트엔드 개발 가이드입니다.
> React, Vue, Angular, Next.js 등 주요 프레임워크별 활용법을 다룹니다.

---

## 1. React

### CLAUDE.md 설정 예시

```markdown
# React 프로젝트 컨벤션

## 기술 스택
- React 18 + TypeScript
- 상태 관리: Zustand
- 스타일링: Tailwind CSS
- 테스트: Jest + React Testing Library
- 빌드: Vite

## 프로젝트 구조
src/
├── components/    # 재사용 컴포넌트 (PascalCase)
├── pages/         # 페이지 컴포넌트
├── hooks/         # 커스텀 훅 (use 접두사)
├── stores/        # Zustand 스토어
├── types/         # TypeScript 타입 정의
├── utils/         # 유틸리티 함수
└── api/           # API 호출 함수

## 코딩 규칙
- 함수형 컴포넌트만 사용 (클래스 컴포넌트 금지)
- Props는 interface로 정의, ComponentNameProps 네이밍
- 커스텀 훅은 반드시 hooks/ 디렉토리에 배치
- CSS 클래스는 Tailwind 유틸리티 클래스 사용

## 명령어
- 개발: npm run dev
- 빌드: npm run build
- 테스트: npm run test
- 린트: npm run lint
```

### 컴포넌트 생성 예제

**프롬프트:**
```
사용자 프로필 카드 컴포넌트를 만들어줘.
- 아바타 이미지, 이름, 이메일, 역할 표시
- 편집 버튼 클릭 시 모달 열기
- 반응형 디자인 (모바일/데스크톱)
- Tailwind CSS 사용
```

**Claude의 행동:**
1. `src/components/UserProfileCard.tsx` 생성
2. `src/types/user.ts`에 타입 정의 추가
3. Props interface 정의
4. 반응형 Tailwind 클래스 적용

**생성되는 코드 예시:**
```tsx
// src/types/user.ts
export interface User {
  id: string;
  name: string;
  email: string;
  role: 'admin' | 'user' | 'editor';
  avatarUrl: string;
}

// src/components/UserProfileCard.tsx
interface UserProfileCardProps {
  user: User;
  onEdit: (user: User) => void;
}

export function UserProfileCard({ user, onEdit }: UserProfileCardProps) {
  return (
    <div className="rounded-lg border p-4 shadow-sm md:p-6">
      <img
        src={user.avatarUrl}
        alt={user.name}
        className="h-16 w-16 rounded-full md:h-24 md:w-24"
      />
      <h3 className="mt-2 text-lg font-semibold">{user.name}</h3>
      <p className="text-sm text-gray-500">{user.email}</p>
      <span className="mt-1 inline-block rounded-full bg-blue-100 px-2 py-1 text-xs">
        {user.role}
      </span>
      <button
        onClick={() => onEdit(user)}
        className="mt-4 w-full rounded bg-blue-500 px-4 py-2 text-white hover:bg-blue-600"
      >
        편집
      </button>
    </div>
  );
}
```

### 테스트 작성 예제

**프롬프트:**
```
UserProfileCard 컴포넌트의 테스트를 작성해줘.
렌더링, 클릭 이벤트, 역할별 표시를 테스트해야 해.
```

**생성되는 테스트:**
```tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { UserProfileCard } from './UserProfileCard';

const mockUser = {
  id: '1',
  name: '홍길동',
  email: 'hong@example.com',
  role: 'admin' as const,
  avatarUrl: '/avatar.jpg',
};

describe('UserProfileCard', () => {
  it('사용자 정보를 올바르게 렌더링한다', () => {
    render(<UserProfileCard user={mockUser} onEdit={jest.fn()} />);
    expect(screen.getByText('홍길동')).toBeInTheDocument();
    expect(screen.getByText('hong@example.com')).toBeInTheDocument();
    expect(screen.getByText('admin')).toBeInTheDocument();
  });

  it('편집 버튼 클릭 시 onEdit이 호출된다', () => {
    const onEdit = jest.fn();
    render(<UserProfileCard user={mockUser} onEdit={onEdit} />);
    fireEvent.click(screen.getByText('편집'));
    expect(onEdit).toHaveBeenCalledWith(mockUser);
  });
});
```

### 빌드/배포 자동화

**프롬프트:**
```
이 React 프로젝트의 GitHub Actions CI/CD 파이프라인을 만들어줘.
- PR 시: 린트, 테스트, 빌드 검증
- main 머지 시: Vercel 배포
```

---

## 2. Vue

### CLAUDE.md 설정 예시

```markdown
# Vue 프로젝트 컨벤션

## 기술 스택
- Vue 3 + TypeScript + Composition API
- 상태 관리: Pinia
- 라우팅: Vue Router
- 테스트: Vitest + Vue Test Utils
- 스타일링: SCSS Modules

## 코딩 규칙
- <script setup lang="ts"> 사용
- 컴포넌트: PascalCase 파일명
- Composable: use 접두사 (useAuth, useForm 등)
- defineProps/defineEmits로 타입 안전한 props/events

## 명령어
- 개발: npm run dev
- 빌드: npm run build
- 테스트: npx vitest
- 린트: npm run lint
```

### 컴포넌트 생성 예제

**프롬프트:**
```
Vue 3 Composition API로 검색 가능한 드롭다운 컴포넌트를 만들어줘.
- v-model 지원
- 키보드 탐색 (위/아래 화살표, Enter)
- 디바운스된 검색
```

**생성 코드 예시:**
```vue
<script setup lang="ts">
import { ref, computed, watch } from 'vue';

interface Option {
  value: string;
  label: string;
}

interface Props {
  options: Option[];
  modelValue: string;
  placeholder?: string;
}

const props = withDefaults(defineProps<Props>(), {
  placeholder: '선택하세요',
});
const emit = defineEmits<{
  'update:modelValue': [value: string];
}>();

const searchQuery = ref('');
const isOpen = ref(false);
const activeIndex = ref(-1);

const filteredOptions = computed(() =>
  props.options.filter((opt) =>
    opt.label.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
);

function selectOption(option: Option) {
  emit('update:modelValue', option.value);
  isOpen.value = false;
  searchQuery.value = '';
}
</script>
```

### 테스트 작성

**프롬프트:**
```
SearchDropdown 컴포넌트의 Vitest 테스트를 작성해줘.
```

```ts
import { mount } from '@vue/test-utils';
import { describe, it, expect } from 'vitest';
import SearchDropdown from './SearchDropdown.vue';

describe('SearchDropdown', () => {
  const options = [
    { value: 'kr', label: '한국' },
    { value: 'us', label: '미국' },
    { value: 'jp', label: '일본' },
  ];

  it('옵션 목록을 렌더링한다', async () => {
    const wrapper = mount(SearchDropdown, {
      props: { options, modelValue: '' },
    });
    await wrapper.find('input').trigger('focus');
    expect(wrapper.findAll('li')).toHaveLength(3);
  });

  it('검색어로 필터링한다', async () => {
    const wrapper = mount(SearchDropdown, {
      props: { options, modelValue: '' },
    });
    await wrapper.find('input').setValue('한');
    expect(wrapper.findAll('li')).toHaveLength(1);
  });
});
```

---

## 3. Angular

### CLAUDE.md 설정 예시

```markdown
# Angular 프로젝트 컨벤션

## 기술 스택
- Angular 17+ (Standalone Components)
- 상태 관리: NgRx Signals
- 테스트: Jasmine + Karma
- 스타일링: Angular Material + SCSS

## 코딩 규칙
- Standalone 컴포넌트 사용 (NgModule 최소화)
- Signal 기반 반응성 패턴 사용
- inject() 함수로 DI (constructor 주입 지양)
- OnPush 변경 감지 전략 기본 사용

## 명령어
- 개발: ng serve
- 빌드: ng build --configuration production
- 테스트: ng test
- E2E: ng e2e
```

### 컴포넌트 생성 예제

**프롬프트:**
```
Angular Standalone 컴포넌트로 데이터 테이블을 만들어줘.
- 정렬, 페이지네이션, 검색 기능
- Angular Material 테이블 사용
- Signal 기반 상태 관리
```

### 서비스 생성 예제

**프롬프트:**
```
사용자 인증 서비스를 만들어줘.
- 로그인/로그아웃/토큰 갱신
- HttpInterceptor로 자동 토큰 첨부
- Guard로 라우트 보호
```

---

## 4. Next.js

### CLAUDE.md 설정 예시

```markdown
# Next.js 프로젝트 컨벤션

## 기술 스택
- Next.js 14+ (App Router)
- TypeScript
- Prisma ORM + PostgreSQL
- NextAuth.js 인증
- Tailwind CSS

## 프로젝트 구조
app/
├── (auth)/        # 인증 관련 라우트 그룹
├── (dashboard)/   # 대시보드 라우트 그룹
├── api/           # API Route Handlers
└── layout.tsx     # 루트 레이아웃

## 코딩 규칙
- 서버 컴포넌트 기본, 클라이언트는 'use client' 명시
- 데이터 페칭: 서버 컴포넌트에서 직접 fetch
- API Routes: app/api/ 디렉토리의 Route Handlers 사용
- 메타데이터: generateMetadata 함수 활용

## 명령어
- 개발: npm run dev
- 빌드: npm run build
- DB: npx prisma migrate dev
- 시드: npx prisma db seed
```

### 페이지 및 API 라우트 생성

**프롬프트:**
```
블로그 게시물 CRUD를 구현해줘.
- app/blog/page.tsx: 게시물 목록 (서버 컴포넌트)
- app/blog/[slug]/page.tsx: 게시물 상세
- app/api/posts/route.ts: REST API
- Prisma 모델 포함
```

**생성되는 코드 예시:**
```ts
// app/api/posts/route.ts
import { NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';

export async function GET() {
  const posts = await prisma.post.findMany({
    orderBy: { createdAt: 'desc' },
    include: { author: { select: { name: true } } },
  });
  return NextResponse.json(posts);
}

export async function POST(request: Request) {
  const body = await request.json();
  const post = await prisma.post.create({ data: body });
  return NextResponse.json(post, { status: 201 });
}
```

```tsx
// app/blog/page.tsx (서버 컴포넌트)
import { prisma } from '@/lib/prisma';
import Link from 'next/link';

export default async function BlogPage() {
  const posts = await prisma.post.findMany({
    orderBy: { createdAt: 'desc' },
  });

  return (
    <main className="mx-auto max-w-4xl p-8">
      <h1 className="text-3xl font-bold">블로그</h1>
      <ul className="mt-6 space-y-4">
        {posts.map((post) => (
          <li key={post.id}>
            <Link href={`/blog/${post.slug}`} className="text-blue-600 hover:underline">
              {post.title}
            </Link>
            <p className="text-sm text-gray-500">{post.createdAt.toLocaleDateString('ko-KR')}</p>
          </li>
        ))}
      </ul>
    </main>
  );
}
```

---

## 5. 공통 프론트엔드 프롬프트 패턴

### 성능 최적화

```
이 컴포넌트의 리렌더링을 최적화해줘.
- 불필요한 리렌더링 원인 분석
- React.memo, useMemo, useCallback 적절히 적용
- React DevTools Profiler 결과 기반 개선
```

### 접근성(a11y) 개선

```
이 폼 컴포넌트의 접근성을 개선해줘.
- ARIA 속성 추가
- 키보드 네비게이션 지원
- 스크린 리더 호환성
- WCAG 2.1 AA 기준 충족
```

### 반응형 디자인

```
이 대시보드 레이아웃을 반응형으로 만들어줘.
- 모바일: 단일 컬럼, 햄버거 메뉴
- 태블릿: 2컬럼 그리드
- 데스크톱: 사이드바 + 메인 컨텐츠
- Tailwind 브레이크포인트 활용
```

---

## 6. 팁과 주의사항

| 상황 | 팁 |
|------|-----|
| 대규모 컴포넌트 리팩토링 | 한 번에 하나의 컴포넌트씩 요청 |
| 스타일링 일관성 | CLAUDE.md에 디자인 토큰/컬러 팔레트 명시 |
| 상태 관리 선택 | 프로젝트 규모에 맞는 도구 CLAUDE.md에 지정 |
| 테스트 커버리지 | "이 컴포넌트의 테스트 커버리지를 90% 이상으로" 요청 |
| 번들 크기 | "번들 사이즈를 분석하고 최적화 방안을 제시해줘" |

> **다음 단계:** [백엔드 기술스택 가이드](./02-backend.md)로 이동하여 서버 측 개발 활용법을 학습하세요.
