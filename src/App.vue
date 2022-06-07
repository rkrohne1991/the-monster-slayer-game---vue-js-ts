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
    <div class="container" v-if="winner">
      <h2>Game Over!</h2>
      <h3 v-if="winner === 'monster'">You lost!</h3>
      <h3 v-else-if="winner === 'player'">You won!</h3>
      <h3 v-else>It's a draw!</h3>
      <button @click="startGame">Start New Game</button>
    </div>
    <section id="controls" v-else>
      <button @click="attackMonster">ATTACK</button>
      <button :disabled="mayUseSpecialAttack" @click="specialAttackMonster">
        SPECIAL ATTACK
      </button>
      <button @click="healPlayer">HEAL</button>
      <button @click="surrender">SURRENDER</button>
    </section>
    <section id="log" class="container">
      <h2>Battle Log</h2>
      <ul>
        <li v-for="(logMessage, index) in logMessages" :key="index">
          <span
            :class="{
              'log--player': logMessage.actionBy === 'player',
              'log--monster': logMessage.actionBy === 'monster',
            }"
            >{{ logMessage.actionBy === 'player' ? 'Player' : 'Monster' }}</span
          >

          <span v-if="logMessage.actionType === 'heal'">
            heals himself for
            <span class="log--heal">{{ logMessage.actionValue }}</span></span
          >

          <span v-else>
            attacks and deals
            <span class="log--damage">{{ logMessage.actionValue }}</span></span
          >
        </li>
      </ul>
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

interface LogMessages {
  actionBy: string;
  actionType: string;
  actionValue: number;
}

interface DataType {
  currentRound: number;
  playerHealth: number;
  monsterHealth: number;
  winner: null | string;
  logMessages: LogMessages[];
}

export default defineComponent({
  data(): DataType {
    return {
      currentRound: 0,
      playerHealth: 100,
      monsterHealth: 100,
      winner: null,
      logMessages: [],
    };
  },
  computed: {
    monsterBarStyles() {
      const health = this.monsterHealth as number;
      if (health < 0) {
        return { width: '0%' };
      }
      return { width: health + '%' };
    },
    playerBarStyles() {
      const health = this.playerHealth as number;
      if (health < 0) {
        return { width: '0%' };
      }
      return { width: health + '%' };
    },
    mayUseSpecialAttack() {
      const theCurrentRound = this.currentRound as number;
      return theCurrentRound % 3 !== 0;
    },
  },
  watch: {
    playerHealth(value: number) {
      if (value <= 0 && this.monsterHealth <= 0) {
        // A draw
        this.winner = 'draw';
      } else if (value <= 0) {
        // Player lost
        this.winner = 'monster';
      }
    },
    monsterHealth(value: number) {
      if (value <= 0 && this.playerHealth <= 0) {
        // A draw
        this.winner = 'draw';
      } else if (value <= 0) {
        // Monster lost
        this.winner = 'player';
      }
    },
  },
  methods: {
    startGame() {
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.winner = null;
      this.currentRound = 0;
      this.logMessages = [];
    },
    attackMonster() {
      this.currentRound++;
      const attackValue = getRandomValue(5, 12);
      this.monsterHealth -= attackValue;
      this.addLogMessage('player', 'attack', attackValue);
      this.attackPlayer();
    },
    attackPlayer() {
      const attackValue = getRandomValue(8, 15);
      this.playerHealth -= attackValue;
      this.addLogMessage('monster', 'attack', attackValue);
    },
    specialAttackMonster() {
      this.currentRound++;
      const attackValue = getRandomValue(10, 25);
      this.monsterHealth -= attackValue;
      this.addLogMessage('player', 'special-attack', attackValue);
      this.attackPlayer();
    },
    healPlayer() {
      this.currentRound++;
      const healValue = getRandomValue(8, 20);
      if (this.playerHealth + healValue > 100) {
        this.playerHealth = 100;
      } else {
        this.playerHealth += healValue;
      }
      this.addLogMessage('player', 'heal', healValue);
      this.attackPlayer();
    },
    surrender() {
      this.winner = 'monster';
    },
    addLogMessage(who: string, what: string, value: number) {
      this.logMessages.unshift({
        actionBy: who,
        actionType: what,
        actionValue: value,
      });
    },
  },
});
</script>
