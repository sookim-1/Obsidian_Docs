# [View 프로토콜](https://developer.apple.com/documentation/swiftui/view)

`View` 프로토콜은 앱 사용자 인터페이스의 일부를 나타내며 뷰를 구성하는 데 사용하는 수정자(modifier)를 제공하는 타입입니다.

**지원 플랫폼:**
*   iOS 13.0+

```swift
@MainActor @preconcurrency
protocol View
```    

**개요:**

`View` 프로토콜을 준수하는 타입을 선언하여 커스텀 뷰를 생성합니다. 필수 `body` 연산 프로퍼티를 구현하여 커스텀 뷰의 콘텐츠를 제공합니다.

```swift
struct MyView: View {
    var body: some View {
        Text("Hello, World!")
    }
}
```

뷰의 `body`는 SwiftUI가 제공하는 `Text`와 같은 내장 뷰와 사용자가 정의하는 다른 커스텀 뷰를 하나 이상 결합하여 뷰 계층 구조로 조립합니다.

`View` 프로토콜은 앱 레이아웃에서 뷰를 구성하는 데 사용하는 수정자(기본 구현이 있는 프로토콜 메서드) 세트를 제공합니다. 수정자는 호출된 뷰 인스턴스를 지정된 특성을 가진 다른 뷰로 래핑하는 방식으로 작동합니다. 예를 들어, 텍스트 뷰에 `opacity(_:)` 수정자를 추가하면 투명도가 적용된 새 뷰가 반환됩니다.

기본 수정자의 전체 목록은 뷰 관리를 위한 다양한 컨트롤을 제공합니다. 레이아웃, 접근성, 입력 및 이벤트 수정자 등을 사용할 수 있습니다. 기본 수정자 그룹을 새로운 커스텀 뷰 수정자로 모아 재사용할 수도 있습니다.

이 프로토콜을 준수하는 타입은 원래 선언에 포함된 경우 `@preconcurrency @MainActor` 격리를 상속받습니다. MainActor 격리는 기본값이지만 필수는 아닙니다. 확장(extension)에서 준수함을 선언하여 격리를 선택 해제할 수 있습니다.
