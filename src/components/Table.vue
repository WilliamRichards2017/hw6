<template>
    <div id="table">


        <table>
            <thead>
            <tr>
                <td>Phrase</td> <td>Frequency</td> <td>Percentages</td><td>Total</td>
            </tr>
            <tr>
                <td></td> <td id="freqAxis"></td> <td id="percentAxis"></td><td></td>
            </tr>
            </thead>
            <tbody></tbody>
        </table>

    </div>
</template>

<script>
    export default {
        name: 'Table',
        props: {
            words: null
        },

        data() {
            return {

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

            cellArray(d){
                return [{"type": "phrase", "val" : d.phrase}, {"type" : "freq", "val" : [d.total, d.category]}, {"type" : "percent", "val" : [d.percent_of_d_speeches, d.percent_of_r_speeches]}, {"type" : "total", "val" :d.total}]
            },

            buildTable() {
                console.log("this.words", this.words);

                let tr = d3.select("tbody").selectAll("tr")
                    .data(this.words).join("tr");


                let td = tr.selectAll("td").data(d => this.cellArray(d)).join("td");

                let phrases = td.filter((d) => {
                    return d.type === 'phrase'
                });

                let total = td.filter((d) => {
                    return d.type === 'total'
                });

                let percent = td.filter((d) => {
                    return d.type === 'percent'
                });

                let freq = td.filter((d) => {
                    return d.type === 'freq'
                });

                console.log("phrases", phrases);

                phrases.join("text")
                    .text(d => {console.log("d.val", d.val); return d.val});

                total.join("text")
                    .text(d => d.val);

                let freqWidth = 100;



                let freqSvgs = freq.append("svg")
                    .attr("class", "chart")
                    .attr("width", freqWidth)
                    .attr("height", 25);


                let freqScale = d3.scaleLinear()
                    .domain([0, 1])
                    .range([10, freqWidth-10]);

                let barHeight = 20;

                freqSvgs
                    .append("rect")
                    .attr("width", d => freqScale(d.val[0]/50))
                    .attr("height", barHeight)
                    .style("fill", d => this.categoryToColor(d.val[1]))



            },

            buildAxis(){

                let freqWidth = 100;
                let percentWidth = 200;

                let svgHeight = 25;

                let freqScale = d3.scaleLinear()
                    .domain([0, 1])
                    .range([10, freqWidth-10]);

                let percentScale = d3.scaleLinear()
                    .domain([-100, 100])
                    .range([10, percentWidth-10]);


                let freqAxis = d3.axisTop(freqScale).ticks(3);

                let ticks=["100", "50", "0", "50", "100"];

                let percentAxis = d3.axisTop(percentScale).ticks(5).tickFormat(function (d, i) {
                    return ticks[i];
                });

                let freqHeader = d3.select("#freqAxis").append("svg")
                    .attr("width", freqWidth)
                    .attr("height", svgHeight)
                    .attr("margin", "5px");

                freqHeader.append("g")
                    .attr("class", "axis axis--x")
                    .attr("transform", "translate(0, 25)")
                    .call(freqAxis);

                let percentHeader = d3.select("#percentAxis").append("svg")
                    .attr("width", percentWidth)
                    .attr("height", svgHeight)
                    .attr("margin", "5px");


                percentHeader.append("g")
                    .attr("class", "axis axis--x")
                    .attr("transform", "translate(0, 25)")
                    .call(percentAxis);

            }

        },

        mounted(){
            this.buildAxis();
            this.buildTable();
        }

    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

    #freqAxis{
        background: beige;
        margin: 5px;
    }

    #percentAxis{
        background: beige;
        margin: 5px;
    }

    text{
        font-size: 10px;
        stroke: black;
    }

</style>
