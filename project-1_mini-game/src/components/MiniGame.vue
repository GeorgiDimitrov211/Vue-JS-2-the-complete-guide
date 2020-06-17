<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            :style="{ width: health.person + '%' }"
          >
            {{ health.person }}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">MONSTER</h1>
        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
            :style="{ width: health.monster + '%' }"
          >
            {{ health.monster }}
          </div>
        </div>
      </div>
    </section>
    <section class="row controls" v-if="!gameInProgress">
      <div class="small-12 columns">
        <button id="start-game" @click="gameInProgress = true">
          START NEW GAME
        </button>
      </div>
    </section>
    <section class="row controls" v-else>
      <div class="small-12 columns">
        <button id="attack" @click="attackMonster()">ATTACK</button>
        <button id="special-attack" @click="specialAttackMonster()">
          SPECIAL ATTACK
        </button>
        <button id="heal" @click="healPerson()">HEAL</button>
        <button id="give-up" @click="stopGame('You lost')">GIVE UP</button>
      </div>
    </section>
    <section class="row log" v-if="turns.length > 0">
      <div class="small-12 columns">
        <ul>
          <li v-for="turn in turns" :key="turn.msg" :class="turn.turnType">
            {{ turn.msg }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "MiniGame",
  props: {},
  data: () => {
    return {
      health: {
        person: 100,
        monster: 100,
      },
      gameInProgress: false,
      turns: [],
    };
  },
  methods: {
    attackMonster() {
      let dmg = Math.floor(Math.random() * 10) + 1;
      this.health.monster -= dmg;
      this.turns.unshift({
        turnType: "attack",
        msg: `You have dealt ${dmg} damage to the monster. (Attack)`,
      });
      this.attackPerson();
    },
    specialAttackMonster() {
      let dmg = Math.floor(Math.random() * 10) + 5;
      this.health.monster -= dmg;
      this.turns.unshift({
        turnType: "attack",
        msg: `You have dealt ${dmg} damage to the monster. (Special attack)`,
      });
      this.attackPerson();
    },
    attackPerson() {
      if (this.health.monster < 1) {
        this.health.monster = 0;
        this.stopGame("You won!");
      }
      let dmg = Math.floor(Math.random() * 10) + 1;
      this.health.person -= dmg;
      this.turns.unshift({
        turnType: "defend",
        msg: `The monster hit you with ${dmg} damage. (Attack)`,
      });
      if (this.health.person < 1) {
        this.health.person = 0;
        this.stopGame("You lost!");
      }
    },
    healPerson() {
      let heal = Math.floor(Math.random() * 10) + 5;
      this.health.person += heal;
      this.turns.unshift({
        turnType: "heal",
        msg: `You healed with ${heal} HP. (Heal)`,
      });
      this.attackPerson();
      if (this.health.person > 100) {
        this.health.person = 100;
      }
    },
    stopGame(msg) {
      this.health.person = 100;
      this.health.monster = 100;
      if (confirm(msg + " New Game?")) {
        this.gameInProgress = true;
      } else {
        this.gameInProgress = false;
      }
    },
  },
};
</script>

<style scoped>
@import "../foundation.min.css";
.text-center {
  text-align: center;
}

.healthbar {
  width: 80%;
  height: 40px;
  background-color: #eee;
  margin: auto;
  transition: width 500ms;
}

.controls,
.log {
  margin-top: 30px;
  text-align: center;
  padding: 10px;
  border: 1px solid #ccc;
  box-shadow: 0px 3px 6px #ccc;
}

.turn {
  margin-top: 20px;
  margin-bottom: 20px;
  font-weight: bold;
  font-size: 22px;
}

.log ul {
  list-style: none;
  font-weight: bold;
  text-transform: uppercase;
}

.log ul li {
  margin: 5px;
}

.log ul .attack {
  color: blue;
  background-color: #e4e8ff;
}

.log ul .defend {
  color: red;
  background-color: #ffc0c1;
}
.log ul .heal {
  color: green;
  background-color: rgb(136, 230, 136);
}

button {
  font-size: 20px;
  background-color: #eee;
  padding: 12px;
  box-shadow: 0 1px 1px black;
  margin: 10px;
}

#start-game {
  background-color: #aaffb0;
}

#start-game:hover {
  background-color: #76ff7e;
}

#attack {
  background-color: #ff7367;
}

#attack:hover {
  background-color: #ff3f43;
}

#special-attack {
  background-color: #ffaf4f;
}

#special-attack:hover {
  background-color: #ff9a2b;
}

#heal {
  background-color: #aaffb0;
}

#heal:hover {
  background-color: #76ff7e;
}

#give-up {
  background-color: #ffffff;
}

#give-up:hover {
  background-color: #c7c7c7;
}
</style>
