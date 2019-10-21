<template>
  <div id="app">
    <div id="header-wrap">
      <header>
        <h1>The State of the State of the States</h1>
        <div>Name: Will Richards; E-Mail: richardsw2017@gmail.com; UID: u0401321</div>
      </header>

      <div class="flexRow">
        <BubbleChart :words="words">
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
    }
  },
  methods : {
    initBrush(){

      // console.log("this.words", this.words);
      var brush = d3.brush()
              .extent([[20, 20], [780, 980]]);

      console.log("brush", brush);

      let svg = d3.select("#bubbleChart").selectAll('svg');

      svg
              .attr("class", "brush")
              .call(d3.brush().on("brush", this.brushed));




    },
    brushed(){


      let self = this;
      console.log("this.words", self.words)

      this.brushedWords = [];

      // console.log("this.words", this.words);

      const clonedWords = self.words.slice();

      console.log("clonedWords", clonedWords)


      var s = d3.event.selection;
      console.log("selection", s);


      let x1 = s[0][0];
      let y1 = s[0][1];

      let x2 = s[1][0];
      let y2 = s[1][1];

      let yOffset = 100;

      for(let i = 0; i < clonedWords.length; i++){

        let x = clonedWords[i].sourceX;
        let y = clonedWords[i].sourceY + yOffset;

        //
        // console.log("x, y", x, y);
        // console.log("x1, y1", x1, y1);
        // console.log("x2, y2,", x2, y2);

        if(x > x1 && x < x2 && y > y1 && y < y2){
          this.brushedWords.push(clonedWords[i]);
          console.log("true");
        }
      }
      console.log("this.words after selection", this.brushedWords);

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


