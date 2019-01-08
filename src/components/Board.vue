<template>
  <div class="l-slide-puzzle">
    <div class="c-board">
      <!-- Content with tiles -->
      <Tile v-for="tile in tiles"
            :key="tile.position"
            :tile="tile"
            ref="tiles"
            :playing="playing"
            @move="moveTile"/>
    </div>

    <div v-if="!playing">
      <a href="#" @click="shuffleTiles()" class="c-button"> Play again! </a>
    </div>
  </div>
</template>

<script>
import Tile from './Tile.vue';

export default {
  name: 'Board',
  props: {
    image: null,
  },
  data() {
    return {
      playing: true,
      tiles: [],
      tileSize: {
        width: 25,
        height: 25,
      },
    };
  },
  watch: {
    tiles: {
      deep: true,
      // eslint-disable-next-line func-names
      handler(newValue) {
        let completed = true;
        newValue.some((tile, index) => {
          if (tile.position !== index) {
            completed = false;
            return tile;
          }
        });

        this.playing = !completed;
      },
    },
  },
  mounted() {
    // Generate tiles
    this.generateTiles(16);
  },
  methods: {
    /**
     * Generate the tiles for the current game.
     *
     * @param totalTiles
     * @return void
     */
    generateTiles(totalTiles) {
      this.tiles = [];
      for (let i = 0; i < totalTiles; i += 1) {
        // Push the tiles into the array
        this.tiles.push({
          position: i,
          isEmpty: i === (totalTiles - 1),
        });
      }

      // Randomize the array order, so we have a nice starting grid
      // this.shuffleTiles();
    },
    /**
     * Shuffle all puzzle pieces
     *
     * @return void
     */
    shuffleTiles() {
      this.tiles.sort(() => 0.5 - Math.random());
    },
    /**
     * Move the tile, if there is a empty spot around it..
     *
     * @param object
     * @return void|Tile
     */
    moveTile(object) {
      if (!object.tile.isEmpty) {
        // Check if there is an empty spot around the clicked tile
        const emptyTilePosition = this.hasLinkToEmptyTile(object.tile.position);
        // Check if the empty tile was around the clicked tile
        if (emptyTilePosition >= 0) {
          // Get object position in array
          const clickedTileIndex = this.tiles.findIndex(
            tile => tile.position === object.tile.position,
          );
          // Get empty tile
          const emptyTile = this.tiles.find(tile => tile.isEmpty);

          // Map the array with tiles to new format, so vue.js will do it's recursive magic
          this.tiles = this.tiles.map((tile, index) => {
            if (index === clickedTileIndex) {
              return {
                isEmpty: true,
                position: emptyTile.position,
              };
            } if (index === emptyTilePosition) {
              return {
                isEmpty: false,
                position: object.tile.position,
              };
            }

            return tile;
          });
        }
      }
    },
    /**
     * Check if there is a empty tile around the clicked tile
     * @param position
     * @return Number
     */
    hasLinkToEmptyTile(position) {
      const clickedTileIndex = this.tiles.findIndex(tile => tile.position === position);
      const emptyTileIndex = this.tiles.findIndex(tile => tile.isEmpty);
      const perRow = 4;
      const iterationStep = 1;

      // Set some moving restrictions
      if (
        (
          // when on the last position of the row,
          // disable the option to click the first tile on the next row
          clickedTileIndex - iterationStep === emptyTileIndex
          && clickedTileIndex % perRow !== 0
        ) || (
          // when on the first position of the row,
          // disable the option to click the last tile on the previous row
          clickedTileIndex + iterationStep === emptyTileIndex
          && clickedTileIndex % perRow !== 3
        )
        || clickedTileIndex - perRow === emptyTileIndex
        || clickedTileIndex + perRow === emptyTileIndex) {
        // return the position of the empty tile..
        return emptyTileIndex;
      }

      return -1; // falsy state, because there is no empty tile around the clicked tile..
    },
  },
  components: {
    Tile,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .l-slide-puzzle {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .c-board {
    width: 60vh;
    height: 60vh;
    border: 1px solid white;
    flex-wrap: wrap;
    display: flex;
    justify-content: flex-start;
    align-items: center;
  }

  .c-button {
    text-decoration: none;
    color: white;
    font-weight: 600;
    padding: 8px 15px;
  }
</style>
