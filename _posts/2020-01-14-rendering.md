---
layout: post
title: >
    브라우저 렌더링
---

코딩을 하다가 화면에 내가 코딩한 화면이 어떻게 그려지는가 궁금해졌다. 
어떻게 그려지는가를 알면 웹 성능을 최적화 하는데에 도움이 될 거 같아 포스트를 해본다.

클라이언트가 브라우저 검색창에 주소를 치면 웹 서버는 해당 주소에 맞는 html문서를 내보낸다.

{% include aligner.html images="pexels/posts/render_img0.png" column=1 %}

해당 주소에 맞는 html문서를 내보낼 때에 렌더링 과정을 거치는데 대부분의 브라우저는 렌더링을 수행하는 렌더링 엔진(Rendering Engine)을 가지고 있다.

### 브라우저별 렌더링 엔진
- **크롬, 오페라**
  - 블링크(Blink) - 웹키트에서 파생된 엔진
- **인터넷 익스플로러**
  - 트라이던트(Trident)
- **파이어폭스, 모질라**
  - 게코(Gecko)
- **사파리**
  - 웹키트(Webkit)



브라우저마다 렌더링 엔진이 다르기 때문에 똑같은 html문서를 렌더링 하는 과정에서 미세한 차이가 존재한다.

## 렌더링 엔진 동작 과정

아래는 Webkit 엔진 기준으로 설명한다.

{% include aligner.html images="pexels/posts/render_img2.png" column=1 %}


### 1. 불러오기

서버로부터 전달 받는 리소스 스트림을 읽는 과정이다. 읽으면서 어떤 파일인지, 데이터인지 파일을 다운로드 할 것인지 등을 결정한다.

### 2. 파싱과 DOM 트리 구축

웹 엔진이 가지고 있는 HTML/XML 파서가 문서를 파싱해서 DOM TREE를 만든다.
파싱이란 브라우저가 코드를 이해하고 사용할 수 있는 구조로 변환하는 작업이다.
여기서 DOM TREE란 HTML 구조를 있는 그대로 표현하는 트리라고 할 수 있겠다.

{% include aligner.html images="pexels/posts/render_img3.png" column=1 %}


### 3. CSSOM (CSS Object Model) 구축

브라우저가 HTML을 파싱하는 과정에서 css파일을 불러오게 된다. css 파일을 불러온 뒤, HTML과 마찬가지로 브라우저가 이해하고 처리할 수 있는 형식으로 변환해야 한다.
그 과정은 DOM을 생성하는 과정과 동일하다. 단 출력된 결과는 CSSOM(CSS Object Model) 이라고 부른다.

{% include aligner.html images="pexels/posts/render_img4.png" column=1 %}

### 4. 렌더 트리 (Render Tree)

앞서 만든 DOM 트리와, CSSOM 트리는 서로 연관이 없는 독립적인 객체이다. DOM 트리는 콘텐츠를 의미하고, CSSOM은 문서에 적용되어야 하는 스타일 규칙을 의미한다.
__이 두 개의 트리를 결합하여 렌더 트리를 생성__
한다.


렌더 트리는 DOM 트리의 최상위 노드부터 각각의 노드를 탐색하여 렌더링에 필요한 노드들을 CSSOM 트리와 일치시켜 생성한다. 그리고 DOM 트리를 탐색하는 과정에서 렌더링 출력에 반영되지 않는 불필요한 노드들은 건너뛰게 된다. 예를들면 script 태그나, meta 태그 같은 것들이 있다. 또한 display:none 처럼 CSS를 통해 렌더링 출력에 반영되지 않는 노드들도 실제 화면에서 렌더링이 되지 않기 때문에 렌더 트리에서 제외된다. 다만 visibility:hidden 속성은 렌더 트리에 포함된다. 그 이유는 visibility:hidden 속성은 렌더링이 되더라도 여전히 레이아웃에서 공간을 차지하기 때문이다.

{% include aligner.html images="pexels/posts/render_img5.png" column=1 %}

### 5. 레이아웃 처리

렌더 트리는 DOM 트리와 CSSOM 트리에 의해 정의된 스타일만 계산하였다. 하지만 기기의 뷰포트(Viewport) 내에서의 정확한 위치 및 크기는 계산되지 않았다.

