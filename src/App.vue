<template>
  <div id="app" class="container">
    <div class="wheel-title">
      <span>Huispedia</span>
      <h1>Luxe Lunch</h1>
      <h2>Wat gaat het deze maand worden?</h2>
    </div>
    <div id="fortune-wheel">
      <wheel @rotate-end="setFood" />
    </div>
    <div class="food" v-if="food.length && options.length">
      <span>{{ food }}</span>
      <nav>
        <a :href="option.optionLink" target="_blank" v-for="option in options" :key="option.optionTitle">{{ option.optionTitle }}</a>
      </nav>
    </div>
    
  </div>
</template>

<script>
import confetti from 'canvas-confetti';
import Wheel from './components/Wheel.vue'

export default {
  name: 'App',
  components: {
    Wheel
  },
  data() {
    return {
      food: '',
      options: [],
      confetti: null
    }
  },
  methods: {
    setFood(val) {
      console.log(val)
      this.food = val.value;
      this.options = val.options;
      confetti();
    }
  },
  mounted () {
    const myCanvas = document.createElement('canvas');
    document.body.appendChild(myCanvas);

    this.confetti = confetti.create(myCanvas, {
      resize: true,
      useWorker: true
    });

    this.confetti({
      particleCount: 100,
      spread: 160
    });
  },
}
</script>

<style lang="scss">
$primary: #ff385c;
@import url('https://fonts.googleapis.com/css2?family=Great+Vibes&family=Megrim&display=swap');


#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 2rem;
}

.wheel-title {
  span {
    font-size: 2rem;
    margin-bottom: 1rem;
    display: block;
  }
  h1 {
    margin-top: 0;
    font-family: 'Great Vibes', cursive;
    color: $primary;
    font-size: 7rem;
  }
}

.food {
  border: .5rem solid $primary;
  color: $primary;
  padding: 3rem;
  
  margin-top: 2rem;
  border-radius: 3rem;

  nav {
    display: flex;
    justify-content: stretch;
    gap: 1rem;
  }

  a {
    background: $primary;
    color: #fff;
    padding: 1rem;
    text-decoration: none;
    font-weight: 700;
    text-transform: uppercase;
    width: 100%;
  }

  span {
    font-size: 2.25rem;
    margin-bottom: 1rem;
    display: block;
  }
}
</style>
