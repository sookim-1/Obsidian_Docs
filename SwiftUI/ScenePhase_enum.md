# [ScenePhase 열거형](https://developer.apple.com/documentation/swiftui/scenephase)

`ScenePhase`는 Scene의 운영 상태를 나타냅니다.

**지원 플랫폼:**
*   iOS 14.0+

**개요:**

시스템은 Scene 인스턴스를 Scene의 운영 상태를 반영하는 단계(phase)로 이동시킵니다. 단계가 변경될 때 작업을 트리거할 수 있습니다. `@Environment`를 사용하여 `scenePhase` 값을 관찰함으로써 현재 단계를 읽을 수 있습니다.

```swift
@Environment(\.scenePhase) private var scenePhase
```

값을 어디서 읽는지에 따라 해석이 달라집니다.

*   **View 인스턴스 내에서 읽을 경우**: 해당 뷰를 포함하는 Scene의 단계를 얻습니다.
```swift
struct MyView: View {
    @ObservedObject var model: DataModel
    @Environment(\.scenePhase) private var scenePhase


    var body: some View {
        TimerView()
            .onChange(of: scenePhase) {
                model.isTimerRunning = (scenePhase == .active)
            }
    }
}
```

*   **App 인스턴스 내에서 읽을 경우**: 앱의 모든 Scene 단계의 집계 값을 얻습니다. 하나 이상의 Scene이 활성이면 `active`, 모든 Scene이 비활성이면 `inactive`를 보고합니다. 앱이 `background` 단계로 진입하면 곧 종료될 수 있으므로 리소스를 해제하는 데 사용할 수 있습니다.

```swift
@main
struct MyApp: App {
    @Environment(\.scenePhase) private var scenePhase


    var body: some Scene {
        WindowGroup {
            MyRootView()
        }
        .onChange(of: scenePhase) {
            if scenePhase == .background {
                // Perform cleanup when all scenes within
                // MyApp go to the background.
            }
        }
    }
}
```

**ScenePhase 케이스:**

*   **`active`**: Scene이 foreground(포그라운드)에 있으며 상호작용 가능합니다.
*   **`inactive`**: Scene이 foreground(포그라운드)에 있지만 작업을 일시 중지해야 합니다.
*   **`background`**: Scene이 현재 UI에 표시되지 않습니다.
