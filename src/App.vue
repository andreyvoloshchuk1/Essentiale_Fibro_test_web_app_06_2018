<template>
  <div id="app">
      
      <div class="header">
          <img src="./assets/images/logo.svg" alt="" class="logo">
          <img src="./assets/images/install_btn.png" alt="" class="install" v-if="isTablet" @touchend="isOpenPopup = true">
      </div>

      <div class="content">
          <div class="wrap">
              <div class="left">
                  <div class="calc">
                      <h6 class="calc__caption" v-html="t.calcCaption"></h6>

                      <p class="calculate-text" v-if="isPhone" v-html="t.calcTextPhone"></p>

                      <div class="calc__field" v-if="!isCalculate">
                          <div class="calc__item" v-for="(value, key) in t.calc" :class="{red: isRed[key]}">
                              <input class="calc__input" type="text" :placeholder="value.placeholder" :class="[{changevalue: key === 1}, {red: isRed[key]}]" v-model="inputValue[key]" @input="validateInput(key)">
                              <p class="item-text" v-html="value.name"></p>
                              <div class="change" v-if="key === 1">
                                  <div class="change__item" v-for="(val, key) in value.btns" v-html="val" :class="{active: changeVal[key]}" @click="changeValue(key)" @touchend="changeValue(key)" ></div>
                              </div>
                          </div>
                          <div class="btn-wrap">
                              <button class="calc__btn" v-html="t.calcBtn" :disabled="isDisabled" @click="calculate" @touchend="calculate"></button>
                          </div>
                      </div>

                      <transition name="fade">
                          <div class="result" v-if="isCalculate" :class="[{low: this.sickStage === 0}, {medium: this.sickStage === 1}, {high: this.sickStage === 2}]">
                              <div class="result__rate">
                                  <p class="result__value" v-html="sickValueComp"></p>
                              </div>

                              <p class="result__stage" v-html="sickStageTextComp"></p>
                              <p class="result__info" v-html="sickText"></p>
                              <button class="restart" v-html="restart" @click="restartCalc"></button>
                          </div>
                      </transition>
                  </div>

                  <div class="rules">
                      <div class="rules__item" v-for="(value, key) in t.rules" :class="`rules__item_${key}`" :key="key">
                          <div class="rules__head" v-html="value.question" @touchend="showText(key)"></div>
                          <transition name="fade">
                              <p class="rules__text" v-html="value.answer" v-if="phoneIf(key)"></p>
                          </transition>
                      </div>
                  </div>
              </div>

              <div class="right">
                  <img src="./assets/images/pack.png" alt="" class="pack">

                  <div class="dose">
                      <p class="dose__caption" v-html="t.dose.captionFirst"></p>
                      <ul class="dose__list">
                          <li class="dose__item" v-for="(value, key) in t.dose.listFirst" v-html="value" :class="`dose__item_${key}`"></li>
                      </ul>
                      <p class="dose__caption" v-html="t.dose.captionSecond"></p>
                      <ul class="dose__list dose__list_1">
                          <li class="dose__item" v-for="(value, key) in t.dose.listSecond" v-html="value" :class="`dose__item_${key}`"></li>
                      </ul>
                  </div>
              </div>
          </div>

          <div class="references">
              <ul class="references__list">
                  <li class="references__item" v-for="(value, key) in t.references" v-html="value"></li>
              </ul>
              <img class="logo-sanofi" src="./assets/images/logo_sanofi.png" alt="">
          </div>
      </div>
      <div class="footer">
          <p class="footer__text" v-html="t.footer"></p>
      </div>

      <transition name="fade">
      <div class="popup" v-if="isOpenPopup">
          <div class="popup-wrap">
            <p class="popup__title" v-html="t.popup.popupTitle"></p>
            <div class="os">
                <p class="os__caption os_android" v-html="t.popup.os.android.caption"></p>
                <ul class="os__list">
                    <li class="os__item"><span>1.</span> Натиснути кнопку <img src='./assets/images/ext.png'> браузера</li>
                    <li class="os__item" v-html="t.popup.os.android.list[1]"></li>
                </ul>
            </div>
              <div class="os">
                  <p class="os__caption os_apple" v-html="t.popup.os.apple.caption"></p>
                  <ul class="os__list">
                      <li class="os__item"><span>1.</span> Натиснути кнопку <img src='./assets/images/exp_ios.png'> браузера</li>
                      <li class="os__item" v-html="t.popup.os.apple.list[1]"></li>
                  </ul>
              </div>
              <div class="close" @touchend="isOpenPopup = false"></div>
              <button class="accept" @touchend="isOpenPopup = false" v-html="t.popup.accept"></button>
          </div>
      </div>
      </transition>
  </div>
