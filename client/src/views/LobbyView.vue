<template>
  <section class="hero welcome is-small animate__animated animate__fadeIn">
    <div class="hero-body">
      <div class="container">
        <div class="columns">
          <div class="column">
            <h1 class="title is-1">Hello, {{ currentPlayer?.name }}!</h1>
            <h2 class="subtitle is-5">Are you ready?</h2>
            <button
              class="button is-info is-medium"
              @click="setPlayerReady"
              :disabled="currentPlayer?.ready"
            >
              <strong>Ready!</strong>
            </button>
            <br /><br />
            <h2 class="title is-5">Let's wait for other players</h2>
            <h2 class="subtitle is-5">
              We need at least 3 players to start. As soon as all players are
              ready, anyone can press the Start button to start playing!
            </h2>
            <button
              class="button is-success is-medium"
              @click="setGameReady"
              :class="{ pulse: playersData?.isGameReady }"
              :disabled="!playersData?.isGameReady"
            >
              <strong>Start!</strong>
            </button>
          </div>
          <div class="column">
            <div class="container">
              <div
                v-if="playersData && playersData.players"
                class="
                  list
                  has-hoverable-list-items has-visible-pointer-controls
                "
              >
                <div
                  v-for="(player, index) in playersData.players"
                  :key="index"
                  class="list-item"
                >
                  <div
                    class="list-item-title"
                    :data-letters="`${player.name.charAt(0).toUpperCase()}`"
                  ></div>
                  <div class="list-item-content">
                    <div class="list-item-title">{{ player.name }}</div>
                    <div
                      class="list-item-description"
                      :class="{ flash: !player.ready }"
                    >
                      {{ player.ready ? "Ready" : "Not ready" }}
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "LobbyView",
  components: {},
  props: {
    currentPlayer: Object,
  },
  data() {
    return {
      playersData: undefined,
    };
  },
  methods: {
    setPlayerReady() {
      this.$socket.emit("player_ready", this.currentPlayer);
    },
    setGameReady() {
      this.$socket.emit("game_ready", this.currentPlayer.roomId);
    },
  },
  sockets: {
    update_preparation(data) {
      this.playersData = data;
    },
  },
});
</script>

<style lang="scss" scoped>
.section {
  padding: 0 1.5rem;
}

.column {
  border-left: 1px solid #dbdbdb;
}

[data-letters]:before {
  content: attr(data-letters);
  display: inline-block;
  font-size: 1em;
  width: 2.5em;
  height: 2.5em;
  line-height: 2.5em;
  text-align: center;
  border-radius: 50%;
  border: 1px solid black;
  background: transparent;
  vertical-align: middle;
  margin-right: 1em;
  color: black;
}

.flash {
  color: red;
  animation: blinker 2s linear infinite;
}

@keyframes blinker {
  50% {
    opacity: 0;
  }
}

.pulse {
  animation: pulse-animation 1s infinite;
}

@keyframes pulse-animation {
  0% {
    box-shadow: 0 0 0 0px rgba(0, 222, 89, 0.636);
  }
  100% {
    box-shadow: 0 0 0 10px rgba(23, 154, 29, 0.2);
  }
}
</style>
