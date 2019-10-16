<template>
    <div id="bubbleChart">

        <svg width="900" height="600"></svg>




    </div>
</template>

<script>

    export default {
  name: 'BubbleChart',
    props: {
        words: null
    },

    data() {
        return {
            margin: {top: 40, right: 40, bottom: 40, left: 40},


        }
    },

    methods: {

   buildBubbleChart(){



           let data = this.words;

       console.log(data);


       var width = 960,
               height = 250,
               radius = 10;

           var svg = d3.select('body').append('svg')
               .attr('width', width)
               .attr('height', height)


           function tick(){

               d3.selectAll('.circ')
                   .attr('cx', function(d){return d.x})
                   .attr('cy', function(d){return d.y})

           }


           svg.selectAll('.circ')
               .data(data)
               .enter().append('circle').classed('circ', true)
               .attr('r', radius)
               .attr('cx', function(d){
                   return d.x;})
               .attr('cy', function(){return height/2;})
               .on('click', function(){
                   var circ = d3.select(this);

                   circ.style('stroke', '#56C6D8')
                       .style('stroke-width', 3)
               })



           var simulation = d3.forceSimulation(data)
               .force('x', d3.forceX(function(d){
                   return d.x;
                       // return x(d[data_set])
                   }).strength(0.99)
               )
               .force('y', d3.forceY(height/2).strength(0.05))
               .force('collide', d3.forceCollide(radius))
               .alphaDecay(0)
               .alpha(0.12)
               .on('tick', tick)


           var init_decay;
           init_decay = setTimeout(function(){
               console.log('init alpha decay')
               simulation.alphaDecay(0.1);
           }, 8000);





               simulation.force('x', d3.forceX(function(d){
                return d.x;
               }))

               simulation
                   .alphaDecay(0)
                   .alpha(0.12)
                   .restart()

               clearTimeout(init_decay);

               init_decay = setTimeout(function(){
                   console.log('init alpha decay');
                   simulation.alphaDecay(0.1);
               }, 8000);


       }

    },

    mounted(){
        this.buildBubbleChart()
    }


}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

var svg = d3.select("svg"),
margin = {top: 40, right: 40, bottom: 40, left: 40},
width = svg.attr("width") - margin.left - margin.right,
height = svg.attr("height") - margin.top - margin.bottom;
