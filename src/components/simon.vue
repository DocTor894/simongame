<template>
  <div class="simon">
    <div class="circle">
      <svg
        width="354"
        height="354"
        viewBox="0 0 354 354"
        fill="none"
      >
        <g filter="url(#filter0_d)">
          <circle cx="177" cy="177" r="150" fill="white" />
        </g>
        <g class="block" v-for="(tile, index) in tiles" :key="index">
          <path
            :d="tile.tileD"
            class="tile"
            ref="tiles"
            :fill="tile.fill"
            :class="{ hoverable: clicked }"
            @click="clicked ? registerClick(index) : ''"
            @mousedown="clicked ? tileDown(index) : ''"
            @mouseup="clicked ? tileUp(index) : ''"
          />
          <path
            :d="tile.borderD"
            class="border"
            ref="borders"
            stroke="#505050"
            stroke-width="2"
          />
        </g>
        <defs>
          <filter
            id="filter0_d"
            x="0"
            y="0"
            width="354"
            height="354"
            filterUnits="userSpaceOnUse"
            color-interpolation-filters="sRGB"
          >
            <feFlood flood-opacity="0" result="BackgroundImageFix" />
            <feColorMatrix
              in="SourceAlpha"
              type="matrix"
              values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 127 0"
            />
            <feOffset />
            <feGaussianBlur stdDeviation="13.5" />
            <feColorMatrix type="matrix" values="0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0.25 0" />
            <feBlend mode="normal" in2="BackgroundImageFix" result="effect1_dropShadow" />
            <feBlend mode="normal" in="SourceGraphic" in2="effect1_dropShadow" result="shape" />
          </filter>
        </defs>
      </svg>
    </div>
    <div class="menu">
      <h3>Раунд: {{ this.round }}</h3>
      <span class="btnStart" @click="start()">Старт</span>
      <p class="lose" ref="lose">Вы проиграли. Пройдено {{ this.round }} раундов</p>
      <p class="title">Уровень сложности:</p>
      <div class="difficult">
        <p><input type="radio" name="dif" id="easy" value="1500" v-model="difficulty"><label for="easy">Легкий</label></p>
        <p><input type="radio" name="dif" id="medium" value="1000" v-model="difficulty"><label for="medium">Средний</label></p>
        <p><input type="radio" name="dif" id="hard" value="400" v-model="difficulty"><label for="hard">Тяжелый</label></p>
      </div> 
    </div>
  </div>
</template>

<script>
export default {
  data: () => ({
    round: 0,
    difficulty: 400,
    roundSequence: [],
    playerSequence: [],
    copy: [],
    active: Boolean,
    clicked: Boolean,
    tiles: [
      {
        tileD: "M317 177.5C317 159.049 313.366 140.779 306.305 123.733C299.244 106.687 288.895 91.1981 275.849 78.1515C262.802 65.1049 247.313 54.7557 230.267 47.6949C213.221 40.6341 194.951 37 176.5 37L176.5 177.5H317Z",
        fill: "#FEF584",
        borderD: "M177 37C195.385 37 213.59 40.6212 230.576 47.6569C247.561 54.6925 262.995 65.0048 275.995 78.005C288.995 91.0053 299.307 106.439 306.343 123.424C313.379 140.41 317 158.615 317 177",
      },
      {
        tileD: "M36 177.5C36 195.951 39.6341 214.221 46.6949 231.267C53.7557 248.313 64.1049 263.802 77.1515 276.849C90.1981 289.895 105.687 300.244 122.733 307.305C139.779 314.366 158.049 318 176.5 318L176.5 177.5L36 177.5Z",
        fill: "#FF998D",
        borderD: "M176 318C157.615 318 139.41 314.379 122.424 307.343C105.439 300.307 90.0053 289.995 77.0051 276.995C64.0049 263.995 53.6925 248.561 46.6569 231.576C39.6212 214.59 36 196.385 36 178",
      },
      {
        tileD: "M176.5 37C158.049 37 139.779 40.6341 122.733 47.6949C105.687 54.7557 90.1981 65.1049 77.1515 78.1515C64.1049 91.1981 53.7557 106.687 46.6949 123.733C39.6341 140.779 36 159.049 36 177.5L176.5 177.5L176.5 37Z",
        fill: "#78BCFF",
        borderD: "M36 177C36 158.615 39.6212 140.41 46.6569 123.424C53.6925 106.439 64.0049 91.0052 77.0051 78.005C90.0053 65.0048 105.439 54.6925 122.424 47.6568C139.41 40.6212 157.615 37 176 37",
      },
      {
        tileD: "M176.5 318C194.951 318 213.221 314.366 230.267 307.305C247.313 300.244 262.802 289.895 275.848 276.849C288.895 263.802 299.244 248.313 306.305 231.267C313.366 214.221 317 195.951 317 177.5L176.5 177.5L176.5 318Z",
        fill: "#D8EB72",
        borderD: "M317 178C317 196.385 313.379 214.59 306.343 231.576C299.307 248.561 288.995 263.995 275.995 276.995C262.995 289.995 247.561 300.307 230.576 307.343C213.59 314.379 195.385 318 177 318",
      },
    ]
  }),
  methods: {
    randNumber() {
      return Math.floor(Math.random() * 4);
    },
    start() {
      this.round = 0;
      this.$refs.lose.style.display = 'none';
      this.roundSequence = [];
      this.playerSequence = [];
      this.active = true;
      this.newRound();
    },
    newRound() {
      this.clicked = false;
      this.round += 1;
      this.roundSequence.push(this.randNumber());
      this.copy = this.roundSequence.slice(0);
      this.animate();
    },
    animate() {
      let i = 0;
			var interval = setInterval(() => {
				this.playSound(this.roundSequence[i]);
				this.lightUp(this.roundSequence[i]);

        i++;
				if (i >= this.roundSequence.length) {
          clearInterval(interval);
					this.clicked = true;
				}
			}, this.difficulty);
    },
    playSound(elm) {

    },
    lightUp(elm) {
      this.$refs.tiles[elm].style.opacity = '1';
      setTimeout(() => {
        this.$refs.tiles[elm].style.opacity = '0.6';
      }, 150)
    },
    registerClick(elm) {
      this.copy.shift() === elm ? this.active = true : this.active = false;
			this.checkLose();
    },
    tileDown(elm) {
      this.playSound(elm);
      this.$refs.tiles[elm].style.opacity = '1';
    },
    tileUp(elm) {
      this.$refs.tiles[elm].style.opacity = '0.6';
    },
    deactivateBoard() {
      this.clicked = false;
    },
    checkLose() {
      if (this.copy.length === 0 && this.active) {
        console.log('nR');
				this.deactivateBoard();
				this.newRound();
			} else if (!this.active) {
				this.deactivateBoard();
				this.endGame();
			}
    },
    endGame() {
      this.$refs.lose.style.display = 'block';
      this.raund = 0;
    }
  },
};
</script>

<style>
.simon {
  width: 700px;
  margin: auto;
  display: grid;
  grid-template-columns: 354px 1fr;
  grid-gap: 20px;
}

.tile {
  opacity: 0.6;
}

.border {
  display: none;
}

.lose {
  display: none;
}

.hoverable {
  cursor: pointer;
}

.btnStart {
  padding: 10px 20px;
  background: rgb(250, 188, 55);
  border: 1px solid rgb(250, 188, 55);
  border-radius: 5px;
  color: #FFFFFF
}

.btnStart:hover {
  cursor: pointer;
  background: #FFFFFF;
  border-color: rgb(250, 188, 55);
  color: rgb(250, 188, 55);
}
</style>