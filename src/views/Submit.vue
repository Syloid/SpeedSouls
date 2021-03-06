<template>
  <div class="container py-6 px-3 max-w-screen-md">
    <site-notice />
    <h1 class="text-4xl font-bold leading-none mb-4 text-nord0 dark:text-nord6">
      Submit a Run.
    </h1>
    <alert type="info" class="mb-6">
      You will need a
      <a :href="VUE_APP_SPEEDRUNCOM" target="_blank" rel="noopener"
        >speedrun.com</a
      >
      account to be able to submit a run. Runs submitted on
      <a :href="VUE_APP_SPEEDRUNCOM" target="_blank" rel="noopener"
        >speedrun.com</a
      >
      will appear both on
      <a :href="VUE_APP_SPEEDRUNCOM" target="_blank" rel="noopener"
        >speedrun.com</a
      >
      and here on SpeedSouls.
    </alert>
    <error v-if="error" :error="error" />
    <div v-else-if="!games" class="progress h-2 flex flex-row" />
    <div
      v-else
      class="my-3 shadow-md text-nord0 dark:text-nord6 bg-nord5 dark:bg-nord1 rounded mb-4"
    >
      <div
        class="tab w-full overflow-hidden"
        :class="{ 'border-t border-nord4 dark:border-nord3': i !== 0 }"
        v-for="(game, i) in games"
        :key="game.id"
      >
        <label
          class="block p-5 leading-normal cursor-pointer"
          v-on:click="handleClick(game.id)"
          >{{ game.name }}</label
        >
        <div
          :id="`section-${game.id}`"
          class="tab-content overflow-hidden border-l-2 border-nord10 bg-nord6 dark:bg-nord2 leading-normal"
        >
          <div class="p-5 flex flex-row flex-wrap justify-start shadow-inner">
            <a
              class="btn border border-nord10 text-nord0 dark:text-nord6 text-left py-2 px-4 m-px rounded"
              v-for="(category, i) in game.categories"
              :key="i"
              :href="`${game.weblink}/editrun#${category.uglyHash}`"
              target="_blank"
              rel="noopener"
              >{{ category.name }}</a
            >
          </div>
        </div>
      </div>
    </div>
    <div class="flex flex-col w-full">
      <by-speedrun-com class="text-center" />
    </div>
  </div>
</template>

<script>
const { VUE_APP_SPEEDRUNCOM } = process.env;
import { reactive, onMounted, toRefs } from '@vue/composition-api';
import { useSoulsGames } from '@/api/rx-souls';
import Alert from '@/components/Alert';
import Error from '@/components/Error';
import BySpeedrunCom from '@/components/BySpeedrunCom';

export default {
  metaInfo: {
    title: 'Submit a run'
  },
  methods: {
    handleClick: function(gameId) {
      let gameSection = document.getElementById(`section-${gameId}`);
      gameSection.style.height = (!gameSection.clientHeight ? gameSection.scrollHeight : 0) + 'px';
    }
  },
  components: { Alert, BySpeedrunCom, Error },
  setup() {
    const state = reactive({
      games: undefined,
      error: null
    });

    async function fetchGames() {
      state.games = undefined;
      state.error = null;

      try {
        const games = await useSoulsGames().toPromise();
        Object.freeze(games);

        state.games = games;
      } catch (e) {
        state.error = e;
      }
    }

    onMounted(fetchGames);

    return {
      VUE_APP_SPEEDRUNCOM,
      ...toRefs(state)
    };
  }
};
</script>

<style lang="scss" scoped>
.tab {
  /* Tab content */
  .tab-content {
    height: 0;
    transition: all 0.6s;
    div {
      a {
        transition: all 0.3s;
      }
      a:hover {
        @apply bg-nord10;
      }
    }
  }
}
</style>
