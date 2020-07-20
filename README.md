# SwiftUI-Note

![SwiftUI](https://img.shields.io/badge/SwiftUI-blue?style=for-the-badge&logo=swift&logoColor=white)

SwiftUI 에 대한 내용을 끄적인 문서입니다.

## Image

### NavigationLink 또는 Button에서 사용되는 이미지가 파란색일 때

`NavigationLink` 나 `Button` 내부에서 쓰는 이미지는 파란색으로 덮어씌워지게 됩니다. 이를 해결하는 방법은 두가지가 있습니다.

#### PlainButtonStyle()

<details>

```swift
NavigationLink(destination: ...) {
    Image(...)
}
.buttonStyle(PlainButtonStyle())


Button(action: ...) {
    Image(...)
}
.buttonStyle(PlainButtonStyle())
```

</details>

#### renderingMode()

<details>

```swift
NavigationLink(destination: ...) {
    Image(...)
        .renderingMode(.original)
}
```

</details>


## List

### List 에서 id 파라미터 생략하기

data 로 사용하는 `struct`가 `Identifiable` 를 준수하면 List에서 id를 명시하지 않아도 됩니다.

```swift
struct Food: Identifiable {
    let id = UUID()
    ...
}
```

#### Before

<details>

```swift
List(foods, id: \.name) { ... }
```

</details>

#### After

```swift
List(foods) { ... }
```

### List 구분선(Separator) 제거하기

![iOS 13](https://img.shields.io/badge/iOS_13-999999?style=for-the-badge&logo=apple&logoColor=white)
![iOS 14](https://img.shields.io/badge/iOS_14-e4405f?style=for-the-badge&logo=apple&logoColor=white)

#### iOS14

`ScrollView` 안에 `LazyVStack` 을 사용하여 모든 구분선를 제거할 수 있습니다.

또한 기본적으로 리스트 아래에 추가적인 구분선들이 존재하지 않습니다(iOS13은 data와 무관하게 구분선들이 기본으로 그려졌음)

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

## ZStack

### 밑에서 올라오는 뷰 만들기
`State` 와 `withAnimation`, 그리고 `offset` 을 사용하여 밑에서 올라오는 뷰를 만들 수 있습니다.

<details>
    
```swift
struct BelowView: View {
    @State var showingUpper: Bool = false   // 1. 뷰를 올릴지 내릴지 결정할 State 값 
    
    var body: some View {
        ZStack {
            Text("Show upper view")
                .onTapGesture {             // 2. "보여주기"를 탭했을 때 State 값을 true로 변경
                    withAnimation {
                        showingUpper = true
                    }
                }
            
            UpperView(showingUpper: $showingUpper)  // 3. 위로 올라올 뷰는 State 값을 바인딩
                // 4. 뷰 오프셋의 y값을 State 값에 따라 바뀌도로 함 (true이면 y = 0, false 이면 y = 스크린 높이)
                .offset(x: 0, y: showingUpper ? 0 : UIScreen.main.bounds.height)
        }
    }
}

struct UpperView: View {
    @Binding var showingUpper: Bool         // 5. (3)번에서 바인딩한 프로퍼티
    
    var body: some View {
        Text("Hide upper view")
            .onTapGesture {                 // 6. 바인딩 값을 false로 변경하여  (4)번의 오프셋 값으 변경
                withAnimation {
                    showingUpper = false
                }
            }
    }
}
```

</details>
