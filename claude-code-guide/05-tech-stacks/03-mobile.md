# 모바일 기술스택 가이드

> Claude Code를 활용한 모바일 앱 개발 가이드입니다.
> React Native과 Flutter를 중심으로 다룹니다.

---

## 1. React Native

### CLAUDE.md 설정 예시

```markdown
# React Native 프로젝트

## 기술 스택
- React Native 0.74+ (New Architecture)
- TypeScript
- 내비게이션: React Navigation 6
- 상태 관리: Zustand + React Query
- 스타일링: StyleSheet (NativeWind 옵션)
- 테스트: Jest + React Native Testing Library

## 프로젝트 구조
src/
├── screens/         # 화면 컴포넌트
├── components/      # 재사용 UI 컴포넌트
├── navigation/      # 네비게이션 설정
├── hooks/           # 커스텀 훅
├── services/        # API 통신 계층
├── stores/          # 상태 관리
├── utils/           # 유틸리티
└── types/           # TypeScript 타입

## 코딩 규칙
- 함수형 컴포넌트 + 커스텀 훅 패턴
- Platform.select()으로 플랫폼별 스타일 분기
- 네이티브 모듈 접근은 services/ 계층을 통해
- 화면 컴포넌트는 비즈니스 로직 최소화

## 명령어
- iOS: npx react-native run-ios
- Android: npx react-native run-android
- 테스트: npm test
- 린트: npm run lint
- 타입 체크: npx tsc --noEmit
```

### 화면 및 컴포넌트 생성

**프롬프트:**
```
상품 목록 화면을 만들어줘.
- FlatList로 무한 스크롤
- 검색 바 (디바운스)
- 카테고리 필터 탭
- 당겨서 새로고침
- React Query로 데이터 페칭
```

**생성되는 코드 예시:**

```tsx
// src/screens/ProductListScreen.tsx
import React, { useState, useCallback } from 'react';
import { FlatList, RefreshControl, View, StyleSheet } from 'react-native';
import { useInfiniteQuery } from '@tanstack/react-query';
import { SearchBar } from '../components/SearchBar';
import { CategoryTabs } from '../components/CategoryTabs';
import { ProductCard } from '../components/ProductCard';
import { productApi } from '../services/api';
import { useDebounce } from '../hooks/useDebounce';

export function ProductListScreen() {
  const [search, setSearch] = useState('');
  const [category, setCategory] = useState<string | null>(null);
  const debouncedSearch = useDebounce(search, 300);

  const { data, fetchNextPage, hasNextPage, isRefetching, refetch } =
    useInfiniteQuery({
      queryKey: ['products', debouncedSearch, category],
      queryFn: ({ pageParam = 1 }) =>
        productApi.list({ page: pageParam, search: debouncedSearch, category }),
      getNextPageParam: (lastPage) => lastPage.nextPage,
    });

  const products = data?.pages.flatMap((page) => page.items) ?? [];

  return (
    <View style={styles.container}>
      <SearchBar value={search} onChangeText={setSearch} />
      <CategoryTabs selected={category} onSelect={setCategory} />
      <FlatList
        data={products}
        renderItem={({ item }) => <ProductCard product={item} />}
        keyExtractor={(item) => item.id}
        onEndReached={() => hasNextPage && fetchNextPage()}
        onEndReachedThreshold={0.5}
        refreshControl={
          <RefreshControl refreshing={isRefetching} onRefresh={refetch} />
        }
      />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#fff' },
});
```

### 네이티브 모듈 연동

**프롬프트:**
```
카메라로 바코드를 스캔하는 기능을 추가해줘.
- react-native-camera 또는 expo-camera 사용
- 스캔 결과로 상품 검색 API 호출
- 진동 피드백
```

### 테스트 작성

```tsx
import { render, screen, fireEvent, waitFor } from '@testing-library/react-native';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ProductListScreen } from './ProductListScreen';

const queryClient = new QueryClient({
  defaultOptions: { queries: { retry: false } },
});

const wrapper = ({ children }: { children: React.ReactNode }) => (
  <QueryClientProvider client={queryClient}>{children}</QueryClientProvider>
);

describe('ProductListScreen', () => {
  it('상품 목록을 렌더링한다', async () => {
    render(<ProductListScreen />, { wrapper });
    await waitFor(() => {
      expect(screen.getByText('테스트 상품')).toBeTruthy();
    });
  });
});
```

### 빌드 및 배포

**프롬프트:**
```
EAS Build를 사용한 CI/CD 파이프라인을 설정해줘.
- PR: 타입 체크 + 테스트
- main 머지: iOS/Android 빌드 + TestFlight/Play Console 배포
```

---

## 2. Flutter

### CLAUDE.md 설정 예시

```markdown
# Flutter 프로젝트

## 기술 스택
- Flutter 3.22+ / Dart 3.4+
- 상태 관리: Riverpod 2
- 라우팅: go_router
- HTTP: dio
- 로컬 저장: Hive / SharedPreferences
- 테스트: flutter_test + mockito

## 프로젝트 구조
lib/
├── features/        # 기능별 모듈
│   ├── auth/
│   ├── home/
│   └── product/
├── core/            # 공통 코드
│   ├── theme/
│   ├── router/
│   └── network/
├── models/          # 데이터 모델 (freezed)
└── main.dart

## 코딩 규칙
- Feature-first 구조
- Riverpod provider로 상태 관리
- freezed + json_serializable로 모델 생성
- Widget은 작게 분리, 재사용성 극대화

## 명령어
- 실행: flutter run
- 테스트: flutter test
- 빌드 (Android): flutter build apk
- 빌드 (iOS): flutter build ipa
- 코드 생성: dart run build_runner build
- 분석: flutter analyze
```

### 위젯 생성 예제

**프롬프트:**
```
상품 상세 화면을 만들어줘.
- 이미지 슬라이더 (PageView)
- 가격, 설명, 옵션 선택
- 장바구니 담기 버튼 (하단 고정)
- Riverpod으로 상태 관리
```

**생성되는 코드 예시:**

```dart
// lib/features/product/presentation/product_detail_screen.dart
import 'package:flutter/material.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import '../providers/product_provider.dart';

class ProductDetailScreen extends ConsumerWidget {
  final String productId;

  const ProductDetailScreen({super.key, required this.productId});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final productAsync = ref.watch(productProvider(productId));

    return productAsync.when(
      data: (product) => Scaffold(
        body: CustomScrollView(
          slivers: [
            SliverAppBar(
              expandedHeight: 300,
              flexibleSpace: FlexibleSpaceBar(
                background: PageView.builder(
                  itemCount: product.images.length,
                  itemBuilder: (context, index) => Image.network(
                    product.images[index],
                    fit: BoxFit.cover,
                  ),
                ),
              ),
            ),
            SliverToBoxAdapter(
              child: Padding(
                padding: const EdgeInsets.all(16),
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Text(product.name, style: Theme.of(context).textTheme.headlineSmall),
                    const SizedBox(height: 8),
                    Text('₩${product.price.toStringAsFixed(0)}',
                      style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold)),
                    const SizedBox(height: 16),
                    Text(product.description),
                  ],
                ),
              ),
            ),
          ],
        ),
        bottomNavigationBar: SafeArea(
          child: Padding(
            padding: const EdgeInsets.all(16),
            child: ElevatedButton(
              onPressed: () => ref.read(cartProvider.notifier).addItem(product),
              child: const Text('장바구니 담기'),
            ),
          ),
        ),
      ),
      loading: () => const Scaffold(body: Center(child: CircularProgressIndicator())),
      error: (e, _) => Scaffold(body: Center(child: Text('오류: $e'))),
    );
  }
}
```

### 모델 생성 (freezed)

**프롬프트:**
```
상품 모델을 freezed로 만들어줘.
- id, name, price, description, images, category, options
- JSON 직렬화 포함
```

```dart
// lib/models/product.dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'product.freezed.dart';
part 'product.g.dart';

@freezed
class Product with _$Product {
  const factory Product({
    required String id,
    required String name,
    required int price,
    required String description,
    required List<String> images,
    required String category,
    @Default([]) List<ProductOption> options,
  }) = _Product;

  factory Product.fromJson(Map<String, dynamic> json) => _$ProductFromJson(json);
}

@freezed
class ProductOption with _$ProductOption {
  const factory ProductOption({
    required String name,
    required List<String> values,
  }) = _ProductOption;

  factory ProductOption.fromJson(Map<String, dynamic> json) =>
      _$ProductOptionFromJson(json);
}
```

### 테스트 작성

```dart
import 'package:flutter_test/flutter_test.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:myapp/features/product/presentation/product_detail_screen.dart';

void main() {
  testWidgets('상품 상세 화면이 올바르게 렌더링된다', (tester) async {
    await tester.pumpWidget(
      ProviderScope(
        overrides: [
          productProvider('1').overrideWith((ref) => AsyncValue.data(mockProduct)),
        ],
        child: const MaterialApp(
          home: ProductDetailScreen(productId: '1'),
        ),
      ),
    );

    expect(find.text('테스트 상품'), findsOneWidget);
    expect(find.text('장바구니 담기'), findsOneWidget);
  });
}
```

---

## 3. 크로스 플랫폼 공통 고려사항

### 플랫폼별 분기 처리

**프롬프트:**
```
iOS와 Android에서 다르게 동작하는 날짜 선택 UI를 만들어줘.
- iOS: CupertinoDatePicker 스타일
- Android: Material DatePicker 스타일
```

### 딥링크 설정

**프롬프트:**
```
딥링크를 설정해줘.
- 스킴: myapp://
- 유니버설 링크: https://myapp.com/product/{id}
- iOS, Android 설정 파일 모두 포함
```

---

## 4. 팁과 주의사항

| 상황 | 팁 |
|------|-----|
| 네이티브 코드 수정 | Claude에게 플랫폼(iOS/Android)을 명시 |
| 상태 관리 선택 | CLAUDE.md에 사용 중인 상태 관리 라이브러리 명시 |
| 성능 최적화 | "이 리스트의 렌더링 성능을 최적화해줘" 요청 |
| 앱 크기 줄이기 | "번들 크기를 분석하고 줄일 방법을 알려줘" |
| 테스트 어려움 | 위젯 테스트 → 통합 테스트 순서로 요청 |

> **다음 단계:** [인프라 기술스택 가이드](./04-infra.md)로 이동하세요.