</template>

<script>
    import text from "@/data/app.js"

    export default {
      name: 'app',

      data(){
          return {
              t: text,
              isPhone: false,
              isTablet: false,

              rulesKey: Number,

              isOpenPopup: false,

              isShowText: {
                  0: false,
                  1: false,
                  2: false
              },

              changeVal: {
                  0: false,
                  1: true
              },

              inputValue: {
                  0: "",
                  1: "",
                  2: "",
                  3: ""
              },

              isRed: {
                  0: false,
                  1: false,
                  2: false,
                  3: false
              },

              isDisabled: true,
              isCalculate: false,

              sickStage: 0,

              sickValue: "",
              sickStageText: "",
              sickText: "",
              restart: "Розрахувати знову"
          }
      },

        computed: {
          sickValueComp: function(){
              return this.sickValue
          },
          sickStageTextComp: function(){
              return this.sickStageText;
          }
        },

        mounted(){
            let dragging = false,
                body = document.getElementsByTagName("BODY")[0],
                elem = document.getElementsByClassName('rules__head');
            if (window.navigator.standalone === true) {
                document.getElementsByClassName('install')[0].style.display = 'none'
            }

            function preventTouch(e){
                e.preventDefault();
            }
            if (window.innerWidth > 770){
                document.addEventListener("touchmove", preventTouch)
            }
            window.addEventListener("resize", function(){
                if (window.innerWidth > 770){
                    document.addEventListener("touchmove", preventTouch)
                }
                else {
                    document.removeEventListener("touchmove", preventTouch)
                }

                if (window.innerWidth <= 768){
                    this.isPhone = true;
                }
                else {
                    this.isPhone = false;
                }
            });

            if (navigator.userAgent.indexOf('Safari') != -1 &&
                navigator.userAgent.indexOf('Chrome') == -1) {
                document.body.className += " safari";
            }
            if (navigator.userAgent.match(/Android|BlackBerry|iPhone|iPad|iPod|Opera Mini|IEMobile/i)){
                this.isTablet = true
            }

            this.$nextTick(function(){
                if (window.innerWidth <= 768){
                    this.isPhone = true;
                }
                if (navigator.userAgent.match(/iPhone/i)){
                    this.isPhone = true;
                }
                else {
                    body.addEventListener('touchmove', function(e){
                        dragging = true;
                    });

                    for (let i = 0; i < elem.length; i++){
                        elem[i].addEventListener("touchend", function(){
                            if (dragging)
                                return;
                        })
                    }

                    body.addEventListener('touchstart', function(){
                        dragging = false;
                    });
                }
            });

        },

      methods: {
          showText: function(key){
           // this.isShowText[key] = !this.isShowText[key];
              this.rulesKey = key;
            this.isShowText[key] = !this.isShowText[key];
          },

          phoneIf: function(key){
              if (navigator.userAgent.match(/iPhone/i)){
                  return this.isShowText[key]
              }
              return this.isShowText[key] || !this.isPhone;
          },

          restartCalc: function(){
              this.isCalculate = false;
              this.isDisabled = true;
              this.inputValue = {
                  0: "",
                  1: "",
                  2: "",
                  3: ""
              }
          },

          changeValue: function(key){
              if (this.changeVal[key] === true){
                  return
              }
              else {
                  this.changeVal = {
                      0: false,
                      1: false
                  };
                  this.changeVal[key] = true
              }
          },

          validateInput: function(key){
            let value = Number(this.inputValue[key]),
                count = 0;

            this.inputCount = 0;
            this.isRed[key] = false;

            if(isNaN(value)){
                this.isRed[key] = true;
            }

            if(key === 0){
                if (value <= 0 || value > 100){
                    this.isRed[key] = true;
                }
            }

            else if(key === 1){
                if (value <= 0 || value > 1200){
                    this.isRed[key] = true;
                }
            }

            else if(key === 2){
                if (value <= 0 || value > 10000){
                    this.isRed[key] = true;
                }
            }

            else if(key === 3){
                if (value <= 0 || value > 4000){
                    this.isRed[key] = true;
                }
            }
              if(value === 0){
                  this.$set(this.isRed, key, false);
                  console.log(this.isRed);
              }

            for (key in this.isRed){
                if (this.isRed[key] === false && this.inputValue[key] !== ''){
                    count++
                }
            }

            if (count === 4){
                this.isDisabled = false;
            }
            else {
                this.isDisabled = true;
            }
          },


          calculate(e){
            if (this.isPhone) {
              window.scrollTo(0,0);
              document.querySelector('.header').scrollIntoView();
            }

              if (e.target.hasAttribute('disabled')){
                  return
              }

              let age = this.inputValue[0],
                  astLevel = this.inputValue[2],
                  plateletCount = this.inputValue[1],
                  alt = this.inputValue[3],
                  result = 0;

              result = (age * astLevel) / (plateletCount * Math.sqrt(alt));
              result = (Math.round(result * 100) / 100);

              if (result < 1.45){
                  this.sickStage = 0;
                  this.sickStageText = "Стадія фіброзу: <span>0-1</span>";
                  this.sickText = "<span><1,45</span> – з достовірністю 90% можна вважати, що <br>значимий фіброз печінки <span>відсутній</span>"
              }
              else if (result >= 100){
                  this.sickStage = 2;
                  this.sickStageText = "Стадія фіброзу: <span>4-6</span>";
                  this.sickText = "<span>>3,25</span> – з достовірністю 65% можна вважати, що <br>наявний <span>обширний фіброз</span>";
                  result = ">100"
              }
              else if (result > 3.25){
                  this.sickStage = 2;
                  this.sickStageText = "Стадія фіброзу: <span>4-6</span>";
                  this.sickText = "<span>>3,25</span> – з достовірністю 65% можна вважати, що <br>наявний <span>обширний фіброз</span>"
              }
              else {
                  this.sickStage = 1;
                  this.sickStageText = "Стадія фіброзу: <span>2-3</span>";
                  this.sickText = "<span>1,45-3,25</span> – <span>наявний фіброз</span>, необхідна біопсія для <br>точного визначення стадії"
              }

              this.sickValue = "<span>" + result + "</span> бала";

              this.isCalculate = true;


          }
      },


      components: {

      }
    }
