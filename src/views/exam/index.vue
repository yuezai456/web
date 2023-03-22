<template>
  <div class="container">
    <h1>{{ exam.title }}</h1>
<!--    <p>{{ exam.description }}</p>-->
    <j-dict-select-tag type="list" style="width: 500px" v-decorator="['fileType', validatorRules.fileType]" :trigger-change="true" dictCode="file_type" placeholder="请选择试卷"/>
    <div v-for="(question, index) in exam.questions" :key="index" class="question" >
      <h2>Question {{ index + 1 }}:</h2>
      <p class="selectors">{{ question.text }}</p>
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
        deleteBatch: '/teaching/menu/deleteBatch'
      },
      validatorRules: {
      fileType: {rules: [
        {required: true, message: '请输入文件类型!'},
      ]},
      fileName: {rules: [
        {required: true, message: '请输入文件名!'},
      ]},
      filePath: {rules: [
        {required: true, message: '请输入文件路径!'},
      ]},
      fileLocation: {rules: [
        {required: true, message: '请输入存储位置!'},
      ]},
      fileTag: {rules: [
      ]},
    },
      URGENTLEVEL:[{
        code:'01',
        name:' '
      },{
        code:'02',
        name:'平急'
      }]
    };
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
          document.querySelector(".selectors").innerHTML=res.title;
          // this.exam.questions[0].options=[res.optionA,res.optionB,res.optionC,res.optionD]
          if(res.answer!="0"){
            document.querySelector(".selector1").innerHTML = "A: "+res.optionA;

            document.querySelector(".selector2").innerHTML = "B: "+res.optionB;
            document.querySelector(".selector3").innerHTML = "C: "+res.optionC;
            document.querySelector(".selector4").innerHTML = "D: "+res.optionD;
            console.log(res)
          }
          else {
            document.querySelector(".selector1").innerHTML = "对 ";
            document.querySelector(".selector2").innerHTML = "错 ";
            document.querySelector(".selector3").innerHTML = "";
            document.querySelector(".selector4").innerHTML = "";
          }

        })
    }
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
