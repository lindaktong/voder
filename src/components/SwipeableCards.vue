<template>
  <section class="container">
    <div class="fixed header">
      <div id="refreshBtn" class="btn btn--refresher" @click="emitAndNext('reset');">
          <i class="material-icons">refresh</i>
      </div>
      <span class="title">
        <img src="../assets/images/Group 24.png" width="200px" height=auto>
      </span>
      <div id = "informationBtn" class="btn btn--information" @click="emitAndNext('giveInformation')">
          <i class="material-icons">tune</i>
      </div>
    </div>
    <div
      v-if="current"
      class="fixed fixed--center"
      style="z-index: 3"
      :class="{ 'transition': isVisible }">
      <Vue2InteractDraggable 
        v-if="isVisible"
        :interact-out-of-sight-x-coordinate="500"
        :interact-max-rotation="2"
        :interact-x-threshold="200"
        :interact-y-threshold="200"
        :interact-event-bus-events="interactEventBus"
        interact-block-drag-down
        @draggedRight="emitAndNext('match')"
        @draggedLeft="emitAndNext('reject')"
        @draggedUp="emitAndNext('skip')"
        class="rounded-borders card card--one">
        <div style="height: 100%">
          <img
            :src="require(`../assets/images/${current.src}`)"
            class="rounded-borders"/>
          <div class="text">
            <h2><span>{{current.category}}</span></h2>
          </div>
        </div>
      </Vue2InteractDraggable>
    </div>
    <div
      v-if="next"
      class="rounded-borders card card--two fixed fixed--center"
      style="z-index: 2">
      <div style="height: 100%">
        <img
          :src="require(`../assets/images/${next.src}`)"
          class="rounded-borders"/>
        <div class="text">
            <h2><span>{{next.category}}</span></h2>
          </div>
      </div>
    </div>
    <div
      v-if="index + 2 < cards.length"
      class="rounded-borders card card--three fixed fixed--center"
      style="z-index: 1">
      <div style="height: 100%">
      </div>
    </div>
    <div class="footer fixed">
      <div class="btn btn--decline" @click="reject">
          <i class="material-icons">close</i>
      </div>
      <div class="btn btn--skip" @click="skip">
          <i class="material-icons">call_missed</i>
      </div>
      <div class="btn btn--like" @click="match">
          <i class="material-icons">favorite</i>
      </div>
    </div>
  </section>
</template>

<script>
import swal from 'sweetalert';
import { Vue2InteractDraggable, InteractEventBus } from 'vue2-interact'

const EVENTS = {
  MATCH: 'match',
  SKIP: 'skip',
  REJECT: 'reject'
}