</script>

<style lang="scss">
    @import "./style/normalize.scss";
    @import "./style/fonts.scss";
    @import "./style/variables.scss";

    body {
        font-family: $font-regular;
        color: $text;
        overflow-x: hidden;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;

        box-sizing: border-box;
    }

    .header {
        width: 100%;
        height: 8.3vh;
        position: relative;

        background-color: $orange;
    }

    .logo {
        height: 6vh;
        margin-top: 1.1vh;
        margin-left: 10vw;
    }

    .content {
        width: 100%;
        min-height: 81.7vh;
        position: relative;

        background-image: url(./assets/images/bg.png);
        background-repeat: no-repeat;
        background-position: top right;
    }

    .left {
        width: calc(66% - 170px);

        margin-left: 10vw;
        margin-top: 4vh;
        display: inline-block;
    }

    .calc {

    }

    .calc__caption {
        font-family: $font-regular;
        font-size: 36px;
        color: $text;
        margin: 0;

        span {
            font-family: $font-bold;
            font-size: 72px
        }
    }

    .calc__field {
        width: 100%;
        height: auto;

        display: flex;
        justify-content: space-between;
        flex-wrap: wrap;
    }

    .calc__item {
        width: 26vw;
        height: 60px;
        position: relative;
        margin-bottom: 5vh;

    /*    &.red:after {
            content: "Невірне значення";
            position: absolute;
            bottom: -82px;
            left: 20px;

            font-family: $font-thin;
            font-size: 18px;
            color: #953412;
        } */
    }

    .calc__input {
        width: 100%;
        height: 50px;
        padding-left: 20px;
        margin-top: 50px;
        box-sizing: border-box;
        position: relative;
        z-index: 1;

        border: 1px solid $orange;

        font-size: 18px;

        &::placeholder {
            opacity: 0.6;
        }
        &:focus::-webkit-input-placeholder { color:transparent; }
        &:focus:-moz-placeholder { color:transparent; } /* FF 4-18 */
        &:focus::-moz-placeholder { color:transparent; } /* FF 19+ */
        &:focus:-ms-input-placeholder { color:transparent; }

        &.red {
            border: 2px solid #953412;
            background-color: rgba(255, 0, 0, 0.2);
        }
    }

    .item-text {
        padding: 2px 10px 0 10px;
        background-color: #fff;

        font-family: $font-bold;
        font-weight: bold;
        color: #555;

        position: absolute;
        top: 25px;
        left: 10px;
        z-index: 1;
    }

    .btn-wrap {
        width: 100%;
    }

    .calc__btn {
        width: 340px;
        height: 60px;
        float: right;
        position: relative;
        margin: 15px 50px 15px 0;

        background-color: $green;
        border: 0;

        font-family: $font-bold;
        font-size: 28px;
        color: #fff;

        &:before {
            content: "";
            width: 0;
            height: 0;
            border-top: 30px solid transparent;
            border-right: 18px solid $green;
            border-bottom: 30px solid transparent;

            position: absolute;
            left: -18px;
            top: 0px;
        }

        &:after {
            content: "";
            width: 0;
            height: 0;
            border-top: 30px solid transparent;
            border-left: 18px solid $green;
            border-bottom: 30px solid transparent;

            position: absolute;
            right: -18px;
            top: 0px;
        }

        &:disabled{
            opacity: 0.5;
        }
    }

    .rules {
        clear: both;

        display: flex;
        justify-content: space-between;
        flex-wrap: wrap;;
    }

    .rules__item {
        &_0 {
            width: 28%;
        }
        &_1 {
            width: 28%;
        }
        &_2 {
            width: 42%;
        }
    }

    .rules__head {
        width: calc(100% - 30px);
        height: 50px;
        position: relative;
        padding: 14px 8px;
        -webkit-box-sizing: border-box;
        -moz-box-sizing: border-box;
        box-sizing: border-box;

        background-color: $orange;

        font-family: $font-thin;
        color: #fff;
        font-size: 1vw;
        p{
            margin: 0;
        }

        &:before {
            content: "";
            width: 0;
            height: 0;
            border-top: 25px solid transparent;
            border-right: 14px solid $orange;
            border-bottom: 25px solid transparent;

            position: absolute;
            left: -14px;
            top: 0px;
        }
        &:after {
            content: "";
            width: 0;
            height: 0;
            border-top: 25px solid transparent;
            border-left: 14px solid $orange;
            border-bottom: 25px solid transparent;

            position: absolute;
            right: -14px;
            top: 0px;
        }
    }

    .rules__text {
        width: 90%;

        font-family: $font-thin;
        font-size: 1.2vh;
        line-height: 1.4vh;
        color: #555;

        span {
            display: inline-block;
            width: 33%;
            float: left;
            b {
                font-family: $font-bold;
                font-weight: bold;
            }
        }
    }

    .right {
        width: 30%;
        display: inline-block;
        position: relative;
        float: right;
    }
    .pack {
        width: 40vh;
        height: auto;

        margin: 0 auto;
        margin-top: -6vh;
        display: block;
    }

    .dose {
        width: auto;
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
    }

    .dose__caption {
        margin: 0;
        margin-top: 20px;

        font-family: $font-bold;
        font-weight: bold;
        font-size: 2.4vh;
        color: #555;
    }

    .dose__list {
        list-style-type: none;
        margin: 0;
    }
    .dose__item {
        margin: 10px 0;
        position: relative;
        padding-left: 20px;

        font-family: $font-regular;
        font-size: 2.4vh;
        color: $text;

        &:before {
            position: absolute;
            left: -20px;
        }

        &_0:before {
            content: url(./assets/images/icon_tablet.png);
        }
        &_1:before {
            content: url(./assets/images/icon_calendar.png);
        }
        &_2:before {
            content: url(./assets/images/icon_clock.png);
        }

        span {
            font-family: $font-bold;
            font-weight: bold;
        }
    }

    .dose__list_1 {
        .dose__item {
            &_0:before{
                content: url(./assets/images/icon.png);
                left: -30px;
                top: 10px;
            }
        }
    }
    
    .references {
        width: 100%;
        padding: 5px 0 5px 10vw;

        background-color: #F5F5F5;

        font-family: $font-regular;
        color: $text;
        font-size: 8px;
        line-height: 10px;
        opacity: 0.6;
        box-sizing: border-box;

        position: absolute;
        bottom: 0;
    }

    .logo-sanofi {
        position: absolute;
        left: 77vw;
        top: 50%;
        transform: translateY(-50%);
    }

    .references__list {
        padding: 0 10px;
        margin: 0;
        counter-reset: item;

    }
    .references__item {
        width: 56%;
        margin: 0;
        padding: 0 0 0 2em;
        text-indent: -2em;
        list-style-type: none;
        counter-increment: item;

        &:before {
            display: inline-block;
            width: 1em;
            padding-right: 0.5em;
            font-family: $font-bold;
            text-align: right;
            content: counter(item) ".";
        }
    }
    
    .footer {
        width: 100%;
        height: 10vh;
        background-color: #c9cfcf;

        position: fixed;
        bottom: 0;
        left: 0;

        display: flex;
        align-items: center;
        justify-content: center;
        z-index: 100;
    }
    .footer__text {
        margin: 0;

        font-size: 3.85vw;
        text-align: center;
        font-family: $font-thin;
        color: #868686;
    }

    .changevalue {
        width: 84%;
        display: inline-block;
    }
    .change {
        width: 60px;
        height: 60px;
        display: flex;
        flex-wrap: wrap;
        border: 1px solid $orange;
        float: right;
        margin-top: 45px;
        cursor: pointer;
        z-index: 100;
        position: relative;
    }

    .change__item {
        width: 100%;
        height: 50%;
        display: flex;
        align-items: center;
        justify-content: center;

        font-family: $font-regular;
        font-size: 14px;
        color: $text;
        opacity: 0.5;

        &.active {
            background-color: $orange;
            color: #fff;
            opacity: 1;
        }
    }

    .result {
        &:after {
            content: "";
            clear: both;
            display: table;
        }
        &.low {
            .result__rate {
                background-image: url(./assets/images/low.png);
            }
            .result__value {
                color: $green;
            }
            .result__stage {
                color: $green;
            }
        }
        &.medium {
            .result__rate {
                background-image: url(./assets/images/medium.png);
            }
            .result__value {
                color: #CC7C04;
            }
            .result__stage {
                color: #CC7C04;
            }
        }
        &.high {
            .result__rate {
                background-image: url(./assets/images/high.png);
            }
            .result__value {
                color: #953412;
            }
            .result__stage {
                color: #953412;
            }
        }
    }

    .result__rate {
        width: 340px;
        height: 360px;
        margin: 0px 0 20px 0;
        position: relative;
        display: inline-block;

        background-position: center center;
        background-repeat: no-repeat;
        -webkit-background-size: contain;
        background-size: contain;

    }

    .result__value {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        margin: 0;

        font-family: $font-bold;
        font-weight: bold;
        font-size: 48px;
        text-align: center;

        span {
            font-size: 100px;
        }
    }

    .calculate-text {
        font-family: $font-bold;
        font-size: 22px;
        color: #555;
        opacity: 0.6;
        text-align: center;

        margin: 0;
        margin-bottom: 20px;
        transform: translateY(40px);
    }

    .install {
        position: absolute;
        right: 20px;
        top: 50%;
        transform: translateY(-50%);
    }

    .result__rate {
        float: left;
    }
    .result__stage {
        font-size: 3.3vw;
        font-family: $font-bold;
        font-weight: bold;
        margin: 0;
        position: relative;
        top: 70px;
    }
    .result__info {
        font-family: $font-regular;
        font-size: 1.4vw;
        line-height: 34px;
        color: $text;

        position: relative;
        top: 90px;
        margin: 0;

        span {
            font-family: $font-bold;
            font-weight: bold;
        }
    }

    .restart {
        width: 380px;
        height: 60px;
        padding-left: 40px;

        background-image: url(./assets/images/Rectangle.png);
        background-position: center center;
        background-color: transparent;
        border: 0;

        font-family: $font-bold;
        font-size: 28px;
        color: #fff;

        float: right;
        position: relative;
        top: 140px;

        &:before {
            content: url(./assets/images/Ellipse.png);
            position: absolute;
            left: 20px;
            top: 4px;
        }
    }

    .popup {
        width: 100%;
        height: 100vh;

        position: fixed;
        top: 0;
        left: 0;
        z-index: 200;

        background-color: rgba(0, 0, 0, 0.4);
    }
    .popup-wrap {
        width: 320px;
        height: 470px;
        padding: 0 20px;
        box-sizing: border-box;

        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background-color: #fff;
    }

    .popup__title {
        margin: 40px 0 30px 0;

        font-family: $font-thin;
        font-size: 20px;
    }

    .os {
        margin-bottom: 30px;

        &_android{
            &:after {
                content: url(./assets/images/android_icon.png);
                position: absolute;
                left: 0;
                top: -2px;
            }
        }
        &_apple{
            &:after {
                content: url(./assets/images/apple.png);
                position: absolute;
                left: -4px;
                top: -10px;
            }
        }
    }

    .os__caption {
        font-family: $font-bold;
        font-size: 16px;

        position: relative;
        padding-left: 30px;
    }

    .os__list {
        list-style-type: none;
        padding-left: 30px;
    }

    .os__item {
        font-size: 16px;
        margin-bottom: 10px;

        span {
            font-family: $font-bold;
        }
    }

    .close {
        width: 40px;
        height: 40px;

        background-image: url(./assets/images/close.png);
        background-position: center center;
        -webkit-background-size: 100% 100%;
        background-size: 100% 100%;

        position: absolute;
        right: 10px;
        top: 10px;
    }

    .accept {
        width: 200px;
        height: 50px;

        background-image: url(./assets/images/ok_btn.png);
        background-position: center center;
        -webkit-background-size: 100% 100%;
        background-size: 100% 100%;
        background-color: transparent;
        border: 0;

        font-family: $font-thin;
        font-size: 20px;
        color: #fff;

        position: absolute;
        bottom: 20px;
        left: 50%;
        transform: translateX(-50%);
    }

    .safari {
        .rules__head {
            &:before {
                left: -13px;
            }
            &:after {
                right: -13px;
            }
        }
        .calc__btn {
            &:before {
                left: -17px;
            }
            &:after {
                right: -17px;
            }
        }
    }

    @import "./style/media.scss";
</style>
