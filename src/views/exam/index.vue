<template>
  <div class="container">
    <select name="status" id="status" v-model="programtype">
      <!-- 由于从接口获取的select的下拉值没有‘请选择’，所以我们要自己写一个 -->
      <option value="">请选择等级</option>
      <option v-for="(statusArr,index) in statusArr">{{statusArr}}</option>
    </select>

    <select name="status" id="status" v-on:change="handleSelectChange" v-model="leveltype" >
      <!-- 由于从接口获取的select的下拉值没有‘请选择’，所以我们要自己写一个 -->
      <option value="">请选择等级</option>
      <option v-for="(levelTypes,index) in levelTypes">{{levelTypes}}</option>
    </select>

    <select name="status" id="status"   v-model="examname" >
      <!-- 由于从接口获取的select的下拉值没有‘请选择’，所以我们要自己写一个 -->
      <option value="">请选择等级</option>
      <option v-for="(examnames,index) in examnames">{{examnames}}</option>
    </select>
    <button @click="getExam">开始</button>
    <h1>{{ exam.title }}</h1>
    <p v-html="exam.description"></p>
    <div  v-for="(question, index) in exam.questions" :key="index" class="question" ><p v-html="index+1+'.'+question.text"></p>
      <ul>
        <li v-for="(option, optionIndex) in question.options" :key="optionIndex">
          <input style="transform: scale(3.0)" type="radio" :id="'q'+(index+1)+'o'+(optionIndex+1)" :value="optionIndex" v-model="answers[index]" />
          <label v-html="' '+optionIndex+'.'+option"></label>
        </li>
      </ul>
      <button @click="showAnswer(index)">答案</button><label :id="`answer${index}`" style="display: none" v-html="question.answer+question.analyze"></label>
    </div>
    <button @click="submitExam">提交</button>
  </div>
</template>

<script>
import { getAction } from '@api/manage'
import { addDictItemsCache, ajaxGetDictItems, getDictItemsFromCache } from '@api/api'

export default {
  name: "Exam",
  data() {
    return {
      exam: {
        title: "",
        description: "",
        questions: [
          {
            text: "",
            options: [],
            answer: "",
            display:false,
            analyze:""
          }
        ]
      },
      answers: [],
      url: {
        list: '/subject/getexam?previousExamName=',
        delete: '/teaching/menu/delete',
        deleteBatch: '/teaching/menu/deleteBatch',
        getExamName:'/subject/getprogramnames',
        getExam:'/subject/getexam?previousExamName=',
        getProgramTypes:'/subject/getprogramtypes',
        getLevelTypes:'/subject/getleveltypes'
      },
      statusArr: {}, //用来接收从接口里获取出来的select下拉框里的值
      levelTypes:{},
      programtype:"",
      leveltype:"",
      examnames:[],
      examname:""
    }
  },
  methods: {
    submitExam() {
      let score = 0;
      console.log( this.exam.questions.length)
      for (let i = 0; i < this.exam.questions.length; i++) {
        if (this.answers[i] === this.exam.questions[i].answer) {
          score++;
        }
        console.log("anwer:"+this.answers[i]);
      }
      alert(`You scored ${score} out of ${this.exam.questions.length} questions.`);
    },
    showAnswer(index) {
      // $("#answer"+index).style("");
      document.getElementById("answer"+index).style = "";
    },
    getSelectInfo(){
      this.dataSource = []
      this.$http.get(this.url.getProgramTypes)
        .then(res => {
          this.statusArr=res;
        })
    },getLevelTypes(){
      this.$http.get(this.url.getLevelTypes)
        .then(res => {
          this.levelTypes=res;
        })
    },handleSelectChange(){
      //查询试卷名称
      this.$http.get(this.url.getExamName+"?programtype="+
        this.programtype+"&leveltype="+this.leveltype).then(res=>{
        this.examnames=res;

      })
    },getExam(){
      this.dataSource = []
      this.$http.get(this.url.getExam+this.examname)
        .then(res => {
          this.exam=res
          console.log(this.exam)
        })
    }

  },created () {
    this.getSelectInfo();
    this.getLevelTypes();
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  max-width: 1920px;
  padding: 20px;
  font-family: Arial, sans-serif;
  font-size: 40px;
}
.question {
  margin-top: 20px;
  border: 1px solid #ccc;
  padding: 10px;
}
h1, h2 {
  margin-top: 0;
}

ul {
  list-style: none;
  padding: 0;
}
li {
  margin-top: 10px;
}
button {
  margin-top: 20px;
  padding: 10px;
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
</style>
