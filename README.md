# Daum Postcode

[다음 우편번호 서비스](https://postcode.map.daum.net/guide)를 별도의 패키지 설치 없이도 네이티브 앱에서 쉽게 사용할 수 있습니다.

## 사용 방법

1. 웹뷰를 사용해서 https://postcode.pocketlesson.io URL을 로드합니다.
2. 로드된 웹뷰에서 주소를 선택하면 `postcode://` URL scheme과 `webkit.messageHandlers.postcode` 핸들러를 통해 메시지가 전달됩니다.

## API

### Query Parameters

| 이름 | 설명 | 예시 |
|---|---|---|
| query | 검색어 (Optional) | `역삼로 180` |

### Callback Data

콜백은 두 가지 방식으로 제공됩니다.

1. URL Scheme
    `postcode://complete?<key>=<value>&...` 형태로 제공됩니다.
2. Message Handler
    `{key: value, ...}` 형태로 제공됩니다.

자세한 데이터는 [다음 우편번호 서비스 문서](https://postcode.map.daum.net/guide#attributes)를 참고해 주세요.

## 사용 예시

### iOS

#### URL Scheme

```swift
let webView = WKWebView()
webView.navigationDelegate = self

let url = URL(string: "https://postcode.pocketlesson.io")!
webView.load(URLRequest(url: url))

func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction, decisionHandler: @escaping (WKNavigationActionPolicy) -> Void) {
  switch navigationAction.request.url?.scheme {
  case "postcode":
    if let urlString = navigationAction.request.url?.absoluteString {
      let components = URLComponents(string: urlString)
      let address = components?.queryItems?.first { $0.name == "address" }?.value
      let zonecode = components?.queryItems?.first { $0.name == "zonecode" }?.value
      print(address) // e.g. 서울 강남구 역삼로 180
      print(zonecode) // e.g. 06248
    }
    decisionHandler(.cancel)

  default:
    decisionHandler(.allow)
  }
}
```

#### Message Handler

```swift
let webView = WKWebView()
webView.configuration.userContentController.add(PostcodeHandler(), name: "postcode")

let url = URL(string: "https://postcode.pocketlesson.io")!
webView.load(URLRequest(url: url))

class PostcodeHandler: NSObject, WKScriptMessageHandler {
  func userContentController(_ userContentController: WKUserContentController, didReceive message: WKScriptMessage) {
    guard let data = message.body as? [String: Any] else { return }
    let address = data["address"] as? String // e.g. 서울 강남구 역삼로 180
    let zoneCode = data["zonecode"] as? String // e.g. 06248
  }
}
```

## 라이선스

이 소스코드 저장소에 속한 코드는 MIT 라이선스를 따릅니다. 다음 우편번호 서비스와 관련한 소유권과 저작권은 카카오에 있습니다.

로딩 인디케이터의 애니메이션은 [react-native-web](https://github.com/necolas/react-native-web)의 것을 사용했습니다.
