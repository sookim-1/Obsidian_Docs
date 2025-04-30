# [Scene 프로토콜](https://developer.apple.com/documentation/swiftui/scene)

`Scene` 프로토콜은 시스템에 의해 생명주기가 관리되는 앱 UI(사용자 인터페이스)의 일부입니다.

**지원 플랫폼:**
*   iOS 14.0+

**개요:**

```swift
@MainActor @preconcurrency
protocol Scene
```

`App`의 `body`는 하나 이상의 `Scene` 프로토콜을 준수하는 인스턴스를 결합하여 생성됩니다. SwiftUI가 제공하는 `WindowGroup`과 같은 내장 Scene을 사용하거나 다른 Scene으로 구성된 커스텀 Scene을 만들 수 있습니다.

커스텀 Scene을 만들려면 `Scene` 프로토콜을 준수하는 타입을 선언하고 필수 `body` 연산 프로퍼티를 구현하여 커스텀 Scene의 콘텐츠를 제공합니다.

```swift
struct MyScene: Scene {
    var body: some Scene {
        WindowGroup {
            MyRootView()
        }
    }
}
```

Scene은 사용자에게 표시하려는 뷰 계층 구조의 컨테이너 역할을 합니다. 시스템은 플랫폼에 적합하고 앱의 현재 상태에 따라 UI(사용자 인터페이스)에 뷰 계층 구조를 언제, 어떻게 표시할지 결정합니다.

Scene 또는 해당 뷰 내에서 `scenePhase` 환경 값을 읽어 Scene이 활성 상태인지 또는 다른 상태인지 확인할 수 있습니다. `@Environment` 속성을 사용하여 `ScenePhase` 열거형 값 중 하나인 Scene Phase를 포함하는 프로퍼티를 생성할 수 있습니다.

```swift
struct MyScene: Scene {
    @Environment(\.scenePhase) private var scenePhase

    // ...
}
```

`Scene` 프로토콜은 Scene을 구성하는 데 사용하는 Scene 수정자(modifier)를 제공합니다. 예를 들어, `onChange(of:perform:)` 수정자를 사용하여 값이 변경될 때 작업을 트리거할 수 있습니다.

```swift
struct MyScene: Scene {
    @Environment(\.scenePhase) private var scenePhase
    @StateObject private var cache = DataCache()

    var body: some Scene {
        WindowGroup {
            MyRootView()
        }
        .onChange(of: scenePhase) { newScenePhase in
            if newScenePhase == .background {
                cache.empty()
            }
        }
    }
}
```

`Scene` 프로토콜을 준수하는 타입은 기본적으로 `@preconcurrency @MainActor` 격리를 상속받습니다. 확장(extension)에서 준수함을 선언하여 MainActor 격리를 선택 해제할 수 있습니다.

```swift
struct MyCustomType: Transition {
    // `@preconcurrency @MainActor` isolation by default
}

extension MyCustomType: Transition {
    // `nonisolated` by default
}
```