<script setup>

import {nextTick, onMounted, onUpdated, reactive} from "vue";

let props = defineProps({
  imageData:[]
});
let vars = reactive({
  imagesSpecs:[],
  smallItem:0,
  mediumItem:0,
  largeItem:0,
  gap:8,
  cardsInView: 3
});

const changeCardWidth = (el, newWidth) => {
  el.classList.remove("large", 'medium', 'small');
  el.classList.add(newWidth);
}

onMounted(()=> {
  let carouselWidth = document.querySelector(".carousel").getBoundingClientRect().width;
  let cards = document.querySelectorAll(".carousel-item");
  vars.largeItem = Math.round((carouselWidth - vars.gap * ( vars.cardsInView-1) )/ 100 * 50);
  vars.mediumItem = Math.round((carouselWidth - vars.gap * ( vars.cardsInView-1) )/ 100 * 35);
  vars.smallItem = carouselWidth - vars.largeItem - vars.mediumItem - vars.gap * ( vars.cardsInView-1);
  let i = 0;
  cards.forEach(card => {
    if (i === 0) {
      card.style.minWidth = vars.largeItem + "px";
    } else if (i === 1) {
      card.style.minWidth = vars.mediumItem + "px";
    } else {
      card.style.minWidth = vars.smallItem + "px";
    }
    i += 1;
  })
})

let delta = 0;
let isMoving = false;
const scroll = (move) => {
  let carousel = document.querySelector('.carousel');
  let cards = document.querySelectorAll('.carousel-item');
  let zeroPsn = carousel.getBoundingClientRect().left;

      delta = move;


//console.log(isMoving + " " + delta);
  let i = 0;
  cards.forEach(card => {
    let cardWidth = card.getBoundingClientRect().width;
    let cardLeft = card.getBoundingClientRect().left;

    if (delta > 0) {
      if(i<cards.length-3) {
        if (cardLeft === zeroPsn && cardWidth === vars.largeItem) {
          card.style.minWidth = vars.smallItem + "px";
          cards[i + 1].style.minWidth = vars.largeItem + "px";
          cards[i + 2].style.minWidth = vars.mediumItem + "px";
          return;
        } else {
          if (cardLeft === zeroPsn && cardWidth === vars.smallItem) {
            card.style.minWidth = 0 + "px";
            return;
          }
        }
      }

    } else if (delta < 0) {

      if (card.classList.contains('visually-hidden')) {

        if (cards[i+1].getBoundingClientRect().width === vars.largeItem){
          card.classList.remove('visually-hidden');
          card.style.minWidth = vars.smallItem + "px";
        }
      }
      if(cardLeft === zeroPsn && cardWidth === vars.smallItem){
        card.style.minWidth = vars.largeItem + "px";
        cards[i+1].style.minWidth = vars.mediumItem + "px";
        cards[i+2].style.minWidth = vars.smallItem + "px";
      }

    }
    i+=1;

  })
}





function animEnd(){
  isMoving = false;
  let cards = document.querySelectorAll('.carousel-item');
  let carousel = document.querySelector('.carousel');
  cards.forEach(card => {
    if (card.getBoundingClientRect().width <= 0){
      card.classList.add("visually-hidden");
    }
  })
}

function deltaZero(){
  isMoving = true;
}
function leftClick(){
  if (!isMoving) {

    scroll(1);
  }
}
function rightClick(){
  if (!isMoving) {

    scroll(-1);
  }
}

</script>

<template>
<div  class="carousel" @transitionend="animEnd" @transitionrun="deltaZero" >
  <div class="arrow left" @click="leftClick">
      <img class="ico-arrow" src="../assets/components/carousel/left-arrow.svg" alt="arrow">
  </div>
  <div class="carousel-item" v-for='image in props.imageData' >
    <img class="image" :src="image.original"  alt="">
  </div>
  <div class="arrow right" @click="rightClick">
    <img class="ico-arrow mirrored" src="../assets/components/carousel/left-arrow.svg" alt="arrow">
  </div>
  </div>
</template>

<style scoped>

.arrow{
  position: absolute;
  height: 20%;
  width: 7%;
  background-color: white;
  opacity: 0;
  transition: 0.3s;
  z-index: 10;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 30px;
  margin: 8px;
}
.left{
  left: 0;
}
.right{
  right: 0;
}

.carousel:hover .arrow{
  opacity: 0.5;
  transition: 0.3s;
}


.carousel{
  display: flex;
  align-items: center;
  gap: 8px;
  overflow: hidden;
  height: 350px;
  width: 100%;

  position: relative;
  justify-content: left;
}
.image{
  position: absolute;
  object-fit: cover;
  height: 120%;
  transform-origin: center;

}


.carousel-item{
  background: cornflowerblue;
  border-radius: 30px;
  height: 100%;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  transition: 0.5s;
}
.shrink{
  transition: 0.2s ease-in-out;
}

.visually-hidden {
  position: absolute;
  width: 0px;
  margin: -1px;
  border: 0;
  padding: 0;
  white-space: nowrap;
  clip-path: inset(100%);
  clip: rect(0 0 0 0);
  overflow: hidden;
}

.ico-arrow{
  width: 50%;
  height: 50%;
  object-fit: contain;
}
.mirrored{
  transform: scaleX(-1);
}
.large{
  max-width: 50%;
}
.medium{
    max-width: 35%;
}
.small{
  max-width: 15%;
}
</style>

