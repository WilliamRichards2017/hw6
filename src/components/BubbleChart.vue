<template>
    <div id="bubbleChart">

        <div id="tooltip"></div>


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
            showExtremes: null,
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

                var tooltip = d3.select("#tooltip")
                    .style("position", "absolute")
                    .style("z-index", "10")
                    .style("visibility", "hidden")
                    .style("background", "#000")
                    .style("width", "100px")
                    .style("height", "100px");



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
                    .on("mouseover", function(d){tooltip.text(d); return tooltip.style("visibility", "visible");})
                    .on("mousemove", function(){return tooltip.style("top", (d3.event.pageY-10)+"px").style("left",(d3.event.pageX+10)+"px");})
                    .on("mouseout", function(){return tooltip.style("visibility", "hidden");});


            },

            separateBubbleChart() {

                let self = this;
                let data = self.words;

                d3.select('#bubbleChart > svg')
                    .selectAll('circle')
                    .transition().duration(1000)
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
        },

        watch: {
            showExtremes: function(){

                console.log("toggling extremes in bubbleChart");


                if(this.showExtremes){
                    d3.select("#bg")
                        .style("opacity", 0.5);

                   let fg =  d3.select("body").append("div")
                       .attr("id", "fg")

                   fg.append("text")
                       .attr("width", 20)
                       .attr("height", 20)
                       .attr("x", 100)
                       .attr("y", 100)
                       .style("stroke", "black");

                }
                else{
                    d3.select("#bg")
                        .style("opacity", 1);

                    d3.select("#fg")
                        .remove();

                }
            }
        }


    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

    .nb{

        fill: grey !important;
    }

    #tooltip{
        /*background: white;*/
        opacity: 0.5;
    }

    #bubbleChart{
        overflow: hidden;
    }

    #fg{
        top: 0;
        bottom: 0;
        width: 500px;
        height: 500px;
        position: absolute;
    }

</style>

