<template>
  <div id="app" v-on:click="showExtremes = false">
    <div id="bg">
    <div id="header-wrap">
      <header>
        <h1>The State of the State of the States</h1>
        <div>Name: Will Richards; E-Mail: richardsw2017@gmail.com; UID: u0401321</div>

        <button v-on:click="separate = !separate">Grouped by topic</button>
        <button v-on:click="toggleExtremes"> Show Extremes</button>


      </header>

      <div class="flexRow">
        <BubbleChart :words="words" :brushedWords="brushedWords" :separate="separate" :showExtremes="showExtremes">
        </BubbleChart>
        <Table :words="brushedWords"></Table>
      </div>
    </div>
    </div>
  </div>
</template>

<script>
import BubbleChart from './components/BubbleChart.vue'
import Table from './components/Table.vue'

import Words from '../data/words'


export default {
  name: 'app',
  components: {
    BubbleChart,
    Table
  },

  data() { return {
    words: Words,
    brushedWords: Words,
    separate: false,
    showExtremes: false,
    colorDict: {
      "education": "green",
      "economy/fiscal issues": "red",
      "crime/justice": "blue",
      "mental health/substance abuse": "purple",
      "health care": "pink",
      "energy/environment": "magenta",
    }
    }
  },
  methods : {

    te(){
      this.showExtremes = !this.showExtremes;

    },

    toggleExtremes(){


      setTimeout(this.te, 100);

       console.log("toggling extremes in app", this.showExtremes);
    },

    initBrush(){

      // console.log("this.words", this.words);
      var brush = d3.brush()
              .extent([[0, 0], [800, 1000]])
              .on("end", this.brushEnded)
              .on("brush", this.brush)
              .on("start", this.brushStart);



      let svg = d3.select("#bubbleChart").selectAll('svg');

      svg
              .attr("class", "brush")
              .call(brush)


    },

    brushStart(){

      this.brushedWords = this.words;

    },

    brush(){

      this.brushedWords = [];

      let self = this;
      // console.log("this.words", this.words);

      const clonedWords = self.words.slice();


      var s = d3.event.selection;

      let x1 = s[0][0];
      let y1 = s[0][1];

      let x2 = s[1][0];
      let y2 = s[1][1];

      let yOffset = 100;

      for(let i = 0; i < clonedWords.length; i++){

        let x = null;
        let y = null;

        if(!self.separate){
          x = clonedWords[i].sourceX;
          y = clonedWords[i].sourceY + yOffset;
        }
        else if(self.separate){
          x = clonedWords[i].moveX;
          y = clonedWords[i].moveY + yOffset;
        }

        if(x > x1 && x < x2 && y > y1 && y < y2){
          this.brushedWords.push(clonedWords[i]);
        }
      }

      if(this.brushedWords.length === 0){
        console.log("null select on start");
        this.brushedWords = this.words;
      }

    },

  },
  mounted() {
    this.initBrush();

  },
  watch : {

  }
}
</script>

<style>

  #app{
    width: 100%;
    height: 100%;
  }

  .flexRow{
    display: flex;
  }


  #ff1{
    width: 100px;
    height: 100px;
    top: 100px;
    right: 200px;
  }

  #ff2{
    width: 100px;
    height: 100px;
    top: 400px;
    right: 600px;
  }

</style>


