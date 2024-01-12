<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Time Fighter</ion-title>
        <ion-buttons slot="end">
          <ion-button @click="showingAuthor = true">
            <ion-icon :icon="infoIcon" color="danger"></ion-icon>
          </ion-button>
        </ion-buttons>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-alert :is-open="showingAuthor" :header="'Time Fighter' + authorInfo.version"
        :sub-header="'Creat per ' + authorInfo.author"
        :message="'Podeu consultar el codi font al meu repositori de Github.'"
        :buttons="[{ text: 'Close', handler: () => { showingAuthor = false; } }, { text: 'Accedir al github', handler: () => { openRepo(); } }]"
        :onDidDismiss="closeAuthor"></ion-alert>
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Blank</ion-title>
        </ion-toolbar>
      </ion-header>
      <div class="header-app">
        <div class="header-score">
          Your score: {{ score }}
        </div>
        <div class="header-time">
          Time left: {{ timeLeft }}
        </div>
      </div>
      <div id="container">
        <template v-if="status == 0">
          <!-- Ionic button -->
          <ion-button @click="startGame" color="primary">Start Game</ion-button>
        </template>
        <template v-else-if="status == 1">
          <ion-button ref="tapBtn" @click="addScore" color="warning">Tap me!</ion-button>
        </template>
        <template v-else-if="status == 2">
          <template v-if="newRecord">
            <ConfettiExplosion :particleCount="200" :floorHeight="screen.height" :floorWidth="screen.width" :colors="['#ff0000', '#00ff00', '#0000ff']" :duration="5000" :size="3" :speed="3" :opacity="0.9" :angle="60" :decay="0.9" :spread="360" :startVelocity="30" :elementSize="3" :elementCount="200" :zIndex="100" :random="Math.random" 
              style="margin:auto;"></ConfettiExplosion>
            <p>New record!</p>
          </template>
          Your score: {{ score }}
        </template>
        <template v-else-if="status == 3">
          <ion-button @click="startGame" color="success">Restart Game</ion-button>
        </template>
      </div>
      <ion-toast :is-open="showingScore" :message="'La teva puntuació és de ' + score" :duration="5000"></ion-toast>
    </ion-content>
  </ion-page>
</template>

<script>
import { ref } from 'vue';
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButton, IonToast, IonAlert, createAnimation } from '@ionic/vue';
import { heart } from 'ionicons/icons';
import ConfettiExplosion from "vue-confetti-explosion";
export default {
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
    IonButton,
    IonToast,
    IonAlert,
    ConfettiExplosion,
  },
  setup() {
    return {
      infoIcon: heart,
      animation: null
    }
  },
  data() {
    return {
      score: 0,
      timeLeft: 10,
      status: 0,
      lastRecord: 0,
      showingScore: false,
      newRecord: false,
      showingAuthor: false,
      authorInfo: {
        author: "Ismael Semmar Galvez",
        version: "1.0",
      },
      screen: {
        width: window.innerWidth,
        height: window.innerHeight
      },
    }
  },
  methods: {
    startGame() {
      if (this.status == 2) return;
      this.status = 1;
      this.setupTapAnimation();
      this.score = 0;
      this.timeLeft = 10;
      this.timer = setInterval(() => {
        this.timeLeft--;
        if (this.timeLeft == 0) {
          clearInterval(this.timer);
          this.stopGame();
        }
      }, 1000);
    },
    addScore() {
      this.score++;
      this.animation.play();
    },
    stopGame() {
      this.showingScore = true;
      this.status = 2;
      if (this.score > this.lastRecord) {
        this.lastRecord = this.score;
        this.newRecord = true;
      } else {
        this.newRecord = false;
      }
      setTimeout(() => {
        this.status = 3;
      }, 5000);
    },
    closeAuthor() {
      this.showingAuthor = false;
    },
    openRepo() {
      window.open("https://github.com/Ism1tha/MP07_Projecte_Ionic_DAM");
      this.closeAuthor();
    },
    setupTapAnimation() {
      console.log(this);
      console.log(this.$refs.tapBtn);
      this.animation = createAnimation()
        .addElement(this.tapBtn)
        .duration(1500)
        .iterations(Infinity)
        .fromTo('transform', 'translateX(0px)', 'translateX(100px)')
        .fromTo('opacity', '1', '0.2');
    }
  },
}
</script>

<style scoped>
#container {
  text-align: center;

  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;

  color: #8c8c8c;

  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
