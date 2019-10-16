<template>
  <div id="app">
    <div id="header-wrap">
      <header>
        <h1>The State of the State of the States</h1>
        <div>Name: Will Richards; E-Mail: richardsw2017@gmail.com; UID: u0401321</div>
      </header>

      <div class="flex-row">
        <BubbleChart :words="words">
        </BubbleChart>
        <!--<Table :words="words"></Table>-->
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
    words: Words
    }
  }
}
</script>

<style>
</style>


