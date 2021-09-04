<template>
    <div class="question-box-container">
        <b-jumbotron>

                <template #lead>
                    {{ curQuestion.question }}
                </template>

                <hr class="my-4">

                    <b-list-group>
                        <b-list-group-item
                            v-for="(answer, index) in answers"
                            :key="index"
                            @click.prevent="selectAnswer(index)"
                            :class="answerClass(index)"
                        >   {{ answer }}
                        </b-list-group-item>

                    </b-list-group>


                <b-button variant="primary"
                    @click="submitAnswer"
                    :disabled="selectedIndex === null || answered"
                >
                    
                    Submit
                    </b-button>
                <b-button @click="next" variant="success" href="#">
                    
                    Next</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'
export default {
    props:{
        curQuestion: Object,
        next: Function,
        increment: Function
    },
    data(){
        return{
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers:[],
            answered: false
        }
    },
    computed:{
        answers(){
            let answers = [...this.curQuestion.incorrect_answers]
            answers.push(this.curQuestion.correct_answer)
            return answers
        }
    },
    watch:{
        curQuestion(){
            this.selectedIndex = null
            this.answered = false
            this.shuffleAnswers()
        }
    },
    methods:{
        selectAnswer(index){
            this.selectedIndex = index
            console.log(index)
        },

        submitAnswer(){
            let isCorrect = false;
            if(this.selectedIndex === this.correctIndex){
                isCorrect = true;
            }

            this.answered = true

            this.increment(isCorrect)
        },
        shuffleAnswers(){
            let answers =[...this.curQuestion.incorrect_answers, this.curQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.curQuestion.correct_answer)
        },
        answerClass(index){
            let answerClass = ''
            if(!this.answered && this.selectedIndex === index){
                answerClass = 'selected'
            }else if(this.answered && this.correctIndex === index){
                answerClass = 'correct'
            }else if(this.answered && this.selectedIndex === index && this.correctIndex !== index){
                answerClass = 'incorrect'
            }
            return answerClass
        },
    },
    mounted(){
        this.shuffleAnswers()
    }
}
</script>

<style scoped>
    .list-group{
        margin-bottom: 15px;
    }

    .list-group-item:hover{
        background: #eee;
        cursor: pointer;
    }

    .btn{
        margin: 0 5px;
    }

    .selected{
        background-color: lightblue;
    }

    .correct{
        background-color: lightgreen;
    }

    .incorrect{
        background-color: red;
    }
</style>