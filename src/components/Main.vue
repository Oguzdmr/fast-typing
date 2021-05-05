<template>
    <div class="main">
        <div class="container jumbotron">
            <h1 class="display-4">Fast Typing</h1>
            <p class="lead">How fast can you type using the keyboard?</p>
            <hr class="my-4" />
            <div v-if="isFinish" class="aler alert-primary">
                <h1>Game Over</h1>
                <p class="display-4"> {{dks}} DKS</p>
                <span>Percentage of Accuracy: % {{truePercent}} </span><br>
                <span>True Word : {{trueCount}} </span><br>
                <span>False Word : {{falseCount}} </span><br>
                <button @click="newGame" class="btn btn-success btn-lg btn-block">New Game</button>
            </div>
            <div v-else>
                <div class="card">
                <div class="card-body">
                    <span v-for="(word,key) in words.filter((data,index)=>index<=15)" :key="key" v-bind:class="key==0 && writingWordControl " class="words ml-2">{{word}}</span>
                </div>
                </div>
            <div class="card">
                <div class="card-body bg-secondary">
                    <div class="input-group input-group-lg">
                        <input type="text" class="form-control" 
                             aria-describedby="basic-addon2" v-model="writingWord" />
                        <div class="input-group-append bg-light">
                            <button class="btn btn-outline-secondary" disabled type="button">
                                {{timer}} sn
                            </button>
                            <button  @click="newGame" class="btn btn-outline-secondary" type="button">
                                Refresh
                            </button>
                        </div>
                    </div>
                </div>
            </div>
            </div>
        </div>
    </div>
</template>
<script>
    import wordList from '/vueproject/fast-typing/src/assets/words.json'
    export default {
        data() {
            return {
                words:[],
                writingWord: null,
                isTrue:true,
                trueCount:0,
                falseCount:0,
                timer:5,
                interval:false,
                isRunning:false,
                isFinish:false,
                wordList:wordList
            }
        },
        watch:{
            writingWord(val){
                if(!val || val ===' '){
                    this.writingWord =''
                    return
                }
                if(!this.isRunning) {
                    this.toggleTimer() 
                }
                    

                let word = this.words[0].slice(0,val.length).toUpperCase()
                let userWord = val.trim().toUpperCase()

                this.isTrue = word === userWord

                if(val.indexOf(' ') !== -1){
                    this.isTrue ? this.trueCount++ : this.falseCount++
                    this.words.splice(0,1)
                    this.writingWord = ''
                    this.isTrue=true
                }
            }
        },
        mounted() {
            this.getWords()
        },
        computed:{
            writingWordControl(){
                return this.isTrue ? 'writing-word' :'writing-word bg-danger' 
            },
            dks(){
                return 300 - this.words.length
            },
            truePercent(){
                const percent = (100/this.dks)
                return(percent*this.trueCount)
            }
        },
        methods: {
            toggleTimer(){
                this.isRunning=true
                this.interval = setInterval(this.timeProcess,1000)
            },
            timeProcess(){
                if(this.timer === 0){
                    clearInterval(this.interval)
                    this.isFinish=true
                    return
                }   
                this.timer--
            },
            getWords(){
                this.words = this.wordList.sort(()=>Math.random()-0.5).splice(0,300)
            },
            newGame(){
                clearInterval(this.interval)
                this.getWords()
                this.writingWord= null,
                this.timer=60
                this.isTrue=true
                this.isRunning=false
                this.trueCount=0
                this.falseCount=0
                this.isFinish=false
            }

        },
    };
</script>
<style>
.words{
    font-size: 25px;
    font-weight: 400;
}
.writing-word{
    background-color: lightslategray;
    color: white;
    border-radius: 5px;
    padding: 5px;
}
</style>