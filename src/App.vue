<template >
  <div class="container">
    <h1>Math training. Level {{level}}</h1>

    <h3 class="progres_bar">Progres point {{levelUp.BarProgres.BarProgresSuccess}}</h3>
    <h3 class="regress_bar">Regress point {{levelUp.BarProgres.BarProgresError}}</h3>

    <!-- <button @click="printNumbersInterval()">printNumbersInterval()</button> -->
    <hr>
    <ProgresBar :progresBar="progresBar"></ProgresBar>
    <transition name="switch" mode="out-in">
      <starting v-if="visible === 'Starting'" @visibleQuestion="visible = 'Question'"></starting>
      <question
        v-else-if="visible === 'Question'"
        @success="success"
        @error="error"
        :levelUp="levelUp"
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
      descriptionMessage: "", // Отвечает за вывод success и error при нажатии на success() или error()
      status: {
        success: 0, // Счетчик верных ответов
        error: 0 // Счетчик не верных ответов
      },
      level: 1,
      progresBar: 0, //Заполнение прогресс бара || при заполнении переход на следующий уровень
      levelUp: {
        // отвечает за изменение сложности теста
        sum: {
          min: 10,
          max: 20
        },
        BarProgres: {
          BarProgresSuccess: 50,
          BarProgresError: 20
        },
        answerLength: 2
      }
    };
  },
  computed: {
    answerLengthFFF() {
      if (this.level == 2) {
        return this.answerLength = 3;
      }
    }
  },
  methods: {
    success() {
      this.visible = "Message";
      this.descriptionMessage = "Success: " + (this.status.success += 1);
      this.progresBar += this.levelUp.BarProgres.BarProgresSuccess; // прирост прогресса если success
      if (this.progresBar >= 100) {
        // прогрес не больше ста
        this.progresBar = 100;
      }
    },
    error() {
      this.visible = "Message";
      this.descriptionMessage = "Error: " + (this.status.error += 1);
      this.progresBar -= this.levelUp.BarProgres.BarProgresError; // сбрасывает прогрес если error
      if (this.progresBar <= 0) {
        // прогрес не меньше нуля
        this.progresBar = 0;
      }
    },
    nextQuestion() {
      // continue, возврат Question
      if (this.progresBar >= 100) {
        this.progresBar = 0;
        this.visible = "Result";
      } else {
        this.visible = "Question";
      }
    },

    nextLevel() {
      // выводит компонет Question меняя уровень на следующий ++
      this.visible = "Question";
      this.level += 1;
      // Сума чисел задачи
      this.levelUp.sum.min += 50;
      this.levelUp.sum.max += 50;
      // Значение прироста и отката прогресса
      let success = (this.levelUp.BarProgres.BarProgresSuccess -= 5);
      let error = (this.levelUp.BarProgres.BarProgresError += 5);
      if (success <= 25) {
        success = 25;
      }
      if (error >= 35) {
        error = 35;
      }
      //Количество Ответов
      
      // if (this.level % 2) {
      //   this.levelUp.answerLength += 1;
      // }
        this.levelUp.answerLength += 1;
        if (this.levelUp.answerLength >= 5) {
        this.levelUp.answerLength = 5;
      }
    },
    repeatLevel() {
      // выводит компонет Question меняя уровень на начальный = 0 и сбрасывая результаты success и error = 0
      this.visible = "Question";
      this.level = 1;
      this.status.success = 0;
      this.status.error = 0;
      this.levelUp.BarProgres.BarProgresSuccess = 50;
      this.levelUp.BarProgres.BarProgresError = 20;
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
h3 {
  display: inline-block;
  margin: 0 3rem 1rem 3rem;
}
h3.progres_bar {
  color: #00a214;
}
h3.regress_bar {
  color: #cc3c3c;
}
</style>
