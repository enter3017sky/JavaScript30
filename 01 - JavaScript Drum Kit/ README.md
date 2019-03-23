# Drum Kit

既然決心要走前端，而且覺得 JavaScript 真的是很好，所以就利用 JS30 熟悉一下，順便複習吧！
這個專案一開始就附上了 html, css，所以就專注在 js 的部分吧。

```html
  <div class="keys">
    <div data-key="65" class="key">
      <kbd>A</kbd>
      <span class="sound">clap</span>
    </div>
    <div data-key="83" class="key">
      <kbd>S</kbd>
      <span class="sound">hihat</span>
    </div>
    <div data-key="68" class="key">
      <kbd>D</kbd>
      <span class="sound">kick</span>
    </div>
    <div data-key="70" class="key">
      <kbd>F</kbd>
      <span class="sound">openhat</span>
    </div>
    <div data-key="71" class="key">
      <kbd>G</kbd>
      <span class="sound">boom</span>
    </div>
    <div data-key="72" class="key">
      <kbd>H</kbd>
      <span class="sound">ride</span>
    </div>
    <div data-key="74" class="key">
      <kbd>J</kbd>
      <span class="sound">snare</span>
    </div>
    <div data-key="75" class="key">
      <kbd>K</kbd>
      <span class="sound">tom</span>
    </div>
    <div data-key="76" class="key">
      <kbd>L</kbd>
      <span class="sound">tink</span>
    </div>
  </div>

  <audio data-key="65" src="sounds/clap.wav"></audio>
  <audio data-key="83" src="sounds/hihat.wav"></audio>
  <audio data-key="68" src="sounds/kick.wav"></audio>
  <audio data-key="70" src="sounds/openhat.wav"></audio>
  <audio data-key="71" src="sounds/boom.wav"></audio>
  <audio data-key="72" src="sounds/ride.wav"></audio>
  <audio data-key="74" src="sounds/snare.wav"></audio>
  <audio data-key="75" src="sounds/tom.wav"></audio>
  <audio data-key="76" src="sounds/tink.wav"></audio>
```

```js
window.addEventListener('keydown', (e) => {
    console.log(e)
    console.log(e.keyCode)
})
```

首先我們可以注意到 html 裡面的元素都有 _data-key_ 的屬性，所以透過監聽 window 的 keydown 事件，然後取得 _e.keyCode_。

```js
const playSound = function(e) {
    let keyCode = e.keyCode || this.getAttribute('data-key')
    const key = document.querySelector(`div[data-key="${keyCode}"]`)
    const audio = document.querySelector(`audio[data-key="${keyCode}"]`)
    if(!audio) return;
    audio.currentTime = 0 // 返回 0 秒, 連續點按才跟得上
    audio.play()
    key.classList.add('playing')
}
```

- 搭配很方便的 ES6 的模板字符串(Template literals)，當你按按鍵時透過 event 取得的 e.keyCode，利用模板字符串套用在 HTML 的 data-key，兩者互相搭配，很方便。

Element.getAttribute(): var attribute = element.getAttribute(attributeName);
getAttribute 方法是透過屬性的名稱取得屬性的值。

- 利用 _this.getAttribute('data-key')_ 取得滑鼠點擊取得 **data-key** 的值。

- audio.currentTime: 當前時間設定為 0 秒

```js
    /** 
     * 函式陳述式(function statement): function() {}
     * 函式表達式(function expression): const fn = function() {}
     * 箭頭函式(arrow function): const fn = () => {}
     */

    const playSound = function(e) {
        let keyCode = e.keyCode || this.getAttribute('data-key')
        const key = document.querySelector(`div[data-key="${keyCode}"]`)
        const audio = document.querySelector(`audio[data-key="${keyCode}"]`)
        if(!audio) return;
        audio.currentTime = 0 // 返回 0 秒, 連續點按才跟得上
        audio.play()
        key.classList.add('playing')
    }

/** 使用 console.log(e) 看看監聽 key 的 transitionend 事件後發生什麼事情，
 *  我在 console 可以發現 propertyName 屬性中的 transform 就是我們要找的。
 *  ps. Arrow function 不適合用在 addEventListener ，因為無所透過 .call() .bind() .apply() 改變 this 的指向(想想 react 中的 arrow function)
 */

    const removeTransition = function(e) {
        if(e.propertyName !== 'transform') return;
        this.classList.remove('playing')
    }

    // function removeTransition(e) {
    //     if(e.propertyName !== 'transform') return;
    //     // this.classList.remove('playing')
    // }

/**     // 返回 NodeList(9)
 * 使用 document.querySelectorAll() 方法與  Node.childNodes 屬性會取得 NodeList 物件(節點的集合)，NodeList 不是陣列，但可以使用 forEach() 迭代，但老舊瀏覽器不支援。
 * transitionend 事件會在 CSS transition 結束後觸發。
 */

    keys = document.querySelectorAll('.key')
    keys.forEach(key => {
        key.addEventListener('transitionend', removeTransition)
        key.addEventListener('click', playSound)
    })

    // 幫 window 新增 keydown 的監聽事件
    window.addEventListener('keydown', playSound)
```


## 自己練習一次

用自己的想法寫了一次

透過監聽 click 事件取得的 _this.getAttribute("data-key")_ 是 string
透過監聽 keydown 事件取得的 _e.keyCode_ 是數字

用 forEach 遍歷 .key audio，符合 keyCode 的才執行。感覺很耗效能。

```js

function playSound(e) {
  // e.keyCode => number, this.getAttribute("data-key") => string
    let keyNum = Number(e.keyCode || this.getAttribute("data-key"))
    let keys = document.querySelectorAll('.key')
    let audios = document.querySelectorAll('audio')
    keys.forEach(key => {
        if(Number(key.getAttribute("data-key")) !== keyNum) return
            key.classList.add('playing')
    } )
    audios.forEach(audio => {
        if(Number(audio.getAttribute("data-key")) !== keyNum) return
        audio.currentTime = 0
        audio.play()
    })
}

let keys = document.querySelectorAll('.key')
keys.forEach(key => {
    key.addEventListener('transitionend', function(e) {
        this.classList.remove('playing')
    })
    key.addEventListener('click', playSound)
})

window.addEventListener('keydown', playSound)
```