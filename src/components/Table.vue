<template>
    <div id="table">

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

            buildTable(){
                console.log("this.words", this.words);
            }

        },

        mounted(){
            this.buildTable()
        }

    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
