<template>
    <div class="phone">
        <div class="top">
            <button @click="showHistory" class="history"></button>
            <button @click="deleteValue" class="delete"></button>
        </div>

        <ul v-if="isHistory" class="historyDetails">
            <li :key="operation.operations" v-for="operation in historyData" >
                <small>{{operation.operations}}</small>
                <h2>{{operation.sum}}</h2>
            </li>
        </ul>

        <div class="middle">
            <small v-if="selectedOperation">{{ prevNum }} {{ selectedOperation }} {{ currentNum }}</small>
            <div>
                <h1>{{currentNum}}</h1>
            </div>
        </div>
        <div class="bottom">
            <button @click="pressed('c')">c</button>
            <button @click="pressed('()')">()</button>
            <button @click="pressed('%')">%</button>
            <button @click="pressed('/')">/</button>

            <button @click="pressed('7')">7</button>
            <button @click="pressed('8')">8</button>
            <button @click="pressed('9')">9</button>
            <button @click="pressed('*')">*</button>

            <button @click="pressed('4')">4</button>
            <button @click="pressed('5')">5</button>
            <button @click="pressed('6')">6</button>
            <button @click="pressed('-')">-</button>

            <button @click="pressed('1')">1</button>
            <button @click="pressed('2')">2</button>
            <button @click="pressed('3')">3</button>
            <button @click="pressed('+')">+</button>
            
            <button @click="negateValue">+/-</button>
            <button @click="pressed('0')">0</button>
            <button @click="pressed('.')">.</button>
            <button @click="pressed('=')">=</button>
        </div>
    </div>
</template>

<script>
import { ref, onMounted } from "vue";
export default {
  setup() {
    const operations = ["+", "-", "*", "/", "%", "()"];
    const numbers = ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0", "."];
    const currentNum = ref("");
    const prevNum = ref("");
    const selectedOperation = ref("");
    const total = ref('')
    const isHistory = ref(false);
    const pressed = (value) => {
      if (value === "=" || value === "Enter") {
          historyData.push({
              operations: `${prevNum.value} ${selectedOperation.value} ${currentNum.value}`,
            sum: eval(`${prevNum.value} ${selectedOperation.value} ${currentNum.value}`)
        })
          calculate();
          console.log(historyData);

      }
      else if (value === "%") percentage();
      else if (value === "c") clear();
      else if (operations.includes(value)) applyOperation(value);
      else if (numbers.includes(value)) appendNumber(value);
    }
    const appendNumber = (value) => {
      currentNum.value = currentNum.value + value;
    }
    const applyOperation = (value) => {
      calculate();
      prevNum.value = currentNum.value;
      currentNum.value = "";
      selectedOperation.value = value;
    }
    const calculate = () => {
      if (selectedOperation.value === "*") multiply();
      if (selectedOperation.value === "()") multiply();
      else if (selectedOperation.value === "/") divide();
      else if (selectedOperation.value === "%") percentage();
      else if (selectedOperation.value === "-") subtract();
      else if (selectedOperation.value === "+") sum();
      prevNum.value = "";
      selectedOperation.value = "";
    }
    const multiply = () => {
      currentNum.value = prevNum.value * currentNum.value;
      total.value = currentNum.value;
    }
    const divide = () => {
      currentNum.value = prevNum.value / currentNum.value;
      total.value = currentNum.value;

    }
    const percentage = () => {
      currentNum.value = currentNum.value / 100;
      total.value = currentNum.value;

    }
    const subtract = () => {
      currentNum.value = prevNum.value - currentNum.value;
      total.value = currentNum.value;

    }
    const sum = () => {
      currentNum.value = +prevNum.value + +currentNum.value;
      total.value = currentNum.value;

    }
    const clear = () => {
     currentNum.value = ""
     prevNum.value = ""
     selectedOperation.value = ""
     currentNum.value = ""
    }

    const deleteValue = () => {
      if(currentNum.value.length !== 0 && currentNum.value !== '0') {
        currentNum.value = currentNum.value.slice(0, -1)
      }
    }   

    const negateValue = () => {
        if(currentNum.value  === '0') {
            return currentNum.value  = '-0'
        }
        if(currentNum.value === '-') {
        return currentNum.value = '0'
        }
        if(currentNum.value === '0.') {
            return currentNum.value = `-${currentNum.value}`
        }
        if(currentNum.value === '-0.') {
            return currentNum.value = '0.'
        }
        currentNum.value = `${currentNum.value * -1}` 
    }

    let historyData = [];
    
    const showHistory = () => {
        isHistory.value = !isHistory.value;
    }

    const handleKeydown = (e) => {
        pressed(e.key);
    }
    onMounted(() => window.addEventListener('keydown', handleKeydown));

    return { currentNum, pressed, selectedOperation, prevNum, negateValue, deleteValue, total, historyData, isHistory, showHistory };
  },
};
</script>

<style scoped>
    .phone {
        background-color: #6E3DD6;
        width: 20rem;
        /* height: 70vh; */
        border-radius: 2rem;
        margin: auto;
        position: relative;
    }
    .top {
        display: flex;
        justify-content: space-between;
        margin-bottom: 2rem;
        padding: 1rem;

    }
    .history {
        background: url('../assets/history.svg') no-repeat top left;
        height: 2rem;
        width: 2rem;
    }
    .historyDetails {
        position: absolute;
        bottom: -130px;
        left: 0;
        background-color: #fff;
        padding: 1.4rem 1rem;
        border-radius: 2rem;
        height: 23rem;
        width: 20rem;
        z-index: 999;
        overflow-y: scroll;
    }
    .historyDetails li {
        border-bottom: 1px solid gray;
        padding-bottom: 10px;
    }
    .delete {
        background: url('../assets/delete.svg') no-repeat top left;
        height: 2rem;
        width: 2rem;
    }
    .middle {
        text-align: center;
        margin-bottom: 3rem;
        height: 6rem;
    }
    .middle div{
        overflow-x: scroll;
        margin-top: 1rem;
    }
    .middle small {
        font-weight: bold;
        color: #B9A3EC;
        font-size: .75rem;
    }
    .middle h1 {
        color: white;
        font-size: 2.5rem;
        font-weight: 600;
        letter-spacing: -1px;
    }

    .bottom {
        background-color: #fff;
        padding: 1.4rem 1rem;
        border-radius: 2rem;
        font-size: 1.4rem;
        height: 23rem;
        display: grid;
        grid-template-columns: repeat(auto-fit,minmax(4rem,1fr));
        grid-gap: .5rem;
    }
    .bottom button {
        background-color: #E6E7E8;
        padding-top: 12px;
        border-radius: 1.5rem;
        font-size: 1.65rem;
        display: flex;
        justify-content: center;
        align-content: center;
        color: #808285;
    }
    .bottom button:nth-child(1) {
        background-color: #FA386B;
        color: white;
        padding-top: 10px;
    }
    .bottom button:nth-child(2) {
        color: #231F20;
    }
    .bottom button:nth-child(3) {
        color: #231F20;
    }
    .bottom button:nth-child(4) {
        color: #231F20;
    }
    .bottom button:nth-child(8) {
        color: #231F20;
    }
    .bottom button:nth-child(12) {
        color: #231F20;
    }
    .bottom button:nth-child(16) {
        color: #231F20;
    }
    .bottom button:nth-child(20) {
        background-color: #30C117;
        color: white;
    }

    button:focus {
        outline: none;
    }
</style>