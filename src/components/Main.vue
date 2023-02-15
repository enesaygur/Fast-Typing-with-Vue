<template>
  <div class="container jumbotron">
    <h1 class="display-4">Hızlı Yazma Uygulaması</h1>
    <p class="lead">Hadi ne kadar hızlı yazdığını test edelim.</p>
    <hr class="my-4" />
    <div class="alert alert-primary text-center" v-if="isFinish">
      <h2>Game Over</h2>
      <h1>{{ trueCounter }} DKS</h1>
      <h4>
        Doğruluk Yüzdesi :
        {{ (trueCounter / (trueCounter + falseCounter)) * 100 }}
      </h4>
      <div>
        <p>
          Doğru kelime sayısı : <b>{{ trueCounter }}</b>
        </p>
        <p>
          Yanlış sayısı :<b>{{ falseCounter }}</b>
        </p>
      </div>
    </div>
    <div class="card" v-else>
      <div class="card-body">
        <span
          v-for="(word, key) in words.filter(
            (data, index) => oldIndex < index && index < newIndex
          )"
          :key="key"
          class="words me-2"
          v-bind:class="key !== sayac || writingWordControl"
          >{{ word }}</span
        >
      </div>
    </div>
    <div class="card">
      <div class="card-body">
        <div class="input-group input-group-lg">
          <input
            type="text"
            class="form-control"
            v-model="writingWord"
            :disabled="isFinish"
          />
          <div class="input-group-append d-flex align-content-center ms-2">
            <button
              class="btn btn-outline-secondary me-2"
              type="button"
              disabled
            >
              {{ timer }}
            </button>
            <button
              class="btn btn-outline-success"
              type="button"
              @click="getWords()"
              :disabled="isStart"
            >
              Yenile
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import wordList from "@/assets/words.json";
export default {
  data() {
    return {
      words: [],
      writingWord: "",
      isTrue: true,
      trueCounter: 0,
      falseCounter: 0,
      sayac: 0,
      timer: 5,
      interval: false,
      isStart: false,
      isFinish: false,
      wordList: wordList,
      index: 20,
      newIndex: 20,
      oldIndex: -1,
    };
  },
  watch: {
    writingWord(val) {
      if (!val || val === " ") {
        this.writingWord = "";
        return;
      }
      console.log(this.words[this.sayac]);
      if (!this.isStart) this.toogleTimer();
      const word = this.words[this.sayac].slice(0, val.length);
      const newWord = val.replace(" ", "");
      this.isTrue = word === newWord;
      if (val.indexOf(" ") !== -1) {
        this.isTrue ? this.trueCounter++ : this.falseCounter++;
        this.writingWord = "";
        this.sayac++;
        this.index++;
        if (this.index == 40) {
          this.oldIndex += 20;
          this.newIndex += 20;
          this.index = 20;
          this.sayac = 0;
        }
      }
    },
  },
  computed: {
    writingWordControl() {
      return this.isTrue || this.writingWord == ""
        ? "writing-word"
        : "writing-word bg-danger";
    },
  },
  mounted() {
    this.getWords();
  },
  methods: {
    getWords() {
      this.isFinish = false;
      this.timer = 5;
      this.sayac = 0;
      this.writingWord = "";
      this.trueCounter=0;
      this.falseCounter=0;
      this.words = this.wordList.sort(() => Math.random() - 0.5).splice(0, 300);
    },
    toogleTimer() {
      this.isStart = true;
      this.interval = setInterval(this.timeProcess, 1000);
    },
    timeProcess() {
      this.timer--;
      if (this.timer === 0) {
        clearInterval(this.interval);
        this.isFinish = true;
        this.isStart = false;
      }
    },
  },
};
</script>

<style>
.words {
  font-size: 24px;
  font-weight: 400;
  padding: 4px;
}
.writing-word {
  background-color: slategray;
  border-radius: 5px;
}
</style>
