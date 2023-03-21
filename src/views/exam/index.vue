<template>
  <div class="container">
    <h1>{{ exam.title }}</h1>
    <p>{{ exam.description }}</p>
    <div v-for="(question, index) in exam.questions" :key="index" class="question">
      <h2>Question {{ index + 1 }}:</h2>
      <p>{{ question.text }}</p>
      <ul>
        <li v-for="(option, optionIndex) in question.options" :key="optionIndex">
          <input type="radio" :id="'q'+(index+1)+'o'+(optionIndex+1)" :value="option" v-model="answers[index]" />
          <label :for="'q'+(index+1)+'o'+(optionIndex+1)">{{ option }}</label>
        </li>
      </ul>
    </div>
    <button @click="submitExam">Submit</button>
  </div>
</template>

<script>
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
          },
          {
            text: "What is the largest planet in our solar system?",
            options: ["Jupiter", "Saturn", "Mars", "Venus"],
            answer: "Jupiter"
          },
          {
            text: "What is the smallest country in the world?",
            options: ["Vatican City", "Monaco", "San Marino", "Liechtenstein"],
            answer: "Vatican City"
          }
        ]
      },
      answers: []
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
    }
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  max-width: 600px;
  padding: 20px;
  font-family: Arial, sans-serif;
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
