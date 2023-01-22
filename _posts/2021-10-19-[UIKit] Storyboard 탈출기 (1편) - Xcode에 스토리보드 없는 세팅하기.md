---
title:  "[UIKit] Storyboard 탈출기 (1편) - Xcode에 스토리보드 없는 세팅하기"
date:   2021-10-19 23:30:51 +0900
categories: ios view-programming
---



## [UIKit] Storyboard 탈출기 (1편) - Xcode에 스토리보드 없는 세팅하기

스토리보드에서 탈출하기 위해 가장 먼저 Xcode에 스토리보드 없는 세팅하는 과정을 알아보겠습니다. 

(작성버전 : Xcode 12.5.1)

---

우선 만든 프로젝트에서 타겟을 클릭하면 나오는 Deployment Info에서 Main Interface에 있는 `Main` 부분을 제거합니다. 

![step1](https://user-images.githubusercontent.com/72622744/213928921-5567889e-f6e6-40e2-a54d-fd9c5e76e255.jpeg)



그리고 프로젝트를 시작할 때부터 있는 Main.storyboard를 제거해 줍니다. (Move to Trash)

![step2](https://user-images.githubusercontent.com/72622744/213928943-e38e83e0-7c3e-4735-833f-314e375d195d.jpeg)





그 다음 Info.plist로 가서 Application Scene Manifest -> Scene Configuration -> Application Session Role -> Item 0 에서 Storyboard Name 키를 삭제합니다. 

![step3](https://user-images.githubusercontent.com/72622744/213928966-34ee5b5f-8f42-4001-bf43-56b0e4ab16c6.jpeg)

그 다음 SceneDelegate.swift 파일로 가서 `func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions)` 메서드를다음과 같이 설정해줍니다. 

```swift
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {        
        // 옵셔널 바인딩으로 windowScene가져옴
        guard let windowScene = (scene as? UIWindowScene) else { return }
        
        // 윈도우 크기 설정
        window = UIWindow(frame: UIScreen.main.bounds)
        
        // 뷰컨트롤러 인스턴스 가져오기
        let vc = ViewController()
        
        // 네비게이션 컨트롤러 설정
        let navVC = UINavigationController(rootViewController: vc)
  
        // rootVC를 설정
        window?.rootViewController = navVC
        
        // windowScene 설정
        window?.windowScene = windowScene
    }
```

이렇게 하면 네비게이션뷰컨트롤러로 구성된 ViewController() 가 화면에 나타나게 됩니다. 

---

생각보다 초기세팅은 간단하네요. 익숙해질 때까지 종종 까먹을 것 같아 정리해두었습니다. 그럼 이만 총총..





### Reference

- https://www.youtube.com/watch?v=kCJEaMwH5o0&list=PLgOlaPUIbynqRzpQBIdEDnjDdkVsjHqxK&index=12