예를들어 CSS에 width:50% 로 정의되어 있다고 하면, 실제 브라우저에서 표현되는 정확한 사이즈는 계산되지 않았다는 의미이다.

{% include aligner.html images="pexels/posts/render_img6.png" column=1 %}

따라서 브라우저에 출력하기 전 실제 출력되는 정확한 위치와 크기를 계산하여야 한다. 그 단계가 레이아웃 (layout)이라고 한다. 이 단계에서는 각 객체의 정확한 위치 및 크기를 계산하기 위해 렌더 트리의 루트에서부터 탐색해 실제 픽셀 값을 구하여 박스 모델 (Box Model)을 출력한다.

{% include aligner.html images="pexels/posts/render_img7.png" column=1 %}

### 6. 페인트

__Layout 계산이 완료__
되면 이제 __요소들을 실제 화면을 그리게 된다.__ 이전 단계에서 이미 요소들의 위치와 크기, 스타일 계산이 완료된 Render Tree 를 이용해 실제 픽셀 값을 채워넣게 된다. 이 때 __텍스트, 색, 이미지, 그림자 효과등__
이 모두 처리되어 그려진다. 이 때 처리해야 하는 스타일이 복잡할수록 Paint 단계에 소요되는 시간이 늘어나게 된다. 간단한 예시로 단순한 단색 background-color의 경우 paint 속도가 빠르지만 그라데이션이나 그림자 효과등은 painting 소요시간이 비교적 더 오래 소요된다.

### 7. 리플로우와 리페인트

리플로우(Reflow)란 생성된 DOM 노드의 레이아웃(너비, 높이 등) 변경 시, 영향받은 모든 노드(자식, 부모 등)의 수치를 다시 계산하여 렌더 트리를 재생성하는 작업이다.

리페인트(Repaint)란 Reflow 과정이 끝난 후 재생성된 렌더 트리를 다시 그리는 작업이다.

### 8. 렌더링 최적화 (Reflow, Repaint 줄이기)
**Reflow가 일어나는 대표적인 경우**
- 페이지 초기 렌더링 시(최초 Layout 과정)
- 윈도우 리사이징 시 (Viewport 크기 변경시)
- 노드 추가 또는 제거
- 요소의 위치, 크기 변경 (left, top, margin, padding, border, width, height, 등..)
- 폰트 변경 과(텍스트 내용) 이미지 크기 변경(크기가 다른 이미지로 변경 시)

**Reflow 발생 코드**

{% highlight js %}
    function reFlow() { 
        document.getElementById('container').style.width = '600px';
        return false; 
    }
{% endhighlight %}


#### Repaint (Paint)
Reflow 발생 이유와 같이 스타일의 모든 변경이 레이아웃 수치에 영향을 받는것은 아니다. 

즉, 노드의 background-color, visibillty, outline 등의 스타일 변경 시에는 레이아웃 수치가 변경되지 않으므로 Reflow 과정이 생략된 Repaint 과정만 일어나게 된다.

**Repaint 발생 코드**
{% highlight js %}
    function rePaint() { 
        document.getElementById('container').style.backgroundColor = 'red'; 
        return false; 
    }
{% endhighlight %}

#### 해결방법
- 클래스 변화에 따른 스타일 변화를 원할 경우, 최대한 DOM 구조 상 끝단에 위치한 노드에 주어라. 
- 인라인 스타일을 최대한 배제하라.
- 애니메이션이 들어간 엘리먼트는 가급적 position:fixed 또는 position:absolute 로 지정
- 퀄리티와 퍼포먼스 사이에서 타협하라
- 테이블 레이아웃을 피하라
- IE의 경우, CSS에서의 JS표현식을 피하라.
- JS를 통해 스타일변화를 주어야 할 경우, 가급적 한번에 처리하라.
- CSS Rules는 필요한 만큼만 정리하라.
- position:relative 사용 시 주의하자


---

**참고사이트**
* [boxfoxs](https://boxfoxs.tistory.com/408){:target="_blank"}
* [naverD2](https://d2.naver.com/helloworld/59361){:target="_blank"}
* [Reflow 원인과 마크업 최적화](https://lists.w3.org/Archives/Public/public-html-ig-ko/2011Sep/att-0031/Reflow_____________________________Tip.pdf){:target="_blank"}