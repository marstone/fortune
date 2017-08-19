<template>
  <div class="sim">
    <h2>Fortune Simulator</h2>
    <button id="step-forward" @click="stepForward">Step Forward</button>
    <button id="start" @click="start" :disabled="timer != null">Start</button>
    <button id="stop" @click="stop" :disabled="timer == null">Stop</button>
    <button id="classify" @click="classify">Classify</button>
    <input v-model="boost" /> {{boost}}
    <ul>
      <li v-for="item in items" >
        <span v-bind:style="{ width: Math.round(item.v / 30) + 'px', backgroundColor: item.c }" />
      </li>
    </ul>
    <div>( {{items[0].v}}, {{items[99].v}} ), year: {{year}}, stdev: {{stdev}} </div>
  </div>
</template>

<script>
export default {
  name: 'sim',
  data () {
    var items = []
    for (let i = 0; i < 100; i++) {
      items.push({ v: 10000, c: '#0a0' })
    }
    return {
      items: items,
      timer: null,
      boost: 3,
      year: 0,
      stdev: 0
    }
  },
  methods: {
    reverseMessage: function () {
      for (var i in this.items) {
        this.items[i].message = this.items[i].message.split('').reverse().join('')
      }
    },
    stepForward: function () {
      for (var i in this.items) {
        if (this.items[i].v > 0) {
          this.items[i].v--
          let lucky = Math.floor((Math.random() * 100))
          this.items[lucky].v++
        }
      }
      this.items.sort(function (a, b) {
        return a.v - b.v
      })
      this.year++
      let sum = 0
      for (let i = 0; i < 100; i++) {
        let x = this.items[i].v - 100
        sum += (x * x)
      }
      this.stdev = Math.floor(Math.sqrt(sum) * 100) / 100
      // this.stdev = sum
    },

    start: function () {
      let instance = this
      this.timer = setInterval(function () {
        let n = parseInt(instance.boost)
        if (n <= 0) {
          instance.boost = 3
          n = instance.boost
        }
        for (let x = 0; x < n; x++) {
          instance.stepForward()
        }
      }, 50)
    },

    stop: function () {
      if (this.timer != null) {
        clearInterval(this.timer)
        this.timer = null
      }
    },

    classify: function () {
      for (let i in this.items) {
        if (i < 10) {
          this.items[i].c = '#a00'
        } else if (i >= 90) {
          this.items[i].c = '#00a'
        } else {
          this.items[i].c = '#0a0'
        }
      }
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  line-height: 2px;
  height: 2px;
  margin: 1px 10px;
}

li > span {
  display: block;
  border: solid #222 1px;
  line-height: 2px;
  height: 2px;
}

</style>
