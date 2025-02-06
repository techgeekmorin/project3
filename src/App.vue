<script setup>
import {ref, onMounted, watch} from 'vue'

const myArray = ref([])
const name = ref('')
const input_bill = ref('')
const input_tip = ref('')
const input_people = ref('')

const calcTip = () =>{
  if(input_bill.value.trim() === '' || input_tip.value.trim() == '' || input_people.value.trim() == ''){
    alert("Please fill all values!")
    return
  }
  
  myArray.value.push({
    billTotal: (parseFloat(input_bill.value)).toFixed(2),
    tipPercentage: (parseFloat(input_tip.value)).toFixed(2),
    numPeople: parseInt(input_people.value),
    tipPerPerson: (parseFloat((input_bill.value * (input_tip.value/100))/ input_people.value)).toFixed(2),
    paid: false,
  })

  input_bill.value = ''
  input_tip.value = ''
  input_people.value = ''

}

const removeTip = (x) =>{
  myArray.value = myArray.value.filter(Element => Element !== x)
}

onMounted( () => {
  name.value = localStorage.getItem('name')  || ''
  myArray.value = JSON.parse(localStorage.getItem('myArray')) || []
}
)

watch(name, (newVal) =>{
  localStorage.setItem('name', newVal)
})

watch(myArray, (newVal) =>{
  localStorage.setItem('myArray', JSON.stringify(newVal))
}, {deep: true})
</script>

<template>
  <main class="app">

    <section class="greeting">
      <h1 class="title">
        Hello, <input type="text" placeholder="Enter Name" v-model="name">
      </h1>
    </section>

    <section class="create-tiplist">
      <h2>Tip Calculator</h2>
      <form @submit.prevent = "calcTip">

        <h3>What is the bill total?</h3>

        <input type="text" placeholder="Bill Cost" v-model="input_bill"/>

        <h3>What is the percentage for tip?</h3>

        <input type="text" placeholder="Tip Percentage" v-model="input_tip"/>

        <h3>How many people?</h3>

        <input type="text" placeholder="# of People" v-model="input_people"/>

        <input type="submit" value="Calculate Tip">
      </form>
    </section>

    <section class="tip-list">
      <div class="list">
        <div v-for="x in myArray" :class="`tip-item ${x.paid ? 'paid' : 'not-paid'}`" :key="x">
          <label>
            <input type="checkbox" v-model="x.paid"/>
          </label>
          
          <div class="tip-content">
            <label>
              Bill Total: $<input type="text" v-model="x.billTotal"/>
            </label>
            <label>
             <span>Tip Percentage: <input type="text" v-model="x.tipPercentage" style="width: 45px;"/>%</span>
            </label>
            <label>
              Number of People: <input type="text" v-model="x.numPeople"/>
            </label>
            <label>
              Tip Per Person: $<input type="text" v-model="x.tipPerPerson"/>
            </label>
          </div>

          <div class="actions">
            <button class="delete" @click="removeTip(x)">Delete</button>
          </div>

        </div>
      </div>
    </section>

  </main>
</template>