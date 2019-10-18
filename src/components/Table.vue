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

            }
        },

        methods: {

            cellArray(d){
                return [{"type": "phrase", "val" : d.phrase}, {"type" : "freq", "val" : d.total}, {"type" : "percent", "val" : [d.percent_of_d_speeches, d.percent_of_r_speeches]}, {"type" : "total", "va" :d.total}]
            },

            buildTable() {
                console.log("this.words", this.words);

                let tr = d3.select("tbody").selectAll("tr")
                    .data(this.words).join("tr");


                let td = tr.selectAll("td").data(d => this.cellArray(d)).join("td");

                let phrases = td.filter((d) => {
                    return d.type === 'phrase'
                });

                console.log("phrases", phrases);

                phrases.append("text")
                    .text(d => {console.log("d.val", d.val); return d.val});

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
