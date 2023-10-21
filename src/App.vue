<script setup lang="ts">
import { ref, watch } from 'vue'
import BounceLoader from 'vue-spinner/src/BounceLoader.vue'
import { promiseTimeout, useStorage } from '@vueuse/core'

const moves: string[] = ['paper', 'scissors', 'rock']
const results: string[] = ['You lose', 'You win', 'Draw']

const playerMove = ref<number | null>(null)
const houseMove = ref<number | null>(null)
const result = ref<number | null>(null)
const winsCount = useStorage('winsCount', 0)
const showModal = ref<boolean>(false)
const isLoading = ref<boolean>(false)

function toggleModal() {
  showModal.value = !showModal.value
}

function getRandomInt(max: number) {
  return Math.floor(Math.random() * max)
}

function restart() {
  playerMove.value = null
  houseMove.value = null
  result.value = null
}

watch(houseMove, () => {
  if (houseMove.value != null) {
    if (playerMove.value == houseMove.value) result.value = 2
    else if (playerMove.value == 2) {
      if (houseMove.value == 0) {
        result.value = 0
      } else {
        result.value = 1
        winsCount.value++
      }
    } else if (playerMove.value == 1) {
      if (houseMove.value == 2) {
        result.value = 0
      } else {
        result.value = 1
        winsCount.value++
      }
    } else if (playerMove.value == 0) {
      if (houseMove.value == 1) {
        result.value = 0
      } else {
        result.value = 1
        winsCount.value++
      }
    }
  }
})

watch(playerMove, async () => {
  if (playerMove.value != null) {
    isLoading.value = true
    console.log('We are now waiting for house')

    await promiseTimeout(3000)
    houseMove.value = getRandomInt(3)
    isLoading.value = false
  }
})
</script>

