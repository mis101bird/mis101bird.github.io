---
layout: post
title: "[Web Component v1]JS Framework 以外的另一條道路"
description: "本篇介紹 Web Component v1，並透過範例介紹如何建構Web Component"
modified: "2018-02-10"
tags: [custom element, web component, javascript]
image:
  feature: winter.jpg
  credit: hsuan-ju in Newfoundland, Canada
---

為了維護並修改同事寫的 web component v0 元件，我接觸到這個，標榜可以相容所有 JS Framework 的 web component 技術，在此做個紀錄。

### Web Component 的意義

初入前端領域的小毛頭如我，在學完HTML, DOM, CSS, JS的基礎後，下一個抉擇就是要選什麼 JS Framework 入坑 (ex. Angular, React, VueJS...)。學習 JS Framework 可以增加我們的開發速度，引導我們正確的實踐方法，但每個 Framework 都有各自的生態圈，它們之間的相容性極低，若專案轉換到新的 Framework，舊 Framework 實作出來的 Components 大部分都無法拿到新的 Framework 中使用，過去努力開發的 share component 都付諸流水，這在變化極快的前端領域顯然增加不少成本。web component 被提出來就是希望能解決上述問題，並期望它的四個主要技術可以列入 <a href="https://www.w3.org/standards/techs/components#w3c_all">W3C 擬定的標準</a>：

* Custom elements
* Shadow DOM
* HTML templates
* HTML Imports

目前只有 HTML templates 已經成為標準，其他三個仍在草稿階段，列為標準的好處是瀏覽器會接著跟進實作，如果未來做到全面支援，所有人就能直接利用原生的 BOM API 開發 web component，不只網頁效率倍增 (ex. <a href="https://www.youtube.com/watch?v=4bZvq3nodf4&feature=youtu.be">Alex Russell 在 Chrome Dev Summit 2016</a> 提到，如果使用 Framework，網頁渲染效率就會受限於框架本身，Framework是個對開發者方便，但將效率成本加築在終端使用者上的東西...)，也解決前端框架無法相容的問題了。要撰寫自己的 web component，重點在活用 custom elements API 和 shadow DOM，因為目前各大瀏覽器的支援度仍不足，在開發前要先引入 web component polyfill

```
https://cdnjs.cloudflare.com/ajax/libs/webcomponentsjs/0.7.20/webcomponents.min.js
```

### Custom Element 簡介

> Custom Element是骨幹，讓我們能開發自定義元件，自定義標籤或是做屬性的擴充等...

自定義元件可以使用 ES6 class 來定義，用繼承 HTMLElement 來確保自定義的元件擁有完整的 DOM API，創建自定義元件有以下三個規則:

1. 自定義元件的 TAG 名稱必須包含短橫線(-)。因此，&lt;x-tags&gt;、&lt;my-element&gt; 和 &lt;my-awesome-app&gt; 均爲有效名稱，而 &lt;tabs&gt; 和 &lt;foo_bar&gt; 則爲無效名稱。這個要求是為了讓 HTML 解析器能夠區分自定義元件和 HTML自身元件 (ex. div, span...)
2. 不能多次註冊同一個 TAG，否則將產生 DOMException。
3. 自定義元件不能自我封閉 (ex. &lt;App /&gt;)，因爲 HTML 只允許少數元件自我封閉。所以撰寫時，必須寫成封閉的 TAG (ex. &lt;app-drawer&gt;&lt;/app-drawer&gt;)。

我們可以把 web component 的自定義元件想成 React 的 Component，他和 React Component 一樣有自己的生命週期，可以根據 attribute 的變化觸發 element 做出改變，下面介紹幾個常用的 Custom Elements' Life Cycle methods：

* constructor：Custom Elements 剛建立時呼叫，通常會在這邊註冊 Event listener 或是創建 Shadow Dom。
* connectedCallback：當自定義元件被插入頁面的 DOM 時會被呼叫，很像 React 的 componentDidMount
* disconnectedCallback：當自定義元件被移除時會被呼叫，很像 React 的 componentWillUnmount，可以在這邊 remove Event listener 之類的。
* attributeChangedCallback(attrName, oldVal, newVal)：監聽的屬性有變動時會被呼叫，要搭配 observedAttributes() 使用。
* adoptedCallback：整個 custom element 被人用 document.adoptNode(el) 呼叫時觸發。

