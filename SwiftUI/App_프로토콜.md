# [App 프로토콜](https://developer.apple.com/documentation/swiftui/app)

`App` 프로토콜은 앱의 Structure(구조)와 Behavior(동작)을 나타내는 타입입니다.

**지원 플랫폼:**
*   iOS 14.0+

**개요:**

```swift
@MainActor @preconcurrency
protocol App
```

`App` 프로토콜을 준수하는 구조체(struct)를 선언하여 앱을 생성합니다. 구조체 앞에 `@main` 속성을 붙여 앱의 진입점임을 나타냅니다. `App` 프로토콜은 시스템이 앱을 실행할 때 호출하는 `main()` 메서드의 기본 구현을 제공합니다. 앱에는 하나의 진입점만 있을 수 있습니다.

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            Text("Hello, world!")
        }
    }
}
```

앱의 `body`는 `Scene` 프로토콜을 준수하는 인스턴스로 구성됩니다. 각 Scene은 뷰 계층 구조의 루트 뷰를 포함하며 시스템에 의해 생명주기가 관리됩니다. SwiftUI는 문서나 설정 표시와 같은 일반적인 시나리오를 처리하기 위한 구체적인 Scene 타입을 제공하며, 커스텀 Scene도 생성할 수 있습니다.

```swift
@main
struct Mail: App {
    var body: some Scene {
        WindowGroup {
            MailViewer()
        }
        Settings {
            SettingsView()
        }
    }
}
```

**상태 공유:**

앱의 모든 Scene에서 공유할 상태를 선언할 수 있습니다. 예를 들어, `@StateObject` 속성을 사용하여 데이터 모델을 초기화하고, 해당 모델을 `EnvironmentObject` 또는 `ObservedObject`를 통해 Scene에 제공할 수 있습니다.

```swift
@main
struct Mail: App {
    @StateObject private var model = MailModel()

    var body: some Scene {
        WindowGroup {
            MailViewer()
                .environmentObject(model) // 환경 객체로 전달
        }
        Settings {
            SettingsView(model: model) // ObservedObject로 전달
        }
    }
}
```

**MainActor 격리:**

`App` 프로토콜을 준수하는 타입은 기본적으로 `@preconcurrency @MainActor` 격리를 상속받습니다. 확장(extension)에서 준수함을 선언하여 MainActor 격리를 선택 해제할 수 있습니다.

```swift
struct MyCustomType: Transition {
    // `@preconcurrency @MainActor` isolation by default
}

extension MyCustomType: Transition {
    // `nonisolated` by default
}
```