<template>
  <main>
    <div class="header">
      <div class="title-wrapper">
        <h1 class="title">Rock</h1>
        <h1 class="title">Paper</h1>
        <h1 class="title">Scissors</h1>
      </div>
      <div class="score-wrapper">
        <p class="score-text">Score</p>
        <h1 class="score-value">{{ winsCount }}</h1>
      </div>
    </div>

    <div v-if="playerMove == null && houseMove == null" class="start-screen">
      <div
        :class="`move-container paper ${playerMove == 0 ? 'active' : ''}`"
        @click="playerMove = 0"
      >
        <div class="move-wrapper paper">
          <img
            class="move-icon"
            src="/images/icon-paper.svg"
            alt="Paper icon"
          />
        </div>
      </div>
      <div class="move-container scissors" @click="playerMove = 1">
        <div class="move-wrapper scissors">
          <img
            class="move-icon"
            src="/images/icon-scissors.svg"
            alt="Scissors icon"
          />
        </div>
      </div>
      <div class="move-container rock" @click="playerMove = 2">
        <div class="move-wrapper rock">
          <img class="move-icon" src="/images/icon-rock.svg" alt="Rock icon" />
        </div>
      </div>
    </div>
    <div
      v-if="playerMove != null"
      :class="`game-screen ${result ? 'finished' : ''} `"
    >
      <div class="player-pick pick-wrapper">
        <h1 class="player-pick-text pick-text">You picked</h1>
        <div
          :class="`move-container ${moves[playerMove]} ${
            result == 1 ? 'winner' : ''
          }`"
        >
          <div class="move-wrapper paper">
            <img
              class="move-icon"
              :src="`/images/icon-${moves[playerMove]}.svg`"
              alt="Player's pick icon"
            />
          </div>
        </div>
      </div>
      <div :class="`game-result ${result != null ? 'finished' : ''} `">
        <h1 class="game-result-text" v-if="result != null">
          {{ results[result] }}
        </h1>
        <button class="restart-button" @click="restart">Play again</button>
      </div>
      <div class="house-pick pick-wrapper">
        <h1 class="house-pick-text pick-text">The House picked</h1>
        <div
          v-if="houseMove != null"
          :class="`move-container ${moves[houseMove]} ${
            result == 0 ? 'winner' : ''
          }`"
        >
          <div class="move-wrapper paper">
            <img
              class="move-icon"
              :src="`/images/icon-${moves[houseMove]}.svg`"
              alt="House's pick icon"
            />
          </div>
        </div>
        <div v-else class="house-pick-waiter">
          <BounceLoader
            v-if="isLoading"
            color="hsl(217, 16%, 45%)"
            :loading="isLoading"
          />
        </div>
      </div>
    </div>

    <button class="rules-btn" @click="toggleModal">Rules</button>

    <div v-if="showModal" class="rules-modal">
      <div class="rules-modal-wrapper">
        <h1 class="rules-modal-title">Rules</h1>
        <img
          class="close-icon"
          src="/images/icon-close.svg"
          @click="toggleModal"
          alt="Close icon"
        />
        <img
          class="rules-img"
          src="/images/image-rules.svg"
          alt="Rules image"
        />
      </div>
    </div>

    <div class="attribution">
      Challenge by
      <a href="https://www.frontendmentor.io?ref=challenge" target="_blank"
        >Frontend Mentor</a
      >. Coded by <a href="https://github.com/aveandrian">aveandrian</a>.
    </div>
  </main>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Barlow+Semi+Condensed:wght@600;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Barlow Semi Condensed', sans-serif;
}

body {
  min-height: 100vh !important;
  background: linear-gradient(
    hsl(214, 47%, 23%),
    hsl(237, 49%, 15%)
  ) !important;
}

#root {
  min-height: 100vh !important;
  display: flex !important;
  position: relative !important;
  flex-direction: column !important;
  justify-content: start !important;
}

main {
  display: flex !important;
  flex-direction: column !important;
  align-items: center !important;
  justify-content: center !important;
}

.header {
  z-index: 1;
  margin-block: 5vh;
  margin-bottom: 10vh;
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 40.625rem;
  height: 7.5rem;
  border: 0.188rem solid hsl(217, 16%, 45%);
  border-radius: 1.25rem;
  padding-inline: 1.25rem;
  color: white;
  text-transform: uppercase;
  line-height: 1.6em !important;
}

.title {
  font-size: 2rem;
}

.score-wrapper {
  background-color: white;
  height: 80%;
  padding: 0.625rem 1.875rem;
  border-radius: 0.625rem;
  color: black;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.625rem;
}

.score-text {
  color: hsl(229, 64%, 46%);
  letter-spacing: 0.2em;
  font-size: 1rem;
}

.score-value {
  color: hsl(229, 25%, 31%);
  font-size: 4rem !important;
}

.start-screen {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
  gap: 5rem;
  row-gap: 1.875rem;
  width: 31.25rem;
  background: url('/images/bg-triangle.svg') no-repeat;
  background-position: 50% 50%;
  background-size: 50% 60%;
}

.move-container {
  height: 9.375rem;
  width: 9.375rem;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 1.25rem;
  transition: all 0.3s;
}

.move-container:hover {
  cursor: pointer;
}

.move-container.paper {
  background: linear-gradient(hsl(230, 89%, 62%), hsl(230, 89%, 65%));
  box-shadow: 0 0.313rem 0 0 #2b44c2;
}

.move-container.scissors {
  background: linear-gradient(hsl(39, 89%, 49%), hsl(40, 84%, 53%));
  box-shadow: 0 0.313rem 0 0 #c86b1d;
}

.move-container.rock {
  background: linear-gradient(hsl(349, 71%, 52%), hsl(349, 70%, 56%));
  box-shadow: 0 0.313rem 0 0 #9e1936;
}

.move-wrapper {
  height: 100%;
  width: 100%;
  border-radius: 50%;
  box-shadow: 0 -0.313rem 0 0 #babfd5;
  background-color: white;
  display: flex;
  align-items: center;
  justify-content: center;
}

.game-screen {
  display: flex;
  color: white;
  width: 43.75rem;
  align-items: start;
  justify-content: center;
  gap: 3vw;
}

.pick-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.25rem;
}

.pick-wrapper .move-container {
  height: 12.5rem;
  width: 12.5rem;
  transition: all 0.3s;
}

.pick-text {
  text-transform: uppercase;
  font-weight: 600;
  font-size: 1.5rem;
  z-index: 1;
}

.move-container.winner {
  box-shadow:
    0 0 0 3.125rem #2b385898,
    0 0 0 6.25rem #2736559d,
    0 0 0 9.375rem #22335194;
}

.house-pick-waiter {
  margin-top: 1.563rem;
  height: 9.375rem;
  width: 9.375rem;
  border-radius: 50%;
  background: hsl(214, 47%, 23%);
  display: flex;
  align-items: center;
  justify-content: center;
}

.game-result {
  align-self: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.625rem;
  transition: all 0.3s;
  transform: scaleX(0);
}

.game-result.finished {
  transform: scaleX(1);
}

.game-result-text {
  opacity: 0;
  transition: all 0.3s;
}

.game-result.finished .game-result-text {
  text-transform: uppercase;
  font-weight: 600;
  font-size: 2.8rem;
  opacity: 1;
}

.game-screen.finished .move-container:hover {
  cursor: default;
}

.restart-button {
  padding-block: 0.625rem;
  padding-inline: 1.875rem;
  opacity: 0;
  transform: scale(0);
  transition: 0.3s;
}

.game-result.finished .restart-button {
  transform: scale(1);
  width: 100%;
  padding-block: 0.625rem;
  padding-inline: 1.875rem;
  text-transform: uppercase;
  letter-spacing: 0.2em;
  border: none;
  border-radius: 0.313rem;
  background: white;
  color: hsl(229, 25%, 31%);
  opacity: 1;
}

.restart-button:hover {
  cursor: pointer;
  color: red;
}

.rules-btn {
  position: absolute;
  bottom: 10vh;
  right: 10vw;
  padding: 0.625rem 1.875rem;
  background: transparent;
  border: 0.125rem solid hsl(217, 16%, 45%);
  color: white;
  border-radius: 0.313rem;
  text-transform: uppercase;
}

.rules-btn:hover {
  cursor: pointer;
}

.rules-modal {
  z-index: 2;
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: hsla(214, 47%, 23%, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
}

.rules-modal-wrapper {
  padding: 1.25rem;
  background-color: white;
  border-radius: 0.625rem;
  display: grid;
  align-items: center;
  gap: 3vh;
}

.rules-modal-title {
  grid-column: 1;
  grid-row: 1;
  font-size: 1.5rem;
  text-transform: uppercase;
  font-weight: 600;
  color: hsl(229, 25%, 31%);
}

.close-icon {
  grid-column: 2;
  grid-row: 1;
  justify-self: end;
}

.rules-img {
  grid-column: 1 / span 2;
  grid-row: 2;
}

.close-icon:hover {
  cursor: pointer;
}

.attribution {
  color: white;
  position: absolute;
  bottom: 10vh;
}

.attribution a {
  color: white;
  text-decoration: underline;
}

.attribution a:hover {
  cursor: pointer;
  color: hsl(39, 89%, 49%);
}

@keyframes appearing {
  0% {
    transform: scaleX(0);
  }
  100% {
    transform: scaleX(1);
  }
}

@media screen and (max-width: 47rem) {
  main {
    padding: 1.25rem;
  }
  .header {
    min-width: 100%;
    width: 100%;
    margin-top: 1vh;
    margin-bottom: 5vh;
  }

  .score-value {
    font-size: 3rem;
  }
  .start-screen {
    width: 100%;
  }

  .move-container {
    width: 7.5rem;
    height: 7.5rem;
    padding: 0.938rem;
    z-index: 1;
  }

  .game-screen {
    display: grid;
    width: 100%;
    grid-template-columns: 1fr 1fr;
    row-gap: 5vh;
  }

  .game-screen .move-container {
    width: 7.5rem;
    height: 7.5rem;
    padding: 0.938rem;
  }

  .house-pick-waiter {
    margin-block: 0.625rem;
    align-self: center;
    height: 6.25rem;
    width: 6.25rem;
    border-radius: 50%;
    background: hsl(214, 47%, 23%);
  }

  .pick-wrapper {
    display: grid;
    justify-items: center;
    grid-template-columns: 1fr;
  }

  .player-pick {
    grid-column: 1;
    grid-row: 1;
  }

  .house-pick {
    grid-column: 2;
    grid-row: 1;
  }

  .pick-text {
    font-size: 1rem;
    grid-row: 2;
  }

  .game-result {
    grid-column: 1 / span 2;
    grid-row: 2;
  }

  .move-container.winner {
    z-index: 0;
    box-shadow:
      0 0 0 1.25rem #2b385898,
      0 0 0 2.5rem #2736559d,
      0 0 0 3.75rem #22335194;
  }

  .attribution {
    color: white;
    position: absolute;
    bottom: 2vh;
  }

  .rules-btn {
    right: 50%;
    transform: translateX(50%);
  }

  .rules-modal-wrapper {
    width: 100vw;
    height: 100vh;
    border-radius: 0;
    grid-template-columns: 1fr;
    grid-template-rows: repeat(3, auto);
    justify-items: center;
  }

  .rules-modal-title {
    grid-column: 1;
    grid-row: 1;
  }

  .rules-img {
    grid-column: 1;
    grid-row: 2;
  }

  .close-icon {
    grid-column: 1;
    grid-row: 3;
    justify-self: center;
  }

  .game-result.finished .restart-button {
    width: 70%;
  }
}
</style>
