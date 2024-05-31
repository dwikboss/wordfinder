<template>
  <div id="app">
    <div v-if="loading" class="generating">
      Puzzel aan het genereren...
    </div>
    <CrosswordGrid :puzzle="puzzleData" @word-found="handleWordFound" />
    <div class="hints">
      <ul>
        <li v-for="(word, index) in puzzleData.words" :key="index">
          <span :class="{ found: foundWords.includes(word.word) }">{{ word.hint }}</span><br />
          <a :href="word.link" class="link" target="_blank" :class="{ open: foundWords.includes(word.word) }">{{
      word.link }}</a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import CrosswordGrid from './components/CrosswordGrid.vue';

export default {
  name: 'App',
  components: {
    CrosswordGrid
  },
  data() {
    return {
      loading: true,
      puzzleData: {
        size: 15,
        words: []
      },
      foundWords: []
    };
  },
  async mounted() {
    await this.generatePuzzle();
  },
  methods: {
    async generatePuzzle() {
      try {
        const response = await axios.post('http://localhost:3000/generate', {
          rssUrl: 'https://www.nu.nl/rss/Algemeen'
        });
        console.log(response.data);
        this.puzzleData.words = response.data.reply.words;
        this.loading = false;
      } catch (error) {
        console.error('Error getting data:', error);
      }
    },
    handleWordFound(word) {
      if (!this.foundWords.includes(word)) {
        this.foundWords.push(word);
      }
    },
  },
};
</script>

<style lang="scss">
body {
  background: #EABA5B;
}

#app {
  font-family: 'Poppins';
  text-align: center;
  margin-top: 25px;
  display: flex;
  flex-direction: row;
  align-items: flex;
  justify-content: center;
  background: none;
  gap: 50px;
  width: 100%;
  padding-right: 50px;
  padding-left: 50px;

  .generating {
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.781);
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 3rem;
  }

  .hints {
    min-width: 60%;
    height: 100%;
    color: #3d3d3d;
    font-weight: 500;

    ul {
      text-align: left;
      list-style: none;

      li {
        margin-bottom: 15px;

        a {
          font-size: 12px;
          opacity: 0;

          &.open {
            opacity: 1;
            transition: all 250ms ease;
          }
        }
      }
    }
  }
}

.found {
  text-decoration: line-through;
  font-weight: bold;
}

.hints {
  margin-top: 20px;
}
</style>