let cardList = [
        { src: 'abortion-t1.jpg', category: 'Abortion'},
        { src: 'abortion-t2.jpg', category: 'Abortion'},
        { src: 'abortion-t3.jpg', category: 'Abortion'},
        { src: 'abortion-b1.jpg', category: 'Abortion'},
        { src: 'abortion-b2.jpg', category: 'Abortion'},
        { src: 'abortion-b3.jpg', category: 'Abortion'},
        
        { src: 'climate-t1.jpg', category: 'Climate'},
        { src: 'climate-t2.jpg', category: 'Climate'},
        { src: 'climate-t3.jpg', category: 'Climate'},
        { src: 'climate-b1.jpg', category: 'Climate'},
        { src: 'climate-b2.jpg', category: 'Climate'},
        { src: 'climate-b3.jpg', category: 'Climate'},
        
        { src: 'economy-t1.jpg', category: 'Economy'},
        { src: 'economy-t2.jpg', category: 'Economy'},
        { src: 'economy-t3.jpg', category: 'Economy'},
        { src: 'economy-b1.jpg', category: 'Economy'},
        { src: 'economy-b2.jpg', category: 'Economy'},
        { src: 'economy-b3.jpg', category: 'Economy'},
        
        { src: 'edu-t1.jpg', category: 'Education'},
        { src: 'edu-t2.jpg', category: 'Education'},
        { src: 'edu-t3.jpg', category: 'Education'},
        { src: 'edu-b1.jpg', category: 'Education'},
        { src: 'edu-b2.jpg', category: 'Education'},
        { src: 'edu-b3.jpg', category: 'Education'},
        
        { src: 'gun-t1.jpg', category: 'Gun'},
        { src: 'gun-t2.jpg', category: 'Gun'},
        { src: 'gun-t3.jpg', category: 'Gun'},
        { src: 'gun-b1.jpg', category: 'Gun'},
        { src: 'gun-b2.jpg', category: 'Gun'},
        { src: 'gun-b3.jpg', category: 'Gun'},

        { src: 'health-t1.jpg', category: 'Health'},
        { src: 'health-t2.jpg', category: 'Health'},
        { src: 'health-t3.jpg', category: 'Health'},
        { src: 'health-b1.jpg', category: 'Health'},
        { src: 'health-b2.jpg', category: 'Health'},
        { src: 'health-b3.jpg', category: 'Health'},

        { src: 'immigrant-t1.jpg', category: 'Immigrant'},
        { src: 'immigrant-t2.jpg', category: 'Immigrant'},
        { src: 'immigrant-t3.jpg', category: 'Immigrant'},
        { src: 'immigrant-b1.jpg', category: 'Immigrant'},
        { src: 'immigrant-b2.jpg', category: 'Immigrant'},
        { src: 'immigrant-b3.jpg', category: 'Immigrant'},
              
        { src: 'lgbtq-t1.jpg', category: 'LGBTQ'},
        { src: 'lgbtq-t2.jpg', category: 'LGBTQ'},
        { src: 'lgbtq-t3.jpg', category: 'LGBTQ'},
        { src: 'lgbtq-b1.jpg', category: 'LGBTQ'},
        { src: 'lgbtq-b2.jpg', category: 'LGBTQ'},
        { src: 'lgbtq-b3.jpg', category: 'LGBTQ'},

        { src: 'racial-t1.jpg', category: 'Racial'},
        { src: 'racial-t2.jpg', category: 'Racial'},
        { src: 'racial-t3.jpg', category: 'Racial'},
        { src: 'racial-b1.jpg', category: 'Racial'},
        { src: 'racial-b2.jpg', category: 'Racial'},
        { src: 'racial-b3.jpg', category: 'Racial'},

        { src: 'security-t1.jpg', category: 'Security'},
        { src: 'security-t2.jpg', category: 'Security'},
        { src: 'security-t3.jpg', category: 'Security'},
        { src: 'security-b1.jpg', category: 'Security'},
        { src: 'security-b2.jpg', category: 'Security'},
        { src: 'security-b3.jpg', category: 'Security'}       
      ];   

    function shuffle(a) {
      for (let i = a.length - 1; i > 0; i--) {
          const j = Math.floor(Math.random() * (i + 1));
          [a[i], a[j]] = [a[j], a[i]];
      }
      return a;
    }
    
    let actualCardList = [];
    let firstSixCardList = [];

    for(let i = 0; i < cardList.length; i++)
    {
      if(i % 6 === 0 && i !== 0)
     {
          actualCardList = actualCardList.concat(shuffle(firstSixCardList));
          firstSixCardList = [cardList[i]];
      }
      else
      {
        firstSixCardList[i % 6] = cardList[i];
      }
    }
    actualCardList = actualCardList.concat(shuffle(firstSixCardList));

    let finalCardList = [];
    for(let i = 0; i < 6; i++)
    {
      finalCardList.push(actualCardList[i], 
              actualCardList[i+6],
              actualCardList[i+12],
              actualCardList[i+18],
              actualCardList[i+24],
              actualCardList[i+30],
              actualCardList[i+36],
              actualCardList[i+42],
              actualCardList[i+48],
              actualCardList[i+54],
              )
    }
    

  let matchedList = [0, 0];
  let rejectedList = [0, 0];
  let skippedList = [0];

