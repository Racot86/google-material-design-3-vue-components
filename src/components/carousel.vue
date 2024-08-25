<script setup>

import {onMounted, reactive} from "vue";

let props = defineProps({
  imageData:[]
});
let vars = reactive({
  imagesSpecs:[],
  smallItem:0,
  mediumItem:0,
  largeItem:0,
  gap:8
})

onMounted(()=>{

  let carousel = document.querySelector('.carousel');
  let carouselWidth = carousel.getBoundingClientRect().width;
  vars.mediumItem = Math.round( carouselWidth/100 * 40);
  vars.largeItem =  Math.round( carouselWidth/100 * 50);
  vars.smallItem =  (carousel.scrollWidth) - vars.mediumItem - vars.largeItem - 16;
  let cards = document.querySelectorAll('.carousel-item');
  let i = 0;
  cards.forEach(card => {
    if(i===0){
      card.style.minWidth = vars.largeItem +'px';
    }
    else if(i===1){
      card.style.minWidth = vars.mediumItem +'px';
    }else{
      card.style.minWidth = vars.smallItem - 8 +'px';
    }
    //card.innerHTML = card.getBoundingClientRect().left.toString();
    i += 1;
  })

})
let lastScroll = 0;
const scroll = (e) => {
  e.preventDefault();
  let carousel = document.querySelector('.carousel');
  let cards = document.querySelectorAll('.carousel-item');
  let zeroPsn = carousel.getBoundingClientRect().left;
  let delta = e.deltaX;
  console.log(e.deltaX);
  //carousel.scrollBy(e.deltaX,0);
  let i =0;
  cards.forEach(card => {
    let cardLeft = card.getBoundingClientRect().left;
    let cardWidth = card.getBoundingClientRect().width;
    //card.innerHTML = cardLeft.toString();


      if(delta>0) {

        if (cardWidth - delta <= 1) {
          card.classList.add('visually-hidden');
        } else {
          card.classList.remove('visually-hidden');
        }

        if (cardLeft <= zeroPsn && cardWidth - delta <= vars.largeItem) {
          //card.style.background = 'blue';
          card.style.minWidth = card.getBoundingClientRect().width - delta + 'px';
          if(i===cards.length-3 && cardWidth<=vars.smallItem) {
            card.style.minWidth = vars.smallItem + 'px';
          }
        } else {
          if (cardLeft <= zeroPsn + vars.largeItem + vars.gap && cardWidth + delta <= vars.largeItem) {
            //card.style.background = 'red';
            card.style.minWidth = card.getBoundingClientRect().width + delta + 'px';
          } else {
            if (cardLeft <= zeroPsn + vars.largeItem + vars.gap + vars.mediumItem + vars.gap - 1 && cardWidth <= vars.mediumItem) {
              //card.style.background = 'yellow';
              card.style.minWidth = card.getBoundingClientRect().width + delta + 'px';
            }
          }
        }



      }else if(delta<0) {

        if(cardLeft <= zeroPsn && cardWidth + delta <= vars.largeItem && !card.classList.contains('visually-hidden')) {
          if(cardWidth - delta <= vars.largeItem) {
            card.style.minWidth = cardWidth - delta + 'px';
          }else {
            card.style.minWidth = vars.largeItem + 'px';
          }

        }else {

        }
        if(cardLeft> zeroPsn){
          if(i > 0 && cards[i-1].getBoundingClientRect().left===zeroPsn && cards[i-1].getBoundingClientRect().width>=vars.smallItem ){
            if (cardWidth+delta<=vars.mediumItem) {
              card.style.minWidth = vars.mediumItem + 'px';
            }else{
              card.style.minWidth = cardWidth + delta + 'px';
            }
          }
        }

        if(cardLeft > zeroPsn && cardWidth>vars.smallItem){
          if(i > 1 && cards[i-2].getBoundingClientRect().left===zeroPsn && cards[i-1].getBoundingClientRect().width===vars.mediumItem) {
            if (cardWidth+delta<vars.smallItem) {
              card.style.minWidth = vars.smallItem + 'px';
            }else{
              card.style.minWidth = cardWidth + delta + 'px';
            }
          }
        }

        if(card.classList.contains('visually-hidden') && cards[i+1].getBoundingClientRect().width===vars.largeItem && cards[i+1].getBoundingClientRect().width>=vars.largeItem) {
          card.classList.remove('visually-hidden');
        }

      }






i+=1;
  })
  delta = e.deltaX;


}






const scrollEnd = ()=> {
}





</script>

<template>
<div  class="carousel" @wheel="scroll" @scrollend="scrollEnd">
  <div class="carousel-item" v-for='image in props.imageData' >
    <img class="image" :src="image.original"  alt="">
  </div>
  </div>
</template>

<style scoped>



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
  width: 120%;
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
}
.shrink{
  transition: 0.2s ease-in-out;
}

.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  margin: -1px;
  border: 0;
  padding: 0;
  white-space: nowrap;
  clip-path: inset(100%);
  clip: rect(0 0 0 0);
  overflow: hidden;
}
</style>