以下是自定義元件範例的JS部分，這裡的 this 代表該 custom element 本身，其擁有的 DOM API (ex. hasAttribute) 都可以使用。下方範例裡的 Example class 繼承了 HTMLElement class，開放 ex 屬性給使用者做 getter/setter JS 設置，並監控其屬性變更，我們可以藉由 console.log 觀察剛剛提到的生命週期。

{% highlight javascript %}
class Example extends HTMLElement {

  // A getter/setter for an open property.
  get ex() {
    return this.getAttribute("ex");
  }

  set ex(val) {
    // Reflect the value of the open property as an HTML attribute.
    if (val) {
      console.log("set ex attritube: " + val);
      this.setAttribute('ex', val);
    } else {
      this.removeAttribute('ex');
    }
  }

  set id(val) {
    // Reflect the value of the open property as an HTML attribute.
    if (val) {
      console.log("set id attritube:" + val);
      this.setAttribute("id", val);
    } else {
      this.removeAttribute("id");
    }
  }
  
  static get observedAttributes() {
      return ["ex"];
  }

  attributeChangedCallback(name, oldValue, newValue) {
    const hasValue = newValue !== null;
    switch (name) {
      case "ex":
        console.log("the ex is changed on " + oldValue + " to " + newValue);
        break;
    }
  }

  constructor() {
      super(); // always call super() first in constructor.
      console.log("In constructor");
  }
}

customElements.define('my-example', Example);

// set attribute by JS
document.getElementsByTagName("my-example")[0].ex = "setByJS"

// set id but not trigger attributeChangedCallback
document.getElementsByTagName("my-example")[0].id = "example"
{% endhighlight %}

在 HTML 插入剛剛定義的 custom Tag：

{% highlight html %}
<div>
  <my-example ex="HAHA">
  </my-example>
</div>
{% endhighlight %}

我們可以觀察到 console 輸出為：

![webComp Image 01]({{ site.url }}/images/webComp/01.png)

HTML 解析器解析到 my-example 標籤後，會先運行 constructor，再觸發 attributeChangedCallback，設定 ex 屬性為 HAHA，接著我在 JS 呼叫 set ex method 將 ex 設定為 setByJS，一樣也觸發了 attributeChangedCallback! 接著我們再用 JS 呼叫 set id method，設定屬性 id 為 example，但這次沒有觸發 attributeChangedCallback，因為只有 observedAttributes 所列的屬性被改變，才會觸發 attributeChangedCallback。

有了custom element 所提供的 DOM API，我們就能透過自定義屬性和添加 Event Listener，編寫 custom element 與終端使用者互動的邏輯，再配合 shadow dom 的封裝特性，做出華麗的外觀與變化。如何配合運用會在下面繼續說明。

以上範例原始碼可參考我的CodePen: <a href="https://codepen.io/mis101bird/pen/gvegNZ">Custom Element Example</a>。

### Shadow Dom 簡介

> Shadow Dom 是 web component 的靈魂，它讓開發者可以將自己的元件「模組化」

Shadow Dom 封裝元件內部的 HTML DOM/CSS，使其不受元件外面的環境影響，且因為外部的 CSS 無法直接修改到 shadow dom 的 CSS 內容，故 Shadow Dom 的 CSS class 命名可以和外部的 CSS class 撞名。目前的 HTML5 元件像是 &lt;video&gt;, &lt;audio&gt;, 各種 &lt;input&gt; 等等，都是 Shadow Dom 應用的產物。

要加 Shadow Dom，直接在 custom element class 的 constructor 裡用 this.attachShadow() 綁定 Shadow Dom Tree，官方建議不要用關閉模式 { mode: 'closed' } 創建 Shadow Dom，因為它會讓使用此 custom element 的開發者不能透過任何方式修改該 Shadow Dom 的 style 樣式，使該 custom element 的修改彈性變很低。綁定後，用 innerHTML 直接插入 dom 和 style 字串，之後的撰寫方式都和編寫普通的 HTML/CSS 相同，標籤 &lt;slot&gt; 代表插入 custom element 的 child element 位置所在，我們能用 ::slotted(css slector) 來裝飾該 child element。

以下是JS的範例，style 說如果 child element 為 p，則字的大小設 30px、顏色設為綠色

{% highlight javascript %}
class Example extends HTMLElement {