export default {
  name: 'SwipeableCards',
  components: { Vue2InteractDraggable },
  data() {
    return {
      isVisible: true,
      index: 0,
      interactEventBus: {
        draggedRight: EVENTS.MATCH,
        draggedLeft: EVENTS.REJECT,
        draggedUp: EVENTS.SKIP
      },
      cards: finalCardList
    }
  },
  computed: {
    current() {
      return this.cards[this.index]
    },
    next() {
      return this.cards[this.index + 1]
    },
  },
  methods: {
    match() {    
      InteractEventBus.$emit(EVENTS.MATCH)
    },
    reject() {
      InteractEventBus.$emit(EVENTS.REJECT)
    },
    skip() {
      InteractEventBus.$emit(EVENTS.SKIP)
    },
    emitAndNext(event) {
      
      this.$emit(event, this.index)
      setTimeout(() => this.isVisible = false, 200)
      setTimeout(() => {
        this.index++
        this.isVisible = true
      }, 200)

      if (event === 'match') {

        let png = this.cards[this.index].src;
        let president = png[png.indexOf('-')+1];

        if(president === 't')
        {
          matchedList[0] += 1;
        }
        else
        {
          matchedList[1] += 1;
        }

        let totalLength = rejectedList[0] + rejectedList[1] + matchedList[0] + matchedList[1] + skippedList[0];

        if(totalLength === 60)
        {
          swal("Thank you for finishing your Voder Test! Here are your results!" + "\n" + 
          "You have identified with " + matchedList[0]+  " of Trump's views and " + 
          matchedList[1]+ " of Biden's views. Also, you have rejected " + rejectedList[1]+ " of Trump's views" + 
          " and " + rejectedList[0]+" of Biden's views." + " There were " + skippedList[0] + " views from either candidate that you've decided to skip. Again"+
          ", thank you so much for participating! We hope that Voder helped you make a better informed decision during the 2020 election!");
        }
        else if(totalLength % 10 === 0)
        {
          swal("Hey! Congrats on finishing round " + (totalLength/10) + "! So far, you have identified with " + matchedList[0]+  " of Trump's views and " + 
          matchedList[1]+ " of Biden's views. Also, you have rejected " + rejectedList[1]+ " of Trump's views" + 
          " and " + rejectedList[0]+" of Biden's views." + " There were " + skippedList[0] + " views from either candidate that you've decided to skip.")
        }
      }

      else if (event === 'reject') {
        let png = this.cards[this.index].src;
        let president = png[png.indexOf('-')+1];

        if(president === 't')
        {
          rejectedList[1] += 1;
        }
        else
        {
          rejectedList[0] += 1;
        }

        let totalLength = rejectedList[0] + rejectedList[1] + matchedList[0] + matchedList[1] + skippedList[0];
        if(totalLength === 60)
        {
          swal("Thank you for finishing your Voder Test! Here are your results!" + "\n" + 
          "You have identified with " + matchedList[0]+  " of Trump's views and " + 
          matchedList[1]+ " of Biden's views. Also, you have rejected " + rejectedList[1]+ " of Trump's views" + 
          " and " + rejectedList[0]+" of Biden's views." + " There were " + skippedList[0] + " views from either candidate that you've decided to skip. Again"+
          ", thank you so much for participating! We hope that Voder helped you make a better informed decision during the 2020 election!");
        }
        else if(totalLength% 10 === 0)
        {
          swal("Hey! Congrats on finishing round " + (totalLength/10) + "! So far, you have identified with " + matchedList[0]+  " of Trump's views and " + 
          matchedList[1]+ " of Biden's views. Also, you have rejected " + rejectedList[1]+ " of Trump's views" + 
          " and " + rejectedList[0]+" of Biden's views." + " There were " + skippedList[0] + " views from either candidate that you've decided to skip.")
        }
      }

      else if (event === 'skip')
      {
        skippedList[0] += 1;
        let totalLength = rejectedList[0] + rejectedList[1] + matchedList[0] + matchedList[1] + skippedList[0];
        if(totalLength === 60)
        {
          swal("Thank you for finishing your Voder Test! Here are your results!" + "\n" + 
          "You have identified with " + matchedList[0]+  " of Trump's views and " + 
          matchedList[1]+ " of Biden's views. Also, you have rejected " + rejectedList[1]+ " of Trump's views" + 
          " and " + rejectedList[0]+" of Biden's views." + " There were " + skippedList[0] + " views from either candidate that you've decided to skip. Again"+
          ", thank you so much for participating! We hope that Voder helped you make a better informed decision during the 2020 election!");
        }
        else if(totalLength % 10 === 0)
        {
          swal("Hey! Congrats on finishing round " + (totalLength/10) + "! So far, you have identified with " + matchedList[0]+  " of Trump's views and " + 
          matchedList[1]+ " of Biden's views. Also, you have rejected " + rejectedList[1]+ " of Trump's views" + 
          " and " + rejectedList[0]+" of Biden's views." + " There were " + skippedList[0] + " views from either candidate that you've decided to skip.")
 
        }
      }

    else if(event === 'reset')
      {
        this.index = -1;
        matchedList = [0, 0];
        rejectedList = [0, 0];
        skippedList = [0];
      }
    else if (event === 'giveInformation')
    {
      this.index -= 1;
      swal("Hello! We are the creators of Voder! With this project, we aim at providing voters with the knowledge they need to make an informed decision during the 2020 election." +
      " Identity politics create major bubbles around America, oftentimes furthering the cyclical nature of bias. Moreover, both misinformed and uninformed citizens often vote against"+ 
      " their best interests. We at Voder plan to take action on this pressing issue by giving votors as much information as they need, one swipe at a time!")
    }
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  background: -webkit-linear-gradient(to top, #BC4B51, #084887);
  background: linear-gradient(to top, #BC4B51, #084887);
  width: 100%;
  height: 100vh;
}

.header {
  width: 100%;
  height: 60vh;
  z-index: 0;
  top: 0;
  left: 0;
  color: white;
  text-align: center;
  font-style: italic;
  font-family: 'Engagement', cursive; 
  clip-path: polygon(0 0%, 100% 0%, 100% 76%, 0 89%);
  display: flex;
  justify-content: space-between;
  span {
    display: block;
    font-size: 4rem;
    padding-top: 2rem;
    text-shadow: 1px 1px 1px red;
  }
  i {
    padding: 24px;
  }
}

.footer {
  width: 77vw;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  padding-bottom: 30px;
  justify-content: space-around;
  align-items: center;
}

#refreshBtn {
  position: relative;
  top: 25px;
  left: 25px;

}

#informationBtn {
  position: relative;
  top: 25px;
  right: 25px;

}

.btn {
  position: relative;
  width: 50px;
  height: 50px;
  padding: .2rem;
  border-radius: 50%;
  background-color: white;
  box-shadow: 0 6px 6px -3px rgba(0,0,0,0.02), 0 10px 14px 1px rgba(0,0,0,0.02), 0 4px 18px 3px rgba(0,0,0,0.02);
  cursor: pointer;
  transition: all .3s ease;
  user-select: none;
  -webkit-tap-highlight-color:transparent;
  &:active {
    transform: translateY(4px);
  }
  i {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    &::before {
      content: '';
    }
  }

  
  &--like {
    background-color: red;
    padding: .5rem;
    color: white;
    box-shadow: 0 10px 13px -6px rgba(0,0,0,.2), 0 20px 31px 3px rgba(0,0,0,.14), 0 8px 38px 7px rgba(0,0,0,.12);
    i {
      font-size: 32px;
    }
  
  }
  &--refresher {
    width: 35px;
    height: 35px;
  }
  &--information {
    width: 35px;
    height: 35px;
  }
  &--decline {
    color: red;
  }
  &--skip {
    color: green;
  }
  &--refresher {
    color: blue
  }
  &--information{
    color: blue
  }
}

.flex {
  display: flex;
  &--center {
    align-items: center;
    justify-content: center;
  }
}

.fixed {
  position: fixed;
  &--center {
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }
}
.rounded-borders {
  border-radius: 12px;
}
.card {
  width: 39w;
  height: 60vh;
  color: white;
  img {
    object-fit: cover;
    display: block;
    width: 100%;
    height: 100%;
  }
  &--one {
    box-shadow: 0 1px 3px rgba(0,0,0,.2), 0 1px 1px rgba(0,0,0,.14), 0 2px 1px -1px rgba(0,0,0,.12);
  }
  &--two {
    transform: translate(-48%, -48%);
    box-shadow: 0 6px 6px -3px rgba(0,0,0,.2), 0 10px 14px 1px rgba(0,0,0,.14), 0 4px 18px 3px rgba(0,0,0,.12);
  }
  &--three {
    background: rgba(black, .8);
    transform: translate(-46%, -46%);
    box-shadow: 0 10px 13px -6px rgba(0,0,0,.2), 0 20px 31px 3px rgba(0,0,0,.14), 0 8px 38px 7px rgba(0,0,0,.12);
  }
  .text {
    position: absolute;
    bottom: 0;
    width: 100%;
    background:black;
    background:rgba(55,80,92,1);
    border-bottom-right-radius: 12px;
    border-bottom-left-radius: 12px;
    text-indent: 20px;
    span {
      font-weight: normal;
    }
  }
}

.transition {
  animation: appear 200ms ease-in;
}

@keyframes appear {
  from {
    transform: translate(-48%, -48%);
  }
  to {
    transform: translate(-50%, -50%);
  }
}

.title {
        display: block;
        margin-left: 24.22%;
        margin-right: auto;
        width: 50%;
      }

</style>