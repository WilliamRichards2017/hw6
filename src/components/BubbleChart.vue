<template>
    <div id="bubbleChart">



        <svg width="800" height="1000"></svg>


    </div>
</template>

<script>


    export default {
        name: 'BubbleChart',
        props: {
            words: null,
            brushedWords: null,
            separate: null,
        },

        data() {
            return {
                margin: {top: 40, right: 40, bottom: 40, left: 40},

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
                    .style("fill", d => self.colorDict[d.category])

            },

            separateBubbleChart() {

                let self = this;
                let data = self.words;

                d3.select('#bubbleChart > svg')
                    .selectAll('circle')
                    .data(data)
                    .join('circle')
                    .attr('cx', function (d) {
                        return d.moveX;
                    })
                    .attr('cy', function (d) {
                        return d.moveY + 100;
                    })
                    .style("fill", d => self.categoryToColor(d.category));
            },

            highlightBrushedNodes(){
                let self = this;
                let nodes = d3.select("#bubbleChart").selectAll("circle");


                let brushedPhrases = [];

                for(let i = 0; i < this.brushedWords.length; i++){
                    brushedPhrases.push(this.brushedWords[i].phrase);
                }

                let nonBrushed = nodes.filter((d) => {
                    return !brushedPhrases.includes(d.phrase);
                });

                let brushed = nodes.filter((d) => {
                    return brushedPhrases.includes(d.phrase);
                });


                nonBrushed.style("fill", "grey");

                brushed.style("fill", d => self.categoryToColor(d.category));


            }


        },


        watch: {

            separate: function () {

                if (this.separate) {
                    this.separateBubbleChart();
                } else {
                    this.buildBubbleChart();
                }
            },

            brushedWords: function(){
                this.highlightBrushedNodes();
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

    .nb{

        fill: grey !important;
    }

</style>

