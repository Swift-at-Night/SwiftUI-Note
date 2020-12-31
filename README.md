# SwiftUI-Note

![SwiftUI](https://img.shields.io/badge/SwiftUI-blue?style=for-the-badge&logo=swift&logoColor=white)

SwiftUI 에 대한 내용을 끄적인 문서입니다.

## Color

### 그라데이션이 적용된 뷰

`LinearGradient(gradient:startPoint:endPoint)` 를 사용해 그라데이션 뷰를 생성할 수 있습니다.

<details>

```swift
LinearGradient(gradient: Gradient(colors: [.green, .purple]), 
               startPoint: .leading, 
               endPoint: .trailing)
```

</details>

## Toggle

### 토글 스위치 색 변경하기

`SwitchToggleStyle(tint:)` 를 `toggleStyle(_:)` modifier에 적용하여 원하는 색으로 변경할 수 있다.

<details>

```swift
Toggle("Blue", isOn: $isOn)
  .toggleStyle(SwitchToggleStyle(tint: .blue))
```

</details>

## Button

### 버튼 스타일 커스터마이징 하기

`ButtonStyle` 을 준수하는 `struct` 를 생성하고 `makeBody(configuration:)` 델리게이트 메소드를 구현하여 버튼 스타일을 커스터마이징 할 수 있습니다.

<details>

```swift
// 배경에 그라데이션이 적용된 스타일
struct LinearGradientStyle: ButtonStyle {
 
    func makeBody(configuration: Self.Configuration) -> some View {
        configuration.label
            .foregroundColor(.white)
            .frame(minWidth: 0, maxWidth: .infinity)
            .padding()
            .background(
                LinearGradient(gradient: Gradient(colors: [.green, .purple]), startPoint: .leading, endPoint: .trailing)
            )
            .cornerRadius(40)
            .padding(.horizontal, 20)
    }
}
```

```swift
// 사용 예시
Button(action: ...) {
    ...
}
.buttonStyle(LinearGradientStyle())
```

</details>

### 둥근 테두리

`RoundedRectangle` 뷰에 `stroke` 를 적용하면 둥근 테두리를 구현할 수 있습니다.

<details>
  
  ```swift
  RoundedRectangle(cornerRadius: 28)
      .stroke(Color.red, lineWidth: 2)
  ```

</details>

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

<details>

```swift
struct Food: Identifiable {
    let id = UUID()
    ...
}
```

</details>

#### Before

<details>

```swift
List(foods, id: \.name) { ... }
```

</details>

#### After

<details>

```swift
List(foods) { ... }
```

</details>

### List 구분선(Separator) 제거하기

![iOS 13](https://img.shields.io/badge/iOS_13-999999?style=for-the-badge&logo=apple&logoColor=white)
![iOS 14](https://img.shields.io/badge/iOS_14-e4405f?style=for-the-badge&logo=apple&logoColor=white)

#### iOS14

`ScrollView` 안에 `LazyVStack` 을 사용하여 모든 구분선를 제거할 수 있습니다.

또한 기본적으로 리스트 아래에 추가적인 구분선들이 존재하지 않습니다(iOS13은 data와 무관하게 구분선들이 기본으로 그려졌음)

<details>
  
```swift
ScrollView {
    LazyVStack {
        ForEach(items) { item in
            ItemRow(item: item)
        }
    }
}
```

</details>

#### iOS13
`UITableView.appearance().separatorStyle = .none` 을 사용해 구분선을 제거할 수 있습니다.

<details>

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

</details>

## ScrollView

### Horizontal ScrollView 만들기

1. 각 `data`를 `ForEach` 로 감싸서 각 `data`들에 대한 뷰를 그립니다
2. `ForEach`를 `HStack`으로 감싸서 가로로 배열되게 합니다
3. `HStack`을 `ScrollView(.horizontal)`로 감싸면 됩니다.

<details>

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

</details>

### ScrollView 의 스크롤바 제거하기
`ScrollView`의 `showsIndicators` 값을 `false` 로 세팅합니다.

<details>
    
```swift
// 1. onAppear에서 호출하기
ScrollView(showsIndicators: false) {
   ...
}
```

</details>

## 뷰 전환

### 전체화면 형태의 모달 뷰(밑에서 올라오는 뷰) 만들기

![iOS 14](https://img.shields.io/badge/iOS_14-e4405f?style=for-the-badge&logo=apple&logoColor=white)

`View` 에 `fullScreenCover()` 를 사용하여 전체화면의 뷰를 밑에서 올라오도록 할 수 있습니다.

<details>

```swift
struct BelowView: View {
    @State private var isPresented = false

    var body: some View {
        Button("뷰 띄우기") {
            self.isPresented.toggle()
        }
        .fullScreenCover(isPresented: $isPresented, content: UpperView.init)
    }
}
```

```swift
struct FullScreenModalView: View {
    @Environment(\.presentationMode) var presentationMode
    
    var body: some View {
        Button("돌아가기") {
            presentationMode.wrappedValue.dismiss()
        }
    }
}
```

</details>

![iOS 13](https://img.shields.io/badge/iOS_13-999999?style=for-the-badge&logo=apple&logoColor=white)

`ZStack`, `State` 와 `withAnimation`, 그리고 `offset` 을 사용하여 밑에서 올라오는 뷰를 만들 수 있습니다.

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
```

```swift
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

## Text

### 한 개의 builder 안에 최대 몇 개까지 들어갈까?
`VStack` 기준, 10개를 넘기면 에러가 발생합니다: `Extra argument in call`

## App

### AppDelegate 메소드 사용하기

![iOS 14](https://img.shields.io/badge/iOS_14-e4405f?style=for-the-badge&logo=apple&logoColor=white)

`UIApplicationDelegate` 를 준수하는 클래스를 `App`에 `@UIApplicationDelegateAdaptor(클래스타입)` 속성에 해당 클래스 타입을 명시하여 프로퍼티를 선언해주면 됩니다.

<details>
  
  ```swift
  @UIApplicationDelegateAdaptor(AppDelegate.self) var appDelegate
  ```
  
  ```swift
  class AppDelegate: NSObject, UIApplicationDelegate {
      func application(..., didFinishLaunchingWithOptions...) {
          ...
      }
  }
  ```
  
</details>

## State & Data Flows

### State

`State` 속성은 현재 뷰 내에 선언된 프로퍼티가 만약 값이 변경될 때(Mutating) 뷰에 영향(Updating view)을 줄 때 사용합니다.
이 속성을 사용하게 되면 해당 프로퍼티는 SwiftUI 프레임워크에 의해 관리됩니다. (뷰 라이프사이클에 의존하지 않음)
예) 타이머 숫자, 이름을 입력받는 텍스트 필드

이 속성으로 선언된 프로퍼티의 데이터의 값 변화를 전달받기 위해서는 `$` (바인딩) 을 사용하면 됩니다.

> **개인 의견**
> 뷰모델을 사용하는 경우 `@State` 속성의 프로퍼티는 가급적 뷰모델 내부에 `@Published` 로 옮기는 것이 좋습니다.
> `$username` 을 `$viewModel.username` 형태로 동일하게 바인딩할 수 있습니다.

<details>
  
  ```swift
  @State private var username: String = ""
  ```
  
  ```swift
  TextField("Enter your name", text: $username)
  ```
  
</details>

### Binding

`Binding` 속성은 `State` 속성으로 선언된 프로퍼티의 데이터의 값 변화(mutating)을 전달받기 위해 사용합니다.

<details>
  
  ```swift
  @Binding var username: String
  ```
  
  ```swift
  @State var username: String = ""
  
  var body: some View {
      ChildView(username: $username)
  }
  ```
  
</details>

### StateObject

![iOS 14](https://img.shields.io/badge/iOS_14-e4405f?style=for-the-badge&logo=apple&logoColor=white)

`StateObject` 는 뷰모델을 특정 뷰가 아닌 SwiftUI 프레임워크에서 관리하도록 하고 그 모델을 *참조* 합니다. (Source of truth)
이 속성을 사용할 때는 선언하고자 하는 뷰모델의 클래스가 반드시 `ObservableObject` 를 준수하고 있어야 합니다.

<details>
  
```swift
@StateObject private var data = ViewModel()
```

</details>

### ObservedObject

`ObservedObject` 는 뷰모델은 현재 뷰의 라이프사이클에 맞춰 사용하고 싶을 때, 즉 현재 뷰 내에서만 사용할 때 쓰이는 속성입니다. (뷰가 관리)
객체를 생성해도 되지만 이 속성의 특성상(아래의 **주의** 참고) 가급적이면 생성보다는 상위 뷰의 뷰모델로 부터 인스턴스를 전달받는 형태로 사용하는 것을 권장합니다.
이 속성을 사용할 때는 선언하고자 하는 뷰모델의 클래스가 반드시 `ObservableObject` 를 준수하고 있어야 합니다.

> **중요** 상위 뷰가 존재하는 경우 상위 뷰가 업데이트 될때마다 현재 뷰가 재생성되어 뷰모델이 리셋 됩니다.

<details>
  
  ```swift
  @ObservedObject var data: ViewModel
  ```
  
  ```swift
  @StateObject private var data = ViewModel()
  
  var body: some View {
      ChildView(data: data)
  }
  ```

</details>

### Published

`ObservableObject` 프로토콜을 준수하는 뷰모델은 `@Published` 속성을 사용해서 값의 변화를 퍼블리싱 하여 뷰를 업데이트 할 수 있습니다.

<details>
  
  ```swift
  @Published var userID: String = ""
  ```
  
  ```swift
  TextField("아이디를 입력하세요", text: $observedObject.userID)
  ```

</details>

