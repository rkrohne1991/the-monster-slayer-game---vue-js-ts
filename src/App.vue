<template>
  <header>
    <h1>Monster Slayer</h1>
  </header>
  <div id="game">
    <section id="monster" class="container">
      <h2>Monster Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="monsterBarStyles"></div>
      </div>
    </section>
    <section id="player" class="container">
      <h2>Your Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="playerBarStyles"></div>
      </div>
    </section>
    <section id="controls">
      <button @click="attackMonster">ATTACK</button>
      <button :disabled="mayUseSpecialAttack" @click="specialAttackMonster">
        SPECIAL ATTACK
      </button>
      <button>HEAL</button>
      <button>SURRENDER</button>
    </section>
    <section id="log" class="container">
      <h2>Battle Log</h2>
      <ul></ul>
    </section>
  </div>
</template>

<style>
@import './assets/base.css';
</style>

<script lang="ts">
import { defineComponent } from 'vue';

const getRandomValue = (min: number, max: number) =>
  Math.floor(Math.random() * (max - min)) + min;

export default defineComponent({
  data() {
    return { currentRound: 0, playerHealth: 100, monsterHealth: 100 };
  },
  computed: {
    monsterBarStyles() {
      const health = this.monsterHealth as number;
      return { width: health + '%' };
    },
    playerBarStyles() {
      const health = this.playerHealth as number;
      return { width: health + '%' };
    },
    mayUseSpecialAttack() {
      const theCurrentRound = this.currentRound as number;
      return theCurrentRound % 3 !== 0;
    },
  },
  methods: {
    attackMonster() {
      this.currentRound++;
      const attackValue = getRandomValue(5, 12);
      this.monsterHealth -= attackValue;
      this.attackPlayer();
    },
    attackPlayer() {
      const attackValue = getRandomValue(8, 15);
      this.playerHealth -= attackValue;
    },
    specialAttackMonster() {
      this.currentRound++;
      const attackValue = getRandomValue(10, 25);
      this.monsterHealth -= attackValue;
      this.attackPlayer();
    },
  },
});
</script>
