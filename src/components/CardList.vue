<template>
  <div class="box">
    <div class="mini-box"  v-for="(item, index) in cards" :key="index">
    <h3>{{ item.title }}</h3>
    <p>{{ item.description }}</p>
    <img :src="item.image" alt="Card Image" />
    <button style="border:none;width:100px" @click="open(index+1)">edit</button>
  </div>
  <div :style="{ display: visible }" class="popup">
    <input type="text" :placeholder="currCardInfo?.title" v-model="title">
    <input type="text" :placeholder="currCardInfo?.description" v-model="descr">
    <input type="text" :placeholder="currCardInfo?.image" v-model="img">
    <button @click="close()">save</button>

  </div>
  </div>
</template>

<script setup lang="ts">
import axios from 'axios'
import { ref , onMounted } from 'vue'

interface Card {
  id: number;
  title: string;
  description: string;
  image: string;
  date?: string;
}

const cards = ref<Card[]>([]);
const currCard = ref()
const visible = ref('none');
const currCardInfo=ref()
const title = ref();
const descr = ref();
const img = ref(); 

  // const obj: Card = {
  //     id: currCard.value,
  //     title: title.value,
  //     description: descr.value,
  //     image: img.value,
  //     date: currCardInfo.value?.date
  //   };



const open=(index:number)=>{
  visible.value = "block"
  currCard.value=index
  getOneCard(currCard.value)
}
const getOneCard=async(currCard:number)=>{
  const data=await axios(`http://localhost:3000/cards/${currCard}`)
  currCardInfo.value = data.data;
  console.log(currCardInfo);
}
const close=()=>{
  visible.value = "none"
  editCard(currCard.value)
  
}
  const editCard=async(currCard:number)=>{
      const obj: Card = {
      id: currCard,
      title: title.value,
      description: descr.value,
      image: img.value,
      date: currCardInfo.value?.date
    };
   try {
     const data=await axios.patch(`http://localhost:3000/cards/${currCard}`,obj)
   } catch (error) {
    
   }finally{
    getCards()
   }
  }

const getCards = async () => {
  try {
    const response = await axios.get<Card[]>('http://localhost:3000/cards');
    cards.value = response.data;
  } catch (error) {
    console.error('Error fetching cards:', error);
  }
}

onMounted(() => {
  getCards();
});

</script>

<style>
.popup{
  position: absolute;
  width: 30%;
  height: 30%;
  border: 1px solid gray;
  background-color: beige;
  margin-left:30% ;
}
.box{
  display: flex;
  flex-flow: wrap;
}
.mini-box{
  padding: 10px;
  border: 1px solid gray;
}

</style>
