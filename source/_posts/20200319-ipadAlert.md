---
title: ipad에서 ActionSheetAlert가 뜨면 죽는 이슈
date: 2020-03-19 13:28:43
category:
 - iOS
tags: 
- iPadIssue
- ActionSheetAlert
---

최근 클래식매니저앱을 개편하면서 AlertController를 애용했는데 아이패드에서 돌리면 죽는경우가 매우 많아서 분석해보니 액션시트 스타일인 Alert가 뜰때 죽고있었다.

그래서 아래와같이 UIAlertController extension 에 show 함수를 수정한 결과 매우잘된다.
완전 고려를 못하고있던 부분이었는데 그나마 쉽게해결됐다 ㅎㅎㅎㅎㅎ

```
extension UIAlertController{
    func show(){
        guard let topVC = UIApplication.shared.topViewController() else { return }

        if Device.current.isPad,
            let popoverController = self.popoverPresentationController {
                popoverController.sourceView = topVC.view
                popoverController.sourceRect = CGRect(x: topVC.view.bounds.midX, y: topVC.view.bounds.midY, width: 0, height: 0)
                popoverController.permittedArrowDirections = []
            }

        topVC.present(self, animated: true, completion: nil)
    }
}
```
