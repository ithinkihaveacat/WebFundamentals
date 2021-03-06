---
layout: article
title: "클릭투콜(Click to call)"
description: "폰 기능이 있는 장치에서 사용자가 전화번호를 눌러
  여러분과 쉽게 직접 연결하도록 할 수 있는데 이 기능을 흔히
  클릭투콜(click to call)이라고 합니다."
introduction: "폰 기능이 있는 장치에서 사용자가 전화번호를 눌러
  여러분과 쉽게 직접 연결하도록 할 수 있는데 이 기능을 흔히
  클릭투콜(click to call)이라고 합니다."
article:
  written_on: 2014-06-17
  updated_on: 2014-10-21
  order: 1
id: click-to-call
authors:
  - petelepage
collection: click-to-call
priority: 1
key-takeaways:
  c2c: 
    - <code>tel:</code> 스키마를 사용하여 하이퍼링크에 모든 전화번호를 래핑합니다.
    - 항상 국제 전화번호 형식을 사용합니다.
shortlinks: 
  # 참고: 절 제목이나 URL이 변경되면 다음 약식 링크도 업데이트해야 합니다.
  - g.co/mobilesiteprinciple25
---

{% wrap content %}
<style type="text/css">
  img.center {
    display: block;
    margin-left: auto;
    margin-right: auto;
  }
</style>

{% include modules/toc.liquid %}

{% include modules/takeaway.liquid list=page.key-takeaways.c2c %}

## 클릭투콜용 전화번호 링크

대부분의 최신 모바일 브라우저는 자동으로 전화번호를 감지하여 
링크로 변환하지만, 코드에서 직접 이를 수행하는 것이 좋습니다.
각 전화번호를 수동으로 태그로 지정하여 전화번호의 클릭투콜이
항상 활성화되고 사이트와 스타일이 일치되도록 지정할 수 있습니다.

전화번호를 링크로 표시하려면 `tel:` 스키마를 사용합니다.  구문은 
간단합니다.

{% highlight html %}
NIST Telephone Time-of-Day Service <a href="tel:+1-303-499-7111">+1 (303) 499-7111</a>
{% endhighlight %}

결과:

NIST Telephone Time-of-Day Service <a href="tel:+1-303-499-7111">+1 (303) 499-7111</a>

<img src="images/click-to-call_framed.jpg" class="center" alt="클릭투콜의 예.">

전화 기능이 있는 대부분의 장치에서 사용자는 전화를 걸기 전에
 확인 메시지가 표시되므로 속아서 비싼 장거리 전화나
 프리미엄 전화번호로 전화를 거는 일이 예방됩니다. 
전화 통화를 지원하지 않는 장치에서는 전화번호를 브라우저가
처리하는 방식을 선택할 수 있는 메뉴가 표시될 수 있습니다.

음성 통화를 지원하지 않는 데스크톱 브라우저에서는 Google Voice 또는
Microsoft Communicator와 같은 컴퓨터의 기본 전화 통신
앱이 열립니다.

## 국제 전화번호 형식 사용

항상 국제 전화번호 형식(+ 기호, 국가 코드, 지역 코드 및 번호)을 
사용하여 전화번호를 제공합니다.  절대적으로
필요하지 않을 경우 가독성을 높이고 자동 감지 기능 향상을 위해 하이픈(-)을 사용하여
번호의 각 부분을 분리하는 것이 좋습니다.

하이픈으로 구분된 국제 전화번호 형식을 사용하면 몇 백 미터가
떨어져 있든 몇 천 킬로미터가 떨어져 있든 그 발신
위치에 상관없이 통화가 연결됩니다.

## 필요한 경우 자동 감지 기능 비활성화

최신 모바일 브라우저는 자동으로 전화번호를 감지하고
클릭투콜을 활성화합니다.  Mobile Safari는 관련된 하이퍼링크 스타일을 사용하여
전화번호를 링크로 자동 변환합니다.  Android용 Chrome은 자동으로
전화번호를 감지하고 사용자가 클릭투콜을 사용하도록 허용하지만,
전화번호를 하이퍼링크에 래핑하거나 특수 스타일을 적용하지는 않습니다.

Mobile Safari가 자동으로 전화번호를 감지하지 않도록 하려면 다음
메타 태그를 페이지 맨 위에 추가하십시오.

{% highlight html %}
<meta name="format-detection" content="telephone=no">
{% endhighlight %}

## 기타 클릭투콜 기능

일부 최신 브라우저는 `tel:` 스키마뿐만 아니라 `sms:` 및 `mms:` 스키마도
지원합니다. 그러나 지원이 일관되지 않고 메시지 본문 설정과 같은 일부
기능이 작동하지 않는 경우가 있습니다.  

{% endwrap %}
