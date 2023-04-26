<template>
  <div class="container">
    <h1>{{ exam.title }}</h1>
    <p>{{ exam.description }}</p>
    <select name="status" id="status" v-model="programtype">
      <!-- 由于从接口获取的select的下拉值没有‘请选择’，所以我们要自己写一个 -->
      <option value="">请选择语言</option>
      <option v-for="(statusArr,index) in statusArr">{{statusArr}}</option>
    </select>

    <select name="status" id="status"v-on:change="handleSelectChange" v-model="leveltype" >
      <!-- 由于从接口获取的select的下拉值没有‘请选择’，所以我们要自己写一个 -->
      <option value="">请选择等级</option>
      <option v-for="(levelTypes,index) in levelTypes">{{levelTypes}}</option>
    </select>

    <select name="status" id="status" v-model="examname">
      <!-- 由于从接口获取的select的下拉值没有‘请选择’，所以我们要自己写一个 -->
      <option value="">请选择编程语言和等级</option>
      <option v-for="(examname,index) in examname">{{examname}}</option>
    </select>

    <div v-for="(question, index) in exam.questions" :key="index" class="question" >
      <h2>Question {{ index + 1 }}:</h2>
      <p>{{ question.text }}</p>
      <ul>
        <li v-for="(option, optionIndex) in question.options" :key="optionIndex">
          <input type="radio" :id="'q'+(index+1)+'o'+(optionIndex+1)" :value="option" v-model="answers[index]" />
          <label :for="'q'+(index+1)+'o'+(optionIndex+1)" :class="'selector'+(optionIndex+1)">{{ option }}</label>
        </li>
      </ul>
    </div>
    <button @click="loadData">Submit</button>
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
        title: "Sample Exam",
        description: "This is a sample exam for demonstration purposes.",
        questions: [
          {
            text: "What is the capital of France?",
            options: ["Paris", "London", "Berlin", "Rome"],
            answer: "Paris"
          }
        ]
      },
      answers: [],
      url: {
        list: '/subject/getexamlist?id=420&type=1&index=28',
        delete: '/teaching/menu/delete',
        deleteBatch: '/teaching/menu/deleteBatch',
        getExamName:'/subject/getprogramnames',
        getExam:'/subject/getexam?previousExamName=202009机器人四级理论综合真题',
        getProgramTypes:'/subject/getprogramtypes',
        getLevelTypes:'/subject/getleveltypes'
      },
      statusArr: {}, //用来接收从接口里获取出来的select下拉框里的值
      levelTypes:{},
      programtype:"",
      leveltype:"",
      examname:{},
    }
  },
  methods: {
    submitExam() {
      let score = 0;
      for (let i = 0; i < this.exam.questions.length; i++) {
        if (this.answers[i] === this.exam.questions[i].answer) {
          score++;
        }
      }
      alert(`You scored ${score} out of ${this.exam.questions.length} questions.`);
    },
    loadData() {
      this.dataSource = []
      this.$http.get(this.url.list)
        .then(res => {
          this.exam.title=res.previousExamname;
          this.exam.description=res.createTime;
          //document.querySelector(".selectors").innerHTML=res.title;
          this.exam.questions.text=res.title;

          //this.exam.questions[0].options=[res.optionA,res.optionB,res.optionC,res.optionD]
          if(res.answer!="0"){
            document.querySelector(".selector1").innerHTML = "A: "+res.optionA;
            document.querySelector(".selector2").innerHTML = "B: "+res.optionB;
            document.querySelector(".selector3").innerHTML = "C: "+res.optionC;
            document.querySelector(".selector4").innerHTML = "D: "+res.optionD;
            console.log("res:"+res[0].optionA)

          }
          else {
            document.querySelector(".selector1").innerHTML = "对";
            document.querySelector(".selector2").innerHTML = "错";
            document.querySelector(".selector3").innerHTML = "";
            document.querySelector(".selector4").innerHTML = "";
          }

        })
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
      this.examname= this.$http.get(this.url.getExamName+"?programtype="+this.programtype+"&leveltype="+this.leveltype)
      console.log(this.examname);
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
