<template >
  <div class="container">
    <h1>Math training. Level {{level}}</h1>
    <button @click="printNumbersInterval()">printNumbersInterval()</button>
    <hr>
    <ProgresBar :progresBar="progresBar"></ProgresBar>
    <transition name="switch" mode="out-in">
      <starting v-if="visible === 'Starting'" @visibleQuestion="visible = 'Question'"></starting>
      <question
        v-else-if="visible === 'Question'"
        @success="successQuest"
        @error="errorQuest"
      ></question>
      <Result
        v-else-if="visible === 'Result'"
        :status="status"
        @nextLevel="nextLevel"
        @repeatLevel="repeatLevel"
      ></Result>
      <message
        v-else-if="visible === 'Message'"
        :descriptionMessage="descriptionMessage"
        :success="status.success"
        :error="status.error"
        @nextQuest="nextQuestion"
      ></message>
    </transition>
  </div>
</template>

<script>
import Starting from "./components/Starting.vue";
import Question from "./components/Question.vue";
import Result from "./components/Result.vue";
import Message from "./components/Message.vue";
import ProgresBar from "./components/ProgresBar.vue";

export default {
  data() {
    return {
      visible: "Starting", // Вывод компонентов 
      descriptionMessage: '', // Отвечает за вывод success и error при нажатии на successQuest() или errorQuest()
      status: {
        success: 0, // Счетчик верных ответов
        error: 0 // Счетчик не верных ответов
      },
      level: 1,
      progresBar: 0, //Заполнение прогресс бара || при заполнении переход на следующий уровень 
    };
  },
  computed: {},
  methods: {
    successQuest() {
      this.visible = "Message";
      this.descriptionMessage = "Success: " + (this.status.success += 1);
      this.progresBar += 34;
      if (this.progresBar >= 100) {
        this.progresBar = 100;
      }
    },
    errorQuest() {
      this.visible = "Message";
      this.descriptionMessage = "Error: " + (this.status.error += 1);
      this.progresBar -= 20;
      if (this.progresBar <= 0) {
        this.progresBar = 0;
      }
    },
    nextQuestion() {
      if (this.progresBar >= 100) {
        // this.progres = 0;
        this.progresBar = 0;
        this.visible = "Result";
      } else {
        this.visible = "Question";
      }
    },
    // printNumbersInterval() {
    //   let i = 1;
    //   0;
    //   console.log(i);
    //   i++;
    //   setTimeout(this.printNumbersInterval, 100);
    // },

    nextLevel() {
      this.visible = "Question";
      this.level += 1;
    },
    repeatLevel() {
      this.visible = "Question";
      this.level = 1;
      this.status.success = 0;
      this.status.error = 0;
    }
  },
  components: {
    Starting,
    Question,
    Result,
    Message,
    ProgresBar
  }
};
</script>


<style>
.container {
  max-width: 650px;
  margin: 0 auto;
  text-align: center;
}
.container-content {
  background-color: #f5f5f5;
  padding: 25px 0;
}
.btn {
  background-color: #00a214;
  border: none;
  padding: 8px 25px;
  border-radius: 5px;
  outline: none;
  cursor: pointer;
  color: #ffffff;
}

.success-bg {
  background-color: #00a214;
}
.error-bg {
  background-color: #cc3c3c;
}

/* Animation */
.switch-enter-active,
.switch-leave-active {
  transition: all 0.3s;
}
.switch-enter,
.switch-leave-to {
  transform: rotateY(-90deg);
}
</style>
