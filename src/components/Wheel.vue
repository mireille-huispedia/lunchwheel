<template>
  <div>
    <!-- type: canvas -->
    <FortuneWheel
      v-if="!loading"
      style="width: 500px"
      :canvas="canvasOptions"
      :prizes="lunches"
      :verify="canvasVerify"
      @rotateStart="onCanvasRotateStart"
      @rotateEnd="onRotateEnd"
    />
  </div>
</template>

<script>
import axios from 'axios';
import FortuneWheel from 'vue-fortune-wheel';
import 'vue-fortune-wheel/lib/vue-fortune-wheel.css';

const gqHeaders = {
  headers: {
    'Content-Type': 'application/graphql',
    'Authorization': 'Bearer ntKTH26EDqLBC5q21JeSt-45xvtenYxF'
  }
}

const graphQuest = `query MyQuery {
  entry(site: "huispediaIntern", id: 1330) {
    ... on luxeLunch_luxeLunch_Entry {
      body
      wheelOfLunch {
        body
        ... on wheelOfLunch_BlockType {
          id
          color
          bgColor
          lunchValue
          options {
            ... on options_site_BlockType {
              optionLink
              optionTitle
            }
          }
        }
      }
    }
  }
}`;

export default {
  components: {
    FortuneWheel
  },
  data() {
    return {
      lunches: [],
      loading: true,
      canvasVerify: false,
      useWeight: true,
      canvasOptions: {
        borderWidth: 2,
        borderColor: '#fff',
        fontSize: 16,
        useWeight: false,
        btnfontSize: 16,
        btnWidth: 100,
        textRadius: 220,
        textLength: 15
      }
    }
  },
  methods: {
    onCanvasRotateStart(rotate) {
      if (this.canvasVerify) {
        const verified = true // true: the test passed the verification, false: the test failed the verification
        this.DoServiceVerify(verified, 2000).then((verifiedRes) => {
          if (verifiedRes) {
            rotate() // Call the callback, start spinning
            this.canvasVerify = false // Turn off verification mode
          } else {
            alert('Failed verification')
          }
        })
        return
      }
    },
    onRotateEnd(prize) {
      this.$emit('rotate-end', prize);
    },
    // Simulate the request back-end interface, verified: whether to pass the verification, duration: delay time
    DoServiceVerify(verified, duration) {
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve(verified)
        }, duration)
      })
    },

    getCategories() {
      axios.post('https://cms.huispedia.nl/api', graphQuest, gqHeaders).then(result => {
        console.log(result)
        this.lunches = result.data.data.entry.wheelOfLunch;
        this.$emit('update-body', result.data.data.entry.body);

        let probability = Math.round(100 / this.lunches.length);
        let total = 0;

        this.lunches = this.lunches.map((entry, index) => {

          if (index + 1 === this.lunches.length) probability = 100 - total;
          else total = total + probability;
          
          return {
            id: entry.id, 
            name: entry.lunchValue,
            value: entry.lunchValue, 
            probability: probability,
            bgColor: entry.bgColor,
            color: entry.color,
            weight: 1,
            options: entry.options
          }
        })
        this.loading = false;
      }).catch(error => {
        console.log(error);
      })
    }
  },
  mounted () {
    this.getCategories();
  },
}
</script>