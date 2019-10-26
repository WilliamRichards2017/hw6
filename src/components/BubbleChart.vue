<template>
    <div id="bubbleChart">

        <div id="tooltip"></div>

       <pre class="h4">Democratic leaning &#9 &#9 &#9 &#9 &#9 &#9 &#9 &#9 Republican leaning</pre>

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
                    "health care": "orange",
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

                console.log("building bubble Chart")


                d3.select("#bubbleSvg").remove();



                let self = this;
                let data = this.words;

                let maxTotal = d3.max(data, function(d) { return +d.total;} );
                let minTotal = d3.max(data, function(d) { return -d.total;} );

                let ticks=["50", "40", "30", "20", "10", "0", "10", "20", "30", "40", "50"];



                let percentScale = d3.scaleLinear()
                    .range([0, 750]);

                let percentAxis = d3.axisTop(percentScale).ticks(11).tickFormat(function (d, i) {
                    return ticks[i];
                });



                let percentHeader = d3.select('#bubbleChart').append("svg")
                    .attr("id", "bubbleSvg")
                    .attr("width", 800)
                    .attr("height", 1000)
                .append("svg")
                    .attr("width", 790)

                    .attr("height", 50)
                    .attr("margin", "5px");

                d3.select("#bubbleSvg").style("opacity", 0);


                percentHeader.append("g")
                    .attr("x", 15)
                    .attr("class", "axis axis--x")
                    .attr("transform", "translate(25, 25)")
                    .call(percentAxis);





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


                d3.select('#bubbleChart > svg')
                    .selectAll('circle')

                    .data(data)
                    .join('circle')

                    .attr('r', d => radiusScale(d.total))
                    .style("fill", d => self.colorDict[d.category])
                    .style("stroke", "black")
                    .style("z-axis", 0.01)
                    .on("mouseover", d => console.log(d.percent_of_d_speeches, d.percent_of_r_speeches, d.phrase, radiusScale(d.total), d.moveX, d.moveY + 100))
                    .on("mousemove", function(){return tooltip.style("top", (d3.event.pageY-10)+"px").style("left",(d3.event.pageX+10)+"px");})
                    .on("mouseout", function(){return tooltip.style("visibility", "hidden");})


                    .attr('cx', function (d) {
                        return d.sourceX;
                    })
                    .attr('cy', function (d) {
                        return d.sourceY + 100;
                    })

                d3.select("#bubbleSvg").transition().duration(100).style("opacity", 1);








            },

            mouseStart(){

                console.log("mousestart hover detected");

                d3.select("#tooltip").text("uhh ohh")
                    .style("visibility", "visible");
            },

            separateBubbleChart() {

                console.log("calling separate");
                let self = this;
                let data = self.words;

                d3.select('#bubbleChart > svg')
                    .selectAll('circle')
                    .transition().duration(300)
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


            },

            wrap(text, width) {
        text.each(function () {
            var text = d3.select(this),
                words = text.text().split(/\s+/).reverse(),
                word,
                line = [],
                lineNumber = 0,
                lineHeight = 1.1, // ems
                x = text.attr("x"),
                y = text.attr("y"),
                dy = 0, //parseFloat(text.attr("dy")),
                tspan = text.text(null)
                    .append("tspan")
                    .attr("x", x)
                    .attr("y", y)
                    .attr("dy", dy + "em");
            while (word = words.pop()) {
                line.push(word);
                tspan.text(line.join(" "));
                if (tspan.node().getComputedTextLength() > width) {
                    line.pop();
                    tspan.text(line.join(" "));
                    line = [word];
                    tspan = text.append("tspan")
                        .attr("x", x)
                        .attr("y", y)
                        .attr("dy", ++lineNumber * lineHeight + dy + "em")
                        .text(word);
                }
            }
        });
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

                let self = this;

                this.separateBubbleChart();


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
                       .style("position", "absolute")
                       .style("opacity", 0);


                  let svg = fg.append("svg")
                       .attr("width", "1000px")
                       .attr("height", "1000px");




                    let g = svg.append("g")
                      .attr("width", 50)
                      .attr("height", 50)
                      .attr("x", 50)
                      .attr("y", 300)

                    let g2 = svg.append("g")
                        .attr("width", 50)
                        .attr("height", 50)
                        .attr("x", 650)
                        .attr("y", 300)

                    g.append("rect")
                        .attr("width", 200)
                        .attr("height", 100)
                        .attr("x", 50)
                        .attr("y", 240)
                        .style("fill", "gray")
                        .style("opacity", 0.5)

                    g2.append("rect")
                        .attr("width", 200)
                        .attr("height", 100)
                        .attr("x", 571)
                        .attr("y", 240)
                        .style("fill", "gray")
                        .style("opacity", 0.5)



                     g.append("text")
                         .attr("width", 50)
                         .attr("height", 50)
                         .attr("x", 59)
                         .attr("y", 265)
                        .text("Democrats used the phrase \' climate change \' in their speeches 7 times more often than republicans")
                         .call(this.wrap, 175);

                    g2.append("text")
                        .attr("width", 50)
                        .attr("height", 50)
                        .attr("x", 580)
                        .attr("y", 265)
                        .text("Democrats used the phrase \' environment \' in their speeches 7 times more often than republicans")
                        .call(this.wrap, 175);


                    g2.append("text")
                        .text("Republicans uesed the phrase \' environment \' in their speeches 3 times more often than republicans");

                    svg.append("circle")
                        .attr("cx", 35)
                        .attr("cy", 347)
                        .attr("r", 6)
                        .style("fill", "magenta")
                        .style("stroke", "black");

                    svg.append("circle")
                        .attr("cx", 734)
                        .attr("cy", 347)
                        .attr("r", 6)
                        .style("fill", "magenta")
                        .style("stroke", "black");


                    d3.select("#fg").transition()
                        .duration(1000)
                        .style("opacity", 1);





                }
                else{

                    console.log("removed");

                    d3.select("#bg")
                        .style("opacity", 1);

                    d3.select("#fg")
                        .remove();

                    self.buildBubbleChart();


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

    .h4 {
        display: block;
        font-size: 1em;
        margin-top: 1.33em;
        margin-bottom: 1.33em;
        margin-left: 0;
        margin-right: 0;
        font-weight: bold;
    }

</style>

