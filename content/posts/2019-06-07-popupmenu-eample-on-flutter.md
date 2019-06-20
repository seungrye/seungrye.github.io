---
title: "PopupMenu example on Flutter"
date: 2019-06-07T01:15:39+09:00
draft: false
categories: [studylog]
tags: [flutter,example]
---

Flutter 앱에서 깔끔한 [Popup Menu 라이브러리](https://github.com/chinabrant/popup_menu) 를 사용해서 팝업을 띄우는 예제를 작성해 본다.

근데, 이미 Popup Menu 저장소에 샘플 코드가 포함되어 있음.. ㅎ;

<!--more-->

플러터 프로젝트 어떻게 만드는지 모르면, Flutter 는 [여기](https://flutter.dev/docs/get-started/test-drive#create-app)를 참고해서 작성하자.

``` shell
popup_menu_prj$ tree
.
├── README.md
├── android
├── build
├── ios
├── lib
│   └── main.dart
├── pubspec.yaml
└── test
```

이 샘플에, 다음과 같은 팝업 메뉴를 띄우도록 할 거임.

<img class="special-img-class" src="/posts/resources/_gen/images/588e3baa-cb10-4eea-809f-1a634b5f0ae5.png" alt="Popup Menu" style="height:200px"/>


### Add Dependency

`pubspec.yaml` 파일에 [popup_menu 의존성 추가](https://pub.dev/packages/popup_menu#-installing-tab-).

```diff
dependencies:
+  popup_menu: ^1.0.1
```

### 소스에 Popup Menu 관련 코드 추가.
샘플을 만들떄 diff 를 남겨둔게 없어서, 기억에 의존해서 적은것임.
따라서, 실제 diff 포멧과는 다름 :P

Note. 소스코드 라인이 너무 길어지는거 같아서... 불필요한 라인은 줄여서 적음.

```diff
import 'package:flutter/material.dart';
+ import 'package:popup_menu/popup_menu.dart';
...

class _MyHomePageState extends State<MyHomePage> {
+  GlobalKey btnKey2 = GlobalKey();
...

  Widget build(BuildContext context) {
+    PopupMenu.context = context;
...

            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
+            Container(
+              child: MaterialButton(
+                  onPressed: customPopupMenu,
+                  key: btnKey2,
+                  height: 45.0,
+                  child: Text('Show Menu')),
+            ),
...

+  void customPopupMenu() {
+    var menu = PopupMenu(items: [
+      MenuItem(title: 'Home', image: Icon(Icons.home, color: Colors.white,)),
+      MenuItem(title: 'Setting',
+            image: Icon(Icons.settings, color: Colors.white,)),
+      MenuItem(title: 'PopupMenu',
+            image: Icon(Icons.menu, color: Colors.white,)),
+      MenuItem(title: 'Mail', image: Icon(Icons.mail, color: Colors.white,)),
+      MenuItem(title: 'Power', image: Icon(Icons.power, color: Colors.white,)),
+    ],
+    onClickMenu: onClickMenu,
+    onDismiss: onDismiss);
+
+    menu.show(widgetKey: btnKey2);
+  }
+
+  void onDismiss() { print('Menu is closed'); }
+
+  void onClickMenu(MenuItemProvider item) {
+    print('Click menu -> ${item.menuTitle}');
+  }
```

여기서 뭐 설명할건 없지만, `_MyHomePageState::build()` 에서 'Show Menu' 텍스트가 적힌 `MaterialButton` 을 추가하고, 버튼이 눌렸을떄 `customPopupMenu` 가 호출되도록 처리함.

`customPopupMenu` 에서는 팝업 메뉴에 추가될 메뉴 아이템을 만들고, 핸들러를 등록 (`onClickMenu:` , `onDismiss:`) 후 메뉴를 보여 (`.show`) 줍니다.

간단하죠?
