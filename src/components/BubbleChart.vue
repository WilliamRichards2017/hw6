<template>
    <div id="bubbleChart">



        <button v-on:click="separate = !separate"></button>

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

                separate: false,

                minTotal: null,
                maxTotal: null,

                radiusScale: null,

                colorDict: {
                    "education": "green",
                    "economy/fiscal issues": "red",
                    "crime/justice": "blue",
                    "mental health/substance abuse": "purple",
                    "health care": "pink",
                    "energy/environment": "magenta",
                },

                yDict: {
                    "education": 0,
                    "economy/fiscal issues": 100,
                    "crime/justice": 200,
                    "mental health/substance abuse": 300,
                    "health care": 400,
                    "energy/environment": 500,
                }


            }
        },

        methods: {
            categoryToColor(category) {

                if (this.colorDict.hasOwnProperty(category)) {
                    return this.colorDict[category];
                }
                return "grey";

            },

            initScales() {

                let self = this;

                self.minTotal = d3.max(self.words, function (d) {
                    return +d.total;
                });
                self.maxTotal = d3.max(self.words, function (d) {
                    return -d.total;
                });

                self.radiusScale = d3.scaleLinear()
                    .domain([self.minTotal, self.maxTotal])
                    .range([1, 12]);
            },


            buildBubbleChart() {

                let self = this;
                let data = this.words;

                let maxTotal = d3.max(data, function(d) { return +d.total;} );
                let minTotal = d3.max(data, function(d) { return -d.total;} );
                console.log("maxTotal", maxTotal);
                console.log("minTotal", minTotal);

                let radiusScale = d3.scaleLinear()
                    .domain([minTotal, maxTotal])
                    .range([1, 12]);


                d3.select('#bubbleChart > svg')
                    .selectAll('circle')
                    .data(data)
                    .join('circle')
                    .attr('r', d => radiusScale(d.total))

                        .attr('cx', function (d) {
                        return d.sourceX;
                    })
                    .attr('cy', function (d) {
                        return d.sourceY + 100;
                    })
                    .style("fill", d => self.colorDict[d.category]);

            },

            separateBubbleChart() {

                let self = this;
                let data = self.words;

                d3.select('#bubbleChart > svg')
                    .selectAll('circle')
                    .data(data)
                    .join('circle')
                    .attr('cx', function (d) {
                        console.log("d", d.category);
                        return d.moveX;
                    })
                    .attr('cy', function (d) {
                        return d.moveY + 100;
                    })
                    .style("fill", d => self.categoryToColor(d.category));
            },


        },


        watch: {

            separate: function () {

                if (this.separate) {
                    this.separateBubbleChart();
                } else {
                    this.buildBubbleChart();
                }
            }
        },

        mounted() {
            this.initScales();
            this.buildBubbleChart();
        }


    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

