<template>
  <div class="stopwatch">      
    <ul class="stopwatch__content">
      <li class="stopwatch__content-item" v-for="{id, hours, minutes, seconds, isActivePlay, isActiveStop} in setArrayElements" v-bind:key="id">
          <div class="item__time" :class="{ 'active-text': isActivePlay }">
            <div class="item__time-content">
              <span class="item__time-hours" :class="{ active: hours }">{{ hours }}</span> 
              <span class="item__time-colon" :class="{ active: hours }">:</span> 
              <span class="item__time-minutes_content" v-if="minutes !== '00' && hours === 0 || hours">
                <span class="item__time-minutes active">{{ minutes }}</span> 
                <span class="item__time-colon active">:</span> 
              </span>
              <span class="item__time-seconds">{{ seconds }}</span>
            </div>
          </div>
          <div class="item__buttons">
            <div class="item__buttons-play" :class="{ active: isActiveStop}" @click="onStartStopwatch(id)"></div>
            <div class="item__buttons-stop" :class="{ active: isActivePlay}" @click="onStartStopwatch(id)"></div>
            <div class="item__buttons-reset-outside">
              <div class="item__buttons-reset" :class="{ 'active-background': isActivePlay }" @click="onResetStopwatch(id)"></div>
            </div>
          </div>
      </li>
      <button class="stopwatch__button" @click="addStopwatch"><span></span></button> 
    </ul>         
  </div>
</template>

<script>
  export default {
    data (){
      return {
        setArrayElements: [
          { 
            id: 1,
            seconds: '00',
            minutes: '00',
            hours: 0,         
            isActiveStop: true,
            isActivePlay: false,     
          }
        ],
        start: false,
        id: 0,
        countId: 1,
        timer: null,
      }
    },
    methods: {
      addStopwatch(){
        const newItem = {
          id: Math.random() + new Date(),
          seconds: '00',
          minutes: '00',
          hours: 0,          
          isActiveStop: true,
          isActivePlay: false,
        };

        this.setArrayElements = [...this.setArrayElements, newItem];
      },
      onStartStopwatch (id, value){      
        this.id = id;             
        this.start = value ?? !this.start;
        if(this.start && this.id === id){    
          this.countId = id;
          this.timer = setInterval(() => {
              const idx = this.setArrayElements.findIndex((el) => el.id === this.id);
              
              const oldItem = this.setArrayElements[idx];
              let newItem = {
                ...oldItem,              
                seconds:
                  oldItem.seconds < '09'
                    ? (parseInt(oldItem.seconds, 10) + 101).toString().substr(1)
                    : +oldItem.seconds + 1,
                isActiveStop: false,
                isActivePlay: true,
              };
              
              if (oldItem.minutes === 59 && oldItem.seconds === 59) {
                newItem = { ...oldItem, hours: oldItem.hours + 1, minutes: oldItem.minutes = '00', seconds: (oldItem.seconds = '00') };
              }

              if (oldItem.seconds === 59) {
                newItem = { ...oldItem, 
                  minutes: oldItem.minutes < '09'
                  ? (parseInt(oldItem.minutes, 10) + 101).toString().substr(1)
                  : +oldItem.minutes + 1,
                  seconds: (oldItem.seconds = '00'),
                };
              }             

              this.setArrayElements = [...this.setArrayElements.slice(0, idx), newItem, ...this.setArrayElements.slice(idx + 1)];
          }, 1000); 
        } else if(this.countId !== id){   
          const idx = this.setArrayElements.findIndex((el) => el.id === this.countId);
          const oldItem = this.setArrayElements[idx];
          let newItem = {
            ...oldItem,
            isActiveStop: true,
            isActivePlay: false,
          };
          this.setArrayElements = [...this.setArrayElements.slice(0, idx), newItem, ...this.setArrayElements.slice(idx + 1)];
          
          clearInterval(this.timer);
          
          this.id = id;
          this.start = value ?? !this.start;
          if (this.start && this.id === id) {
            this.countId = id;
            this.timer = setInterval(() => {
              const idx = this.setArrayElements.findIndex((el) => el.id === this.id);

              const oldItem = this.setArrayElements[idx];
              let newItem = {
                ...oldItem,
                seconds:
                  oldItem.seconds < '09'
                    ? (parseInt(oldItem.seconds, 10) + 101).toString().substr(1)
                    : +oldItem.seconds + 1,
                isActiveStop: false,
                isActivePlay: true,
              };

              if (oldItem.minutes === 59 && oldItem.seconds === 59) {
                newItem = { ...oldItem, hours: oldItem.minutes + 1, minutes: oldItem.minutes = '00', seconds: (oldItem.seconds = '00') };
              }

              if (oldItem.seconds === 59) {
                newItem = {
                  ...oldItem,
                  minutes: oldItem.minutes < '09'
                    ? (parseInt(oldItem.minutes, 10) + 101).toString().substr(1)
                    : +oldItem.minutes + 1,
                  seconds: (oldItem.seconds = '00'),
                };
              }

              this.setArrayElements = [...this.setArrayElements.slice(0, idx), newItem, ...this.setArrayElements.slice(idx + 1)];
            }, 1000);
          }
        } else {
          this.start = false;   
          const idx = this.setArrayElements.findIndex((el) => el.id === this.id);
          const oldItem = this.setArrayElements[idx];
          let newItem = {
            ...oldItem,        
            isActiveStop: true,
            isActivePlay: false,
          };
          this.setArrayElements = [...this.setArrayElements.slice(0, idx), newItem, ...this.setArrayElements.slice(idx + 1)];
          
          clearInterval(this.timer);
        }
      },
      onResetStopwatch (id){
        this.isActivePlay = true;
        this.isActiveStop = false;
        
        this.onStartStopwatch(id, false);

        const idx = this.setArrayElements.findIndex((el) => el.id === id);

        const oldItem = this.setArrayElements[idx];
        let newItem = {
          ...oldItem,
          seconds: '00',
          minutes: '00',
          hours: 0,  
          isActiveStop: true,
          isActivePlay: false,  
        };

        this.setArrayElements = [...this.setArrayElements.slice(0, idx), newItem, ...this.setArrayElements.slice(idx + 1)];
      }     
    },  
  }
