# SwiftUI 생명주기로 마이그레이션하기

기존 코드를 유지하면서 SwiftUI의 Scene 기반 생명주기를 사용하는 방법입니다.

## 개요

앱을 SwiftUI 생명주기로 전환하면 선언적 구문과 공간 프레임워크와의 호환성을 활용할 수 있습니다. 이를 위해 앱의 진입점 변경, 앱 실행 구성, SwiftUI가 제공하는 메서드를 사용한 생명주기 변경 모니터링 등의 단계가 필요합니다.

## 1. 앱 진입점 변경

UIKit의 `@main` 또는 `@UIApplicationMain`으로 지정된 `AppDelegate` 대신, SwiftUI 앱 구조체를 앱의 진입점으로 사용합니다.

1.  Xcode에서 프로젝트를 엽니다.
2.  `File > New > File > Swift file`을 선택하고 `<YourAppName>App.swift`와 같이 이름을 지정합니다.
3.  파일 상단에 `import SwiftUI`를 추가합니다.
4.  다음과 같이 `@main` 속성을 사용하여 앱 구조체를 선언합니다.

    ```swift
    import SwiftUI

    @main
    struct MyExampleApp: App {
        var body: some Scene {
            WindowGroup {
                ContentView()
            }
        }
    }
    ```
5.  기존 앱 델리게이트 파일에서 `@main` 또는 `@UIApplicationMain` 속성을 제거합니다.

## 2. 앱 델리게이트 메서드 지원

기존 AppDelegate의 메서드를 계속 사용하려면 `UIApplicationDelegateAdaptor` 프로퍼티 래퍼를 사용합니다. `App` 선언 내부에 이 프로퍼티 래퍼를 배치하여 `UIApplicationDelegate` 프로토콜을 준수하는 델리게이트를 SwiftUI에 알립니다.

```swift
@main
struct MyExampleApp: App {
    @UIApplicationDelegateAdaptor private var appDelegate: MyAppDelegate
    var body: some Scene { ... }
}
```

*   AppKit 지원은 `NSApplicationDelegateAdaptor`, WatchKit 지원은 `WKApplicationDelegateAdaptor`를 사용합니다.

## 3. 앱 실행 구성

스토리보드를 포함하는 앱을 마이그레이션하는 경우, 더 이상 필요하지 않다면 제거해야 합니다.

1.  Xcode에서 프로젝트를 엽니다.
2.  프로젝트 네비게이터에서 `Main.storyboard`를 제거합니다.
3.  앱 타겟을 선택합니다.
4.  `Info.plist` 파일을 엽니다.
5.  `Main storyboard file base name` 키를 제거합니다.
6.  `Application Scene Manifest > Scene Configuration > Window Application Session Role > Item 0 (Default Configuration)` 딕셔너리에서 `Storyboard Name` 키를 제거합니다.

![Storyboard_InfoPlist](Storyboard_InfoPlist.png)


*   SceneDelegate는 `Info.plist`에서 키를 제거한 후에도 계속 호출되므로, 이 파일에서 다른 Scene 기반 생명주기 변경을 처리할 수 있습니다. 이전에 Scene 델리게이트에서 앱을 실행했다면 `scene(_:willConnectTo:options:)` 메서드를 제거합니다.
*   이전에 앱에서 Scene을 지원하지 않고 AppDelegate에 의존하여 앱 실행에 응답했다면, `application(_:didFinishLaunchingWithOptions:)`에서 더 이상 루트 뷰 컨트롤러를 설정하지 않도록 하고 대신 `true`를 반환합니다.

## 생명주기 변경 모니터링

Scene 기반 특성으로 인해 앱 델리게이트에서는 더 이상 생명주기 변경을 직접 모니터링할 수 없습니다. 대신 SwiftUI가 제공하는 생명주기 열거형인 `ScenePhase`를 사용하여 Scene의 단계를 모니터링합니다. 환경 값을 관찰하여 단계가 변경될 때 작업을 시작합니다.

```swift
@Environment(\.scenePhase) private var scenePhase
```

값을 어디서 읽는지에 따라 다르게 해석됩니다. `View` 인스턴스 내에서 읽으면 해당 뷰를 포함하는 Scene의 단계를 반영하고, `App` 인스턴스 내에서 읽으면 앱의 모든 Scene 단계의 집계를 반영합니다.

Scene 델리게이트로 Scene 기반 이벤트를 처리하려면 앱 델리게이트 내에서 SwiftUI 앱에 Scene 델리게이트를 제공합니다.
