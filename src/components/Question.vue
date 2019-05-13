<template>
  <div>
    <div class="container-content">
      <span>{{x}} + {{y}} = {{this.rightAnswer}}</span>
      <div class="answer">
        <button
          class="btn"
          v-for="(number, i) in answer"
          :key="i"
          @click="clickAnswer(number)"
        >{{number}}</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["levelUp"],
  data() {
    return {
      x: randomNumber(this.levelUp.sum.min, this.levelUp.sum.max),
      y: randomNumber(this.levelUp.sum.min, this.levelUp.sum.max)
    };
  },
  methods: {
    clickAnswer(num) {
      if (num === this.rightAnswer) {
        this.$emit("success");
      } else {
        this.$emit("error");
      }
      console.log(num);
    }
  },
  computed: {
    rightAnswer() {
      return this.x + this.y;
    },
    answer() {
      let result = [this.rightAnswer]; //масив вариантов

      while (result.length < this.levelUp.answerLength) {
        //Количество кнопок
        let randNum = randomNumber(
          this.rightAnswer + 10,
          this.rightAnswer - 10
        ); //Записываю в параметры варианты рандомных ответов + - 10 разници от ответа

        if (result.indexOf(randNum) === -1) {
          // не дает вывести одинаковые кнопки(ответы)
          result.push(randNum); //пушит рандомный ответ
        }
      }

      return result.sort((a, b) => Math.random() - 0.5); // сартировка кнопок(ответов) формула
    }
  }
};
function randomNumber(max, min) {
  let diff = max - min;
  return Math.floor(Math.random() * (diff + 1)) + min;
}
</script>
<style scoped>
span {
  display: block;
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 50px;
}
.answer {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  margin: 0 auto;
}
</style>