</script>

<style>
body{
  background-color: #353638;
}

.stopwatch{
  max-width: 1000px;

  font-family: Gotham Pro;
  font-size: 22px;
  font-weight: 400;
  line-height: 21px;
  letter-spacing: 0em;
  text-align: center;

  margin: 100px auto;
}

.stopwatch__content{
  display: grid;
  grid-template-columns: min-content min-content min-content;
  justify-content: center; 

  padding: 0 50px;
}
@media (max-width: 1024px) {
  .stopwatch__content{
    grid-template-columns: min-content min-content;
  }
}
@media (max-width: 768px) {
  .stopwatch__content{
    grid-template-columns: min-content;
  }
}

.stopwatch__content-item{
  width: 225px;
  height: 120px;

  list-style-type: none;

  margin: 45px 25px 0 25px;

  background-color: #696969;
}
@media (max-width: 768px) {
  .stopwatch__content-item{
    margin: 45px 0 0 0;
  }
}

.item__time{
  width: 225px;
  height: 60px;

  display: flex;
  justify-content: center;
  align-items: center;  

  position: relative;

  border-bottom: 1px solid #9E9E9E;  

  color: #9E9E9E;  
}

.item__time-content{
  display: flex;
  justify-content: center;
}

.item__time-minutes_content{
  display: flex;
}

.item__time-hours,
.item__time-minutes,
.item__time-colon{
  display: none;
}

.item__time-colon{
  margin: 0 3px;
}

.item__buttons{
  display: flex;

  position: relative;

  margin: 20px 0 0 0;
}

.item__buttons-play{
  width: 0;
  height: 0;

  display: none;
  position: absolute;
  
  margin: 0 0 0 70px;

  border: solid;
  border-color: transparent transparent transparent #9E9E9E;
  border-width: 11px 11px 11px 18px;

  cursor: pointer;
}

.item__buttons-stop{
  width: 6px;
  height: 20px;
  
  display: none;
  position: absolute;
  
  margin: 0 0 0 70px;

  border-right: 3px solid #FFFFFF;
  border-left: 3px solid #FFFFFF;

  cursor: pointer;
}

.item__buttons-reset{
  width: 20px;
  height: 20px;

  position: absolute;
  right: 70px;

  margin: 0 0 0 45px;

  background-color: #9E9E9E;

  cursor: pointer;
}

.active{
  display: block;  

  position: static;
}

.active-text{
  color: #FFFFFF;

  border-bottom: 2px solid #FFFFFF;
}

.active-background{
  background-color: #FFFFFF;
}

.stopwatch__button{
  width: 225px;
  height: 120px;

  position: relative;

  border: none;
  outline: none;
  margin: 45px 25px 0 25px;
  
  background-color: #696969;

  cursor: pointer;
}
@media (max-width: 768px) {
  .stopwatch__button{
    margin: 45px 0 0 0;
  }
}

.stopwatch__button span::before,
.stopwatch__button span::after{
  content: '';

  width: 3px;
  height: 20px;

  display: block;
  
  position: absolute;
  top: 50%;
  left: 50%;

  background-color: #9E9E9E;

  transform: translate(-50%, -50%);
}

.stopwatch__button span::after{
  top: 42%;
  left: 49.6%;

  transform: rotate(90deg);
}

.stopwatch__button:hover span::before,
.stopwatch__button:hover span::after{
   background-color: #FFFFFF;  
}
</style>
