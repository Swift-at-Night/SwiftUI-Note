# SwiftUI-Note

![SwiftUI](https://img.shields.io/badge/SwiftUI-blue?style=for-the-badge&logo=swift&logoColor=white)

SwiftUI 에 대한 내용을 끄적인 문서입니다.

## List
### List 구분선(Separator) 제거하기

![iOS 13](https://img.shields.io/badge/iOS_13-999999?style=for-the-badge&logo=apple&logoColor=white)
![iOS 14](https://img.shields.io/badge/iOS_14-e4405f?style=for-the-badge&logo=apple&logoColor=white)

#### iOS14
`ScrollView` 안에 `LazyVStack` 을 사용하여 모든 구분선를 제거할 수 있습니다
기본적으로 리스트 아래에 추가적인 구분선들이 존재하지 않습니다(iOS13은 data와 무관하게 구분선들이 기본으로 그려졌음)

#### iOS13
`UITableView.appearance().separatorStyle = .none` 을 사용해 구분선을 제거할 수 있습니다.
```swift
// 1. onAppear에서 호출하기
List {
   ...
}
.onAppear {
   UITableView.appearance().separatorStyle = .none
}

// 2. init에서 호출하기
init() {
    UITableView.appearance().separatorStyle = .none
}
```

## ScrollView

### Horizontal ScrollView 만들기

1. 각 `data`를 `ForEach` 로 감싸서 각 `data`들에 대한 뷰를 그립니다
2. `ForEach`를 `HStack`으로 감싸서 가로로 배열되게 합니다
3. `HStack`을 `ScrollView(.horizontal)`로 감싸면 됩니다.

```swift
// 1. onAppear에서 호출하기
ScrollView(.horizontal) {
   HStack {
        ForEach(foods, id: \.name) { foods in
            Text(food.name)
        }
    }
}
```

### ScrollView 의 스크롤바 제거하기
`ScrollView`의 `showsIndicators` 값을 `false` 로 세팅합니다.

```swift
// 1. onAppear에서 호출하기
ScrollView(showsIndicators: false) {
   ...
}
```
