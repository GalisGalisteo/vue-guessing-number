<script setup>
import { ref, computed } from "vue";

const MAX_ATTEMPS = 3;
const randomNumber = ref(null);
const isPlaying = ref(false);
const inputGuess = ref("");
const remainingAttempts = ref(MAX_ATTEMPS);
const previousGuesses = ref([]);
const lastNumber = computed(() => {
  return previousGuesses.value[previousGuesses.value.length - 1];
});

const youLost = computed(() => {
  return (
    remainingAttempts.value === 0 && randomNumber.value !== lastNumber.value
  );
});
const youWon = computed(() => {
  return randomNumber.value === lastNumber.value;
});

const checkGuess = (event) => {
  event.preventDefault();
  previousGuesses.value.push(inputGuess.value);
  remainingAttempts.value--;
  inputGuess.value = "";
};

const newGame = () => {
  console.log("Game starts");
  isPlaying.value = true;
  randomNumber.value = Math.floor(Math.random() * 100 + 1);
  previousGuesses.value = [];
  remainingAttempts.value = MAX_ATTEMPS;
};
</script>

<template>
  <main>
    <h2>Number guessing game</h2>

    <section v-if="isPlaying">
      <p>Try and guess a random number between 1 and 100.</p>
      <p>
        You have {{ remainingAttempts }} attempts to guess the right number.
      </p>
      <div id="wrapper">
        <form @submit="checkGuess" action="">
          <label for="guessField">Guess a number</label>
          <input
            v-model="inputGuess"
            type="number"
            id="guessField"
            min="1"
            max="100"
            :disabled="youWon || youLost"
          />
          <button :disabled="!inputGuess" type="submit" class="button-check">
            Check Guess
          </button>
        </form>

        <div v-show="previousGuesses.length" class="resultParas">
          <p>
            Previous Guesses:
            <span class="guesses"> {{ previousGuesses.join(", ") }}</span>
          </p>
          <p>
            Guesses Remaining:
            <span class="lastResult">{{ remainingAttempts }}</span>
          </p>
          <p v-if="youWon">YOU WON! 😄</p>
          <P v-else-if="youLost"
            >YOU LOST! 😢 The number to guess was {{ randomNumber }}</P
          >
          <p v-else-if="lastNumber < randomNumber" style="color: red">
            Correct number is higher ↑
          </p>
          <p v-else style="color: green">Correct number is lower ↓</p>
        </div>
      </div>
      <button v-show="youWon || youLost" @click="newGame" style="background-color: green;">New Game</button>
    </section>
    <section v-else>
      <button @click="newGame">Start Game</button>
    </section>
  </main>
</template>

<style>
.button-check {
  background-color: rgb(128, 70, 128);
}
</style>
