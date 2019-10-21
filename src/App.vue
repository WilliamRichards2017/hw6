<template>
  <div id="app">
    <div id="header-wrap">
      <header>
        <h1>The State of the State of the States</h1>
        <div>Name: Will Richards; E-Mail: richardsw2017@gmail.com; UID: u0401321</div>

        <button v-on:click="separate = !separate"></button>

      </header>

      <div class="flexRow">
        <BubbleChart :words="words" :brushedWords="brushedWords" :seperate="seperate">
        </BubbleChart>
        <Table :words="brushedWords"></Table>
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
    seperate: false,
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
    initBrush(){

      // console.log("this.words", this.words);
      var brush = d3.brush()
              .extent([[20, 20], [780, 980]])


      let svg = d3.select("#bubbleChart").selectAll('svg');

      svg
              .attr("class", "brush")
              .call(brush.on("brush", this.brushed))
              .on("click", this.brushEnded)

    },
    brushed(){

      let self = this;

      self.brushedWords = [];

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
    },
    brushEnded(){

      let self = this;
      d3.select("#bubbleChart").selectAll("circle")
              .style("fill", d => self.colorDict[d.category])


      console.log("Words", Words);
      self.words = Words;
      self.brushedWords = Words;
    }
  },
  mounted() {
    this.initBrush();

}
}
</script>

<style>

  .flexRow{
    display: flex;
  }
</style>


