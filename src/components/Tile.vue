<template>
  <div class="c-tile"
       :class="{ 'c-tile--image' : !tile.isEmpty }"
       v-bind:style="styleObject"
       @click="move">
  </div>
</template>

<script>
import * as image from '../assets/monks.jpg';

export default {
  name: 'Tile',
  props: {
    id: Number,
    tile: Object,
    playing: Boolean,
  },
  data() {
    return {
      imageElement: new Image(),
      styleObject: {},
    };
  },
  methods: {
    /**
     * Calculate the background positions
     *
     * @param position
     * @param imagePath
     */
    calculateBackgroundImagePosition(position, imagePath) {
      // Set the source of the image
      this.imageElement.src = imagePath;

      // This is just for reference
      const self = this;

      // eslint-disable-next-line func-names
      this.imageElement.onload = function () {
        // Will run when image will be rendered
        // Amount of rows
        const perRow = 4;
        // Get the board, so we can calculate the dimensions
        const board = document.getElementsByClassName('c-board')[0];

        // Calculate the correct image positions
        const width = (board.clientWidth / perRow) * (self.tile.position % perRow);
        const height = (board.clientHeight / perRow) * Math.floor(self.tile.position / perRow);

        // Generate the styles, will be used as inline styles into DOM
        self.styleObject = {
          backgroundPosition: `-${width}px -${height}px`,
          backgroundImage: `url(${image})`,
          backgroundRepeat: 'no-repeat',
          backgroundSize: `${board.clientHeight}px ${board.clientWidth}px`,
        };
      };
    },
    /**
     * Move the tile
     */
    move() {
      if (this.playing) {
        this.$emit('move', {
          tile: this.tile,
        });
      }
    },
  },
  async mounted() {
    if (!this.tile.isEmpty) {
      this.calculateBackgroundImagePosition(this.id, image);
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .l-slide-puzzle {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .c-tile {
    width: calc(25% - 2px);
    height: calc(25% - 2px);
    &:before {
      content: "";
      padding-top: 100%;
    }
    border: 1px solid white;
    background-color: white;

    &--image {
      background-color: saddlebrown;
      background-size: 60vh 60vh;
    }
  }
</style>