  constructor() {
      super(); // always call super() first in constructor.
      let shadowRoot = this.attachShadow({mode: 'open'});
      shadowRoot.innerHTML = '<style>h1{ color: red; } ::slotted(p){ color: green; font-size:30px; }</style><div><h1>Hello Shadow DOM</h1><slot></slot></div>';
  }
}

customElements.define('my-example', Example);
{% endhighlight %}

Html範例，my-example標籤內有子元件 &lt;p&gt;I am slot&lt;/p&gt;：

{% highlight html %}
<div>
  <my-example>
    <p>I am slot</p>
  </my-example>
</div>
{% endhighlight %}

以下是呈現出來的畫面，我們可以看到子元件在紅色的 Hello Shadow DOM 後面，且被裝飾成綠色。

![webComp Image 02]({{ site.url }}/images/webComp/02.png)

開 chrome console 可以觀察到 render dom tree 的 my-example 標籤下有 #shadow-root，內能觀察到被渲染的 shadow dom tree，外面的 CSS 是無法修改到此區域的 style

![webComp Image 03]({{ site.url }}/images/webComp/03.png)

以上範例原始碼可參考我的CodePen: <a href="https://codepen.io/mis101bird/pen/JpLNOg">Custom Element Example & Shadow DOM</a>。

### Shadow Dom + HTML template 讓程式更易維護

使用 HTML template 先定義好 Shadow Dom 的 HTML 結構和 style，再在 custom element 的 constructor 內複製 template 的內容節點到 shadow root。這種做法讓 custom element 的 shadow dom tree 更易讀也更好維護。

首先在 HTML 定義 template:

{% highlight html %}
<template id="my-template">
  <style>
    :host { /* 整個 template */
      display: inline-block;
      padding: 20px;
      border: solid 1px red;
    }
    p {
      font-size: 20px;
      color: orange;
    }
    .slot {
      font-size: 20px;
      color: black;
    }
  </style>
  <p>My example</p>
    <div class="slot">
      <slot></slot>
    <div>
</template>
<div>
  <my-example>
    <p>Hello Web!</p>
  </my-example>
</div>
{% endhighlight %}

在 JS 的 custom element 複製 template content node 到 shadow root:

{% highlight javascript %}
class Example extends HTMLElement {

  constructor() {
      super(); // always call super() first in constructor.
      let shadowRoot = this.attachShadow({mode: 'open'});
      const t = document.querySelector('#my-template');
      const instance = t.content.cloneNode(true); // copy template content node
      shadowRoot.appendChild(instance);
  }
}

customElements.define('my-example', Example);
{% endhighlight %}

結果畫面如下，一樣有裝飾到子元件：

![webComp Image 04]({{ site.url }}/images/webComp/04.png)

以上範例原始碼可參考我的CodePen: <a href="https://codepen.io/mis101bird/pen/JpLMXM">Custom Element Example & Shadow DOM & HTML template</a>。

### 從 Web Component v0 到 v1

關於兩個版本的差異可以參考<a href="https://hayato.io/2016/shadowdomv1/">此篇</a>，基本上 v0 的 custom element API 都有被 v1 向下支援，但 v0 的 Shadow piercing combinators 功能，也就是從外部透過 /deep/ 和 ::shadow 更改 shadow dom 的 style 方法已經完全被 v1 棄用，取而代之的是用<a href="https://www.polymer-project.org/2.0/docs/devguide/custom-css-properties"> custom css property </a>釋出 style 接口，讓使用元件的開發者，透過元件作者所定義的 css property 更改 style 樣式。我個人覺得這樣的改版讓元件使用者很不方便，因為元件開發者不太可能知道元件使用者要怎麼裝飾元件，所以不一定能提供出足夠多的客製 css properties 出來。

### 實例

<a href="https://m.tw.buy.yahoo.com/gdsale/gdsale.asp?gdid=7525613">Yahoo 購物中心商品頁</a> 的手機版，下方出現的 App 下載橫幅就是由 web component 做的！觀看時請開無痕，並開Dev Tool Console 轉到 Mobile mode ~

---------------------------------------
### 參考
* 更多關於 web component 的奧妙都能參考以下！
* <a href="https://developers.google.com/web/fundamentals/web-components/">Google Web Component Overview</a>
* <a href="https://developer.mozilla.org/en-US/docs/Web/Web_Components">MDN Web Component Overview</a>



