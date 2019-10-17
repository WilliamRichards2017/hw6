<template>
    <div id="bubbleChart">

        <svg width="1000" height="1000"></svg>


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


                colorDict: {
                    "education": "green",
                    "economy/fiscal issues" : "red",
                    "crime/justice" : "blue",
                    "mental health/substance abuse" : "purple",
                    "health care" : "pink",
                    "energy/environment":  "magenta",
                },

                yDict: {
                    "education": 0,
                    "economy/fiscal issues" : 100,
                    "crime/justice" : 200,
                    "mental health/substance abuse" : 300,
                    "health care" : 400,
                    "energy/environment":  500,
                }



            }
        },

        methods: {
            categoryToColor(category){

            if(this.colorDict.hasOwnProperty(category)){
                return this.colorDict[category];
            }
            return "grey";

          },

            buildBubbleChart() {

                let self = this;

                let data = self.words;



                let maxTotal = d3.max(data, function(d) { return +d.total;} );
                let minTotal = d3.max(data, function(d) { return -d.total;} );



                console.log("maxTotal", maxTotal);
                console.log("minTotal", minTotal);




                let radiusScale = d3.scaleLinear()
                    .domain([minTotal, maxTotal])
                    .range([1, 12]);



                let svg = d3.select('#bubbleChart > svg');


                d3.select('#bubbleChart > svg')
                    .selectAll('circle')
                    .data(data)
                    .join('circle')
                    .attr('r', d => radiusScale(d.total))
                    .attr('cx', function(d) {
                        console.log("d", d.category);
                        return d.moveX;
                    })
                    .attr('cy', function(d) {
                        return d.moveY + 100;
                    })
                    .style("fill", d => self.categoryToColor(d.category));

                // var simulation = d3.forceSimulation(data)
                //     .force('charge', d3.forceManyBody().strength(1))
                //     .force('center', d => d3.forceCenter(d.sourceX, d.sourceY))
                //     .force('collision', d3.forceCollide().radius(function(d) {
                //         return 5
                //     })).on('tick', ticked());




                // function ticked() {
                //
                //     d3.select('#bubbleChart > svg')
                //         .selectAll('circle')
                //         .data(data)
                //         .join('circle')
                //             .attr('r', 5)
                //             .attr('cx', function(d) {
                //             return d.sourceX;
                //         })
                //         .attr('cy', function(d) {
                //             return d.sourceY;
                //         })
                //
                // }




            }

        },

        mounted() {
            this.buildBubbleChart()
        }


    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

