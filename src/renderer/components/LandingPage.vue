<template>
  <div id="wrapper">
    <!-- <img id="logo" src="~@/assets/logo.png" alt="electron-vue"> -->
    <!-- <system-information></system-information> -->
    <main>
      <div class="container">

        <button class="btn-play" v-on:click="play">Play</button>
        <button class="btn-play" v-on:click="pause">Pause</button>
        <button class="btn-play" v-on:click="back">back</button>
        
        <div class="preview-container">
          <div class="preview">
            <div class="preview-inner" id="previewInner">
              <ul class="preview-list" ref="previewList" id="previewList" :style="{ left: listPosition+'px' }">
                <li v-for="item in numbers">
                  {{ item }}
                </li>
              </ul>
            </div>
          </div>
        </div>
        
        <div class="form-container">
          <form class="insert-form"> 
            <button class="btn-return" v-on:click="removeFromList">-</button>
            <input type="number" class="input" v-model="currentNum" placeholder="">
            <button class="btn-return" v-on:click="addToList">+</button>
          </form>
        </div>

        <div class="overview-container">
          <ul class="overview">
            <li v-for="item in all()" v-bind:class="{ selected: numbers.indexOf(item) !== -1 }" @click="toggleNumber(item)">
              <span>
                {{ item }}
              </span>
            </li>
          </ul>
        </div>

      </div>
    </main>
  </div>
</template>

<script>
  import SystemInformation from './LandingPage/SystemInformation';

  export default {
    name: 'landing-page',
    components: { SystemInformation },
    data() {
      return {
        currentNum: null,
        numbers: [],
        pastNumbers: [],
        maxPartecipants: 2500,
        listPosition: 0,
        interval: null,
        all() {
          const numbers = [];
          for (let i = 0; i < this.maxPartecipants; i += 1) {
            numbers[i] = i + 1;
          }
          return numbers;
        },
      };
    },
    mounted() {
      this.listPosition = document.getElementById('previewInner').offsetWidth;
    },
    methods: {
      onInit() {
        clearInterval(this.interval);
        this.interval = window.setInterval(this.onTick, 100);
      },
      onTick() {
        const previewInner = document.getElementById('previewInner').offsetWidth;
        const previewListWidth = document.getElementById('previewList').offsetWidth;
        const previewListLeft = parseInt(document.getElementById('previewList').style.left, 0);
        console.log(previewListWidth, previewListLeft, previewInner);

        if (Math.abs(previewListLeft) >= previewListWidth) {
          this.listPosition = previewInner;
        }

        this.listPosition -= 2;
      },
      open(link) {
        this.$electron.shell.openExternal(link);
      },
      play() {
        clearInterval(this.interval);
        this.onInit();
      },
      pause() {
        clearInterval(this.interval);
      },
      back() {
        clearInterval(this.interval);
        this.listPosition = document.getElementById('previewInner').offsetWidth;
      },
      addToList() {
        const num = parseInt(this.currentNum, 0);

        const bool = (
          !Number.isNaN(num) && num > 0
          && num <= this.maxPartecipants
          && this.numbers.indexOf(num) === -1
        );

        if (bool) {
          this.numbers.push(num);
          this.currentNum = null;
        }
      },
      removeFromList() {
        const num = parseInt(this.currentNum, 0);
        const index = this.numbers.indexOf(num);

        const bool = (
          !Number.isNaN(num) && num > 0
          && num <= this.maxPartecipants
          && index !== -1
        );

        if (bool) {
          this.numbers.splice(index, 1);
          this.pastNumbers.push(num);
          this.currentNum = null;
        }
      },
      toggleNumber(num) {
        num = parseInt(num, 0);
        if (num > 0 && num <= this.maxPartecipants && this.numbers.indexOf(num) === -1) {
          this.numbers.push(num);
          this.currentNum = null;
        } else {
          const index = this.numbers.indexOf(num);
          this.numbers.splice(index, 1);
          this.pastNumbers.push(num);
          this.currentNum = null;
        }
        this.numbers.sort((a, b) => a - b);
      },
    },
  };
</script>

<style>
  @import url('https://fonts.googleapis.com/css?family=Source+Sans+Pro');

  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }

  body { font-family: 'Source Sans Pro', sans-serif; }

  .container {
    
  }

  .preview-container {
    height: 10vh;
  }

  .preview {
    width: 10%;
    height: 40px;
    border: 1px solid #ccc;
    margin: 20px auto;
    overflow: hidden;
    padding: 5px;
  }

  .preview-inner {
    position: relative;
    overflow-x: hidden;
    font-size: 20px;
    padding: 2px;
    height: 100%;
  }

  .preview-list {
    position: absolute;
    left: 0;
    top: 0;
    list-style: none;
    white-space: nowrap;
  }

  .preview-list li{
    display: inline-block;
    margin-right: 5px;
    text-align: center;
  }

  .form-container {
    height: 10vh;
  }

  .insert-form {
    width: 200px;
    margin: 20px auto;
    text-align: center;
  }

  .input {
    font-size: 40px;
    height: 40px;
    width: 100px;
    vertical-align: middle;
    text-align: center;
  }

  .btn-return {
    width: 30px;
    height: 40px;
    line-height: 40px;
    font-size: 30px;
    vertical-align: middle;
  }

  .overview-container {
    height: 80vh;
    overflow: scroll;
  }

  .overview {
    display: flex;
    list-style: none;
    flex-wrap: wrap;
  }

  .overview li {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 40px;
    height: 40px;
    margin: 5px;
    text-align: center;
    background-color: #ccc;
    border-radius: 20px;
    cursor: pointer;
  }

  .overview li.selected {
    background-color: green;
    color: white;
  }

  
</style>
