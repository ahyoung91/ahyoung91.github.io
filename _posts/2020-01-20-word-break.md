---
layout: post
title: >
    Word break
tags: [word-break, normal, break-word, keep-all]
---

작업을 하다보면 가볍게 이해한 속성을 바탕으로 관습적으로 사용하는 속성들이 있다.
그 중 word-break 속성에 대해 조금 더 명확하게 알고 넘어가는게 좋겠다고 생각했다.
word-break란 단어의 분리를 어떻게 할 것인지 결정한다.
중요하게 기억할 큰이슈들은 한 단어의 넓이가 자신을 감싸고 있는 tag의 width보다 클경우에 생긴다는 점이다.
그리고 여기서 영어와 CJK(Chinese/Japanese/Korean text - 이하 한글로 표현) 는 word-break의 적용이 다른 value가 있다는 점이다.

---

## word-break: normal;

한글은 tag의 width 크기보다 넓은 한개의 단어일경우에도 글자 단위로 줄바꿈해준다.
하지만 영어의 경우에는 넘쳐서 디자인이 깨지는 형태로 표현된다.

## word-break: break-word;

영어에는 잘 적용이되어 단어별로 줄바꿈이 된다. 하지만 한글에는 실제적으로 normal로 적용이 된다. 그리하여 글자 단위로 줄바꿈이 된다. 그리하여 넘치는 이슈가 생길일은 없지만 한글의 경우 단어별로 끊어주지 않게되어 이쁘지 않게 표현된다.

## word-break: keep-all;

한글과 영어 모두 길이가 긴 단어의 경우 container를 넘치게 되고 줄바꿈되지 않는다. 디자인을 깨뜨리는 이슈를 발생시키게 된다.

## 결론
<iframe height="265" style="width: 100%;" scrolling="no" title="MWYxZmQ" src="https://codepen.io/ahyoung/embed/MWYxZmQ?height=265&amp;theme-id=default&amp;default-tab=css,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
    See the Pen <a href='https://codepen.io/ahyoung/pen/MWYxZmQ'>MWYxZmQ</a> by ahyoung
    (<a href='https://codepen.io/ahyoung'>@ahyoung</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

위의 코드펜에서 주석을 풀면서 해당 속성을 확인해보자.

word-break와 같은 텍스트에 관한 기본값이 보통 inherit으로 설정되어 있다. 그래서 상위태그에 속성을 주면 상속이 된다.

body정도에 word-break: break-word; 를 주어 영어는 단어별로 줄바꿈될테고, 한글인경우 필요한부분에 keep-all 을 따로 줄수있겠다.

한글 문장이 대부분인 사이트여서 word-break속성을 반복적으로 주기싫다면, word-break: keep-all;을 body에 주고 한단어가 넘치는 이슈가 발생하는 곳에 word-break; 를 각각 줄수있겠다.