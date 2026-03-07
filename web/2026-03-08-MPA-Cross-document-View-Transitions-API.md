### Context
기존의 View Transitions API는 단일 페이지 애플리케이션(SPA)의 DOM 업데이트에 한정되어 사용되었습니다. 하지만 최근 웹 표준의 발전으로 다중 페이지 애플리케이션(MPA)에서도 페이지 간 자연스러운 전환 효과를 구현할 수 있는 Cross-document View Transitions가 등장했습니다. Chrome 126 버전부터 이를 정식으로 지원하며, 별도의 복잡한 JS 로직 없이 CSS 선언만으로도 구현이 가능합니다.

### Core
MPA 환경에서 페이지 전환 애니메이션을 활성화하려면, 전환에 참여하는 양쪽 페이지의 CSS에 `@view-transition` 규칙을 선언해야 합니다.

```css
/* 전환 효과 활성화 */
@view-transition {
  navigation: auto;
}

/* 특정 요소에 고유한 이름 부여 (애니메이션 동기화) */
.header {
  view-transition-name: main-header;
}
```

해당 선언을 통해 브라우저는 두 페이지 간의 네비게이션 발생 시, `view-transition-name`이 일치하는 요소들을 추적하여 자동으로 보간(interpolation) 애니메이션을 적용합니다.

### Insight
MPA에서의 View Transitions API 지원은 그동안 SPA가 독점하던 '앱 같은 사용자 경험(App-like UX)'을 전통적인 서버 사이드 렌더링 방식의 웹에서도 손쉽게 구현할 수 있게 해준다는 점에서 매우 혁신적입니다. 다만, 현재 Chrome 126 기준으로는 메인 프레임 네비게이션에만 제한적으로 지원되므로, 복잡한 아이프레임(iframe) 구조에서는 제약이 따를 수 있습니다. 향후 브라우저 호환성이 확대된다면 사용자 경험을 개선하는 핵심적인 표준 기술로 자리 잡을 것으로 보입니다.

**출처:** [View Transitions API for cross-document navigations](https://developer.chrome.com/docs/web-platform/view-transitions/cross-document)