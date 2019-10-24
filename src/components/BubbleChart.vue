<template>
    <div id="bubbleChart">

        <div id="tooltip"></div>


        <svg width="800" height="1000" id="bubbleSvg"></svg>


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

                console.log("this.seperate in bubble chart", this.separate);


                let self = this;
                let data = this.words;

                let maxTotal = d3.max(data, function(d) { return +d.total;} );
                let minTotal = d3.max(data, function(d) { return -d.total;} );


                let radiusScale = d3.scaleLinear()
                    .domain([minTotal, maxTotal])
                    .range([1, 12]);

                var tooltip = d3.select("#tooltip")
                    .style("position", "absolute")
                    .style("z-index", "3000")
                    .style("visibility", "hidden")
                    .style("background", "#000")
                    .style("width", "100px")
                    .style("height", "100px");



                let circles = d3.select('#bubbleChart > svg')
                    .selectAll('circle')

                    .data(data)
                    .join('circle')

                    .attr('r', d => radiusScale(d.total))
                    .style("fill", d => self.colorDict[d.category])
                    .style("z-axis", 0.01)
                    .on("mouseover", this.mouseStart)
                    .on("mousemove", function(){return tooltip.style("top", (d3.event.pageY-10)+"px").style("left",(d3.event.pageX+10)+"px");})
                    .on("mouseout", function(){return tooltip.style("visibility", "hidden");});

                circles.transition().duration(1000)

                    .attr('cx', function (d) {
                        return d.sourceX;
                    })
                    .attr('cy', function (d) {
                        return d.sourceY + 100;
                    })



            },

            mouseStart(){

                console.log("mousestart hover detected");

                d3.select("#tooltip").text("uhh ohh")
                    .style("visibility", "visible");
            },

            separateBubbleChart() {

                console.log("this.seperate in bubble chart", this.separate);

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

        mounted() {
            this.initScales();
            this.buildBubbleChart();
        },

        watch: {

            separate: function () {

                console.log("sperate inside watcher");

                if (this.separate) {
                    this.separateBubbleChart();
                } else {
                    this.buildBubbleChart();
                }
            },

            brushedWords: function(){
                this.highlightBrushedNodes();
            },

            showExtremes: function(){

                console.log("toggling extremes in bubbleChart");


                if(this.showExtremes){
                    d3.select("#bg")
                        .style("opacity", 0.5);

                   let fg =  d3.select("#app").append("div")
                       .attr("id", "fg")
                       .style("top", 0)
                       .style("left", 0)
                       .style("width", "500px")
                       .style("height", "500px")
                       .style("position", "absolute");


                  let svg = fg.append("svg")
                       .attr("width", "1000px")
                       .attr("height", "1000px");


                   svg.append("text")
                       .attr("width", 50)
                       .attr("height", 20)
                       .attr("x", 50)
                       .attr("y", 300)
                        .text("Dems are left")

                    svg.append("text")
                        .attr("width", 50)
                        .attr("height", 20)
                        .attr("x", 400)
                        .attr("y", 400)
                        .text("Reps are right");

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

