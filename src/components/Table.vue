<template>
    <div id="table">


        <table>
            <thead>
            <tr>
                <td id="phraseHeader" @click="sortBy('T')">Phrase</td>
                <td id="freqHeader" @click="sortBy('F')" ><table> <tr> <td>Frequency</td> </tr> <tr><td id="freqAxis"></td></tr></table> </td>

                <td id="percentHeader" @click="sortBy('P')"><table><tr><td>Percentages</td> </tr> <tr> <td id="percentAxis"></td></tr></table></td>
                <td id="totalHeader" @click="sortBy('F')">Total</td>
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

                freqWidth: 70,
                percentWidth: 150,
                svgHeight: 25,
                freqScale: null,
                percentScale: null,

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

            initScales() {


                this.freqScale = d3.scaleLinear()
                    .domain([0, 1])
                    .range([10, this.freqWidth - 10]);

                this.percentScale = d3.scaleLinear()
                    .domain([0, 100])
                    .range([10, this.percentWidth - 10]);

            },


            buildAxis(){

                let self = this;

                let ticks=["100", "50", "0", "50", "100"];

                let freqAxis = d3.axisTop(self.freqScale).ticks(3);

                let percentAxis = d3.axisTop(self.percentScale).ticks(5).tickFormat(function (d, i) {
                    return ticks[i];
                });

                let freqHeader = d3.select("#freqAxis").append("svg")
                    .attr("width", self.freqWidth)
                    .attr("height", self.svgHeight)
                    .attr("margin", "5px");

                freqHeader.append("g")
                    .attr("class", "axis axis--x")
                    .attr("transform", "translate(0, 25)")
                    .call(freqAxis);

                let percentHeader = d3.select("#percentAxis").append("svg")
                    .attr("width", self.percentWidth)
                    .attr("height", self.svgHeight)
                    .attr("margin", "5px");

                percentHeader.append("g")
                    .attr("class", "axis axis--x")
                    .attr("transform", "translate(0, 25)")
                    .call(percentAxis);



            },

            buildTable() {

                let self = this;

                d3.select("tbody").selectAll('svg').remove();

                let tr = d3.select("tbody").selectAll("tr")
                    .data(self.words).join("tr");

                let td = tr.selectAll("td").data(d => self.cellArray(d)).join("td");

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

                phrases.join("text")
                    .text(d =>d.val);

                total.join("text")
                    .text(d => d.val);

                let freqSvgs = freq.append("svg")
                    .attr("class", "chart")
                    .attr("width", self.freqWidth)
                    .attr("height", 25);

                let percentSvgs = percent.append("svg")
                    .attr("class", "chart")
                    .attr("width", self.percentWidth)
                    .attr("height", 25);

                let barHeight = 20;

                freqSvgs
                    .append("rect")
                    .attr("width", d => self.freqScale(d.val[0]/50))
                    .attr("height", barHeight)
                    .style("fill", d => this.categoryToColor(d.val[1]))

                percentSvgs.append("rect")
                    .attr("width", d => self.percentScale(d.val[1]))
                    .attr("height", barHeight)
                    .attr("x", self.percentWidth/2)
                    .style("fill", "red");

                percentSvgs.append("rect")
                    .attr("width", d => self.percentScale(d.val[0]))
                    .attr("height", barHeight)
                    .attr("x",d => (self.percentWidth/2) - self.percentScale(d.val[0]))
                    .style("fill", "blue");
            },


            categoryToColor(category) {

                if (this.colorDict.hasOwnProperty(category)) {
                    return this.colorDict[category];
                }
                return "grey";

            },

            sortBy(type){
                console.log("sorting by", type);


                    let preSort = this.words.slice(0);

                    this.words.sort(function (a, b) {

                        let aPoint = null;
                        let bPoint = null;

                        if(type === "F"){
                            aPoint = parseInt(a.total);
                            bPoint = parseInt(b.total);

                        }


                        if(type === 'P'){
                            aPoint = a.percent_of_d_speeches + a.percent_of_R_speeches;
                            bPoint = b.percent_of_d_speeches + b.percent_of_R_speeches;
                        }

                        if(type == 'T'){
                            aPoint = a.phrase;
                            bPoint = b.phrase;
                        }

                        if (aPoint< bPoint) {
                            return -1;
                        }
                        if (bPoint < aPoint) {
                            return 1;
                        }
                        // a must be equal to b
                        return 0;
                    });

                    let arrEq = function arraysEqual(a1, a2) {
                        /* WARNING: arrays must not contain {objects} or behavior may be undefined */
                        return JSON.stringify(a1) == JSON.stringify(a2);
                    }

                    console.log("sorted words", this.words);

                    if (arrEq(preSort, this.words)) {
                        this.words.reverse();
                    }

            },


            cellArray(d){
                return [{"type": "phrase", "val" : d.phrase}, {"type" : "freq", "val" : [d.total, d.category]}, {"type" : "percent", "val" : [d.percent_of_d_speeches, d.percent_of_r_speeches]}, {"type" : "total", "val" :d.total}]
            },

        },

        beforeMount(){
            this.initScales();
        },
        mounted(){
            this.buildAxis();
            this.buildTable();
        },


        watch: {

        words: function () {
            this.buildTable();
        }

    }


    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

    #freqHeader{
        background: beige;
        margin: 5px;
    }

    #percentHeader{
        background: beige;
        margin: 5px;
    }

    text{
        font-size: 10px;
        stroke: black;
    }

</style>
