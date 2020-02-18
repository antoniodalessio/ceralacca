<template>
  <div id="wrapper">
    <!-- <img id="logo" src="~@/assets/logo.png" alt="electron-vue"> -->
    <main>
      <div class="container">
        <div class="preview">
          {{ numbers.join("  ") }}
        </div>
        <!-- <system-information></system-information> -->
        <form class="insert-form"> 
          <button class="btn-return" v-on:click="removeFromList">-</button>
          <input type="number" class="input" v-model="currentNum" placeholder="">
          <button class="btn-return" v-on:click="addToList">+</button>
        </form>

        <div>
          <ul class="overview">
            <li v-for="item in all()" v-bind:class="{ selected: pastNumbers.indexOf(item) !== -1 }">
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
        all() {
          const numbers = [];
          for (let i = 0; i < 1000; i += 1) {
            numbers[i] = i + 1;
          }
          return numbers;
        },
      };
    },
    methods: {
      open(link) {
        this.$electron.shell.openExternal(link);
      },
      addToList() {
        const num = parseInt(this.currentNum, 0);
        if (this.numbers.indexOf(num) === -1) {
          this.numbers.push(num);
          this.currentNum = null;
        }
        this.numbers.sort((a, b) => a - b);
      },
      removeFromList() {
        const num = parseInt(this.currentNum, 0);
        const index = this.numbers.indexOf(num);
        if (index !== -1) {
          this.numbers.splice(index, 1);
          this.pastNumbers.push(num);
          this.currentNum = null;
        }
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

  .insert-form {
    width: 200px;
    margin: 20px auto;
    text-align: center;
  }

  .input {
    font-size: 40px;
    height: 40px;
    width: 100px;
  }

  .preview {
    width: 50%;
    height: 40px;
    font-size: 20px;
    border: 1px solid black;
    margin: 20px auto;
  }

  .btn-return {
    width: 30px;
    height: 40px;
    line-height: 40px;
    font-size: 30px;
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
  }

  .overview li.selected {
    background-color: green;
    color: white;
  }

  
</style>
