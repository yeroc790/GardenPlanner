<template>
  <v-container>
    <!-- Setup -->
    <v-layout row>
      <v-flex xs12>
        <v-row>
          <v-col cols="4" md="2" lg="1">
            <v-text-field
              v-model="input.gridRows"
              label="Rows"
              type="number"
              min="2"
              max="12"
              :error="errorMsg.gridRows != ''"
              :error-messages="errorMsg.gridRows"
            />
          </v-col>
          <v-col cols="4" md="2" lg="1">
            <v-text-field
              v-model="input.gridCols"
              label="Columns"
              type="number"
              min="2"
              max="6"
              :error="errorMsg.gridCols != ''"
              :error-messages="errorMsg.gridCols"
            />
          </v-col>
          <v-col cols="4" md="2" lg="1">
            <v-btn color="primary" @click="buildGrid">Build</v-btn>
          </v-col>
          <v-col cols="4" md="2" lg="1">
            <v-btn color="error" @click="clearGrid">Clear</v-btn>
          </v-col>
        </v-row>
      </v-flex>
    </v-layout>

    <!-- Plants -->
    <v-layout wrap mb-5>
      <v-flex xs12>
        <span class="title">Plants</span>
      </v-flex>
      <draggable
        v-model="plants"
        :group="{ name: 'plants', pull: 'clone', put: false }"
        @start="start"
        @end="end"
      >
        <v-chip
          v-for="plant in plants"
          :key="plant.name"
          color="primary"
          class="mr-3 mb-3 plant"
        >
          {{ plant.name }}
        </v-chip>
      </draggable>
    </v-layout>

    <!-- Grid -->
    <div class="grid">
      <v-row v-for="row in gridRows" :key="row">
        <v-col v-for="col in gridCols" :key="col" cols="2">
          <div v-if="loaded">
            <v-card
              class="card"
              justify-content="center"
              align-content="center"
              :disabled="
                gridList['list-' + row + '-' + col].length == 0 &&
                  dragging == false
              "
            >
              <v-card-actions
                v-if="gridList['list-' + row + '-' + col].length > 0"
              >
                <a class="clear" @click="deleteElement(row, col)">X</a>
              </v-card-actions>
              <draggable
                v-model="gridList['list-' + row + '-' + col]"
                group="plants"
                @add="dropElement(row, col)"
                @remove="deleteElement(row, col)"
                @start="start"
                @end="end"
              >
                <v-card-text
                  v-if="gridList['list-' + row + '-' + col].length == 0"
                  :id="'card-' + row + '-' + col"
                >
                  <p>Empty</p>
                </v-card-text>
                <v-card-text
                  v-if="gridList['list-' + row + '-' + col].length > 0"
                  :id="'card-' + row + '-' + col"
                >
                  <p>
                    {{
                      gridList['list-' + row + '-' + col][0].name
                        .charAt(0)
                        .toUpperCase() +
                        gridList['list-' + row + '-' + col][0].name.slice(1)
                    }}
                  </p>
                </v-card-text>
              </draggable>
            </v-card>
          </div>
        </v-col>
      </v-row>
    </div>
  </v-container>
</template>

<script lang="ts">
import Vue from 'vue'
import draggable from 'vuedraggable'

export default Vue.extend({
  name: 'Planner',
  components: {
    draggable
  },
  data: () => ({
    loaded: false,
    dragging: false,
    errorMsg: {
      gridRows: '',
      gridCols: ''
    },
    input: {
      gridRows: '2',
      gridCols: '2'
    },
    gridRows: 2,
    gridCols: 2,
    // remove instances of: coriander, parsley, rue, eggplant, lettuce (no enemies, neutral plant),
    // remove cont: oregano (neutral), brussel sprout, bush bean (neutral)
    // remove cont: celery (neutral), chard (neutral), rosemary,
    // remove cont: kohlrabi, charlock, mustard, pole bean, nasturtium (neutral), sage, mint, hyssop
    // remove cont: sunflower (neutral), kale, chive, spinach (neutral),
    // remove cont: cauliflower, cantaloupe, watermelon, raspberry, chamomile
    plants: [
      {
        name: 'asparagus',
        friends: ['bean', 'basil', 'carrot', 'dill', 'marigold', 'tomato'],
        enemies: ['broccoli', 'garlic', 'onion', 'potato'],
        bonus: ['marigold', 'tomato']
      },
      {
        name: 'basil',
        friends: [
          'asparagus',
          'bean',
          'beet',
          'broccoli',
          'cabbage',
          'marigold',
          'pepper',
          'potato',
          'tomato'
        ],
        enemies: [],
        bonus: ['tomato']
      },
      {
        name: 'bean',
        friends: [
          'asparagus',
          'basil',
          'beet',
          'cabbage',
          'carrot',
          'corn',
          'cucumber',
          'pea',
          'potato',
          'pumpkin',
          'radish',
          'squash',
          'zucchini'
        ],
        enemies: ['garlic', 'onion'],
        bonus: ['corn']
      },
      {
        name: 'beet',
        friends: ['basil', 'bean', 'broccoli', 'cabbage', 'onion', 'radish'],
        enemies: []
      },
      {
        name: 'broccoli',
        friends: [
          'basil',
          'beet',
          'carrot',
          'cucumber',
          'dill',
          'garlic',
          'marigold',
          'onion',
          'radish'
        ],
        enemies: [
          'asparagus',
          'corn',
          'pepper',
          'potato',
          'pumpkin',
          'strawberry'
        ]
      },
      {
        name: 'cabbage',
        friends: ['basil', 'bean', 'beet', 'onion', 'radish'],
        enemies: ['potato', 'strawberry', 'tomato']
      },
      {
        name: 'carrot',
        friends: [
          'asparagus',
          'bean',
          'broccoli',
          'onion',
          'pea',
          'pepper',
          'radish',
          'tomato'
        ],
        enemies: ['dill', 'potato']
      },
      {
        name: 'corn',
        friends: [
          'bean',
          'cucumber',
          'pea',
          'potato',
          'pumpkin',
          'squash',
          'zucchini'
        ],
        enemies: ['broccoli', 'tomato'],
        bonus: ['bean', 'pea']
      },
      {
        name: 'cucumber',
        friends: ['bean', 'broccoli', 'corn', 'radish'],
        enemies: ['potato']
      },
      {
        name: 'dill',
        friends: ['asparagus', 'broccoli', 'squash', 'zucchini'],
        enemies: ['carrot', 'tomato']
      },
      {
        name: 'garlic',
        friends: ['broccoli', 'potato', 'zucchini'],
        enemies: ['asparagus', 'bean']
      },
      {
        name: 'marigold',
        friends: [
          'asparagus',
          'basil',
          'broccoli',
          'potato',
          'pumpkin',
          'squash',
          'zucchini'
        ],
        enemies: [],
        bonus: ['asparagus']
      },
      {
        name: 'onion',
        friends: [
          'beet',
          'broccoli',
          'cabbage',
          'carrot',
          'pepper',
          'potato',
          'strawberry',
          'tomato'
        ],
        enemies: ['asparagus', 'bean', 'pea']
      },
      {
        name: 'pea',
        friends: ['bean', 'carrot', 'corn', 'potato', 'squash', 'zucchini'],
        enemies: ['onion'],
        bonus: ['corn']
      },
      {
        name: 'pepper',
        friends: ['basil', 'carrot', 'onion', 'tomato'],
        enemies: ['broccoli', 'potato', 'strawberry']
      },
      {
        name: 'potato',
        friends: [
          'basil',
          'bean',
          'corn',
          'garlic',
          'marigold',
          'onion',
          'pea',
          'radish'
        ],
        enemies: [
          'asparagus',
          'broccoli',
          'cabbage',
          'carrot',
          'cucumber',
          'pepper',
          'pumpkin',
          'squash',
          'strawberry',
          'tomato',
          'zucchini'
        ]
      },
      {
        name: 'pumpkin',
        friends: ['bean', 'corn', 'marigold', 'squash'],
        enemies: ['broccoli', 'potato', 'zucchini']
      },
      {
        name: 'radish',
        friends: [
          'bean',
          'beet',
          'broccoli',
          'cabbage',
          'carrot',
          'cucumber',
          'potato',
          'squash',
          'zucchini'
        ],
        enemies: []
      },
      {
        name: 'squash',
        friends: [
          'bean',
          'corn',
          'dill',
          'marigold',
          'pea',
          'pumpkin',
          'radish',
          'strawberry'
        ],
        enemies: ['potato']
      },
      {
        name: 'strawberry',
        friends: ['onion', 'squash'],
        enemies: ['broccoli', 'cabbage', 'pepper', 'potato', 'tomato']
      },
      {
        name: 'tomato',
        friends: ['asparagus', 'basil', 'carrot', 'onion', 'pepper'],
        enemies: ['cabbage', 'corn', 'dill', 'potato', 'strawberry'],
        bonus: ['asparagus', 'basil']
      },
      {
        name: 'zucchini',
        friends: [
          'bean',
          'corn',
          'dill',
          'garlic',
          'marigold',
          'pea',
          'radish'
        ],
        enemies: ['potato', 'pumpkin']
      }
    ],
    activePlant: '',
    gridList: {},
    trashList: []
  }),
  watch: {
    'input.gridRows'(x) {
      if (x < 2) {
        this.errorMsg.gridRows = 'Number must be greater than 2'
        return
      } else if (x > 12) {
        this.errorMsg.gridRows = 'Number must be less than 12'
        return
      }
      this.errorMsg.gridRows = ''
    },
    'input.gridCols'(x) {
      if (x < 2) {
        this.errorMsg.gridCols = 'Number must be greater than 2'
        return
      } else if (x > 6) {
        this.errorMsg.gridCols = 'Number must be less than 6'
        return
      }
      this.errorMsg.gridCols = ''
    }
  },
  mounted() {
    this.buildGrid()
    setTimeout(() => {
      this.loaded = true
    }, 500)
  },
  methods: {
    buildGrid() {
      this.gridRows = parseInt(this.input.gridRows)
      this.gridCols = parseInt(this.input.gridCols)

      for (let i = 1; i <= this.gridRows; i++) {
        for (let j = 1; j <= this.gridCols; j++) {
          this.gridList['list-' + i + '-' + j] = []
        }
      }
    },
    refreshScreen() {
      this.loaded = false
      this.loaded = true
    },
    clearGrid() {
      for (let i = 1; i <= this.gridRows; i++) {
        for (let j = 1; j <= this.gridCols; j++) {
          this.gridList['list-' + i + '-' + j] = []
        }
      }
      this.refreshScreen()
    },
    dropElement(row, col) {
      let plant = this.getPlant()
      if (!plant) {
        console.error('could not find plant')
        return
      }

      // replace array
      this.gridList['list-' + row + '-' + col] = [plant]

      this.refreshScreen()
      this.activePlant = ''
    },
    deleteElement(row, col) {
      this.gridList['list-' + row + '-' + col] = []
      this.refreshScreen()
    },
    start(evt) {
      this.dragging = true
      this.activePlant = evt.item.innerText.toLowerCase().trim()
      this.findFriends()
      this.findEnemies()
      this.findBonus()
    },
    end() {
      this.dragging = false
      this.activePlant = ''
      this.clearClasses()
    },
    getPlant() {
      return this.plants.find(plant => plant.name == this.activePlant)
    },
    findFriends() {
      // get object of current plant
      let plant = this.getPlant()
      if (!plant) {
        console.error('could not find plant')
        return
      }

      // get list of friends
      let friends = plant.friends
      if (!friends) return

      // check each cell for friends
      for (let row = 1; row <= this.gridRows; row++) {
        for (let col = 1; col <= this.gridCols; col++) {
          // skip if cell is empty
          if (this.gridList['list-' + row + '-' + col][0] == undefined) continue

          // name of plant in current cell
          let gridPlant = this.gridList['list-' + row + '-' + col][0].name

          // see if plant in current cell is friend of active plant
          friends.forEach(friend => {
            if (friend == gridPlant) {
              console.log(gridPlant + ' is a friend of ' + plant.name)
              this.highlightFriends(row, col)
            }
          })
        }
      }

      return
    },
    findEnemies() {
      // get object of current plant
      let plant = this.getPlant()
      if (!plant) {
        console.error('could not find plant')
        return
      }

      // get list of enemies
      let enemies = plant.enemies
      if (!enemies) return

      // check each cell for enemies
      for (let row = 1; row <= this.gridRows; row++) {
        for (let col = 1; col <= this.gridCols; col++) {
          // skip if cell is empty
          if (this.gridList['list-' + row + '-' + col][0] == undefined) continue

          // name of plant in current cell
          let gridPlant = this.gridList['list-' + row + '-' + col][0].name

          // see if plant in current cell is enemy of active plant
          enemies.forEach(enemy => {
            if (enemy == gridPlant) {
              console.log(gridPlant + ' is a enemy of ' + plant.name)
              this.highlightEnemies(row, col)
            }
          })
        }
      }

      return
    },
    findBonus() {
      // get object of current plant
      let plant = this.getPlant()
      if (!plant) {
        console.error('could not find plant')
        return
      }

      // get list of bonus plants
      let bonus = plant.bonus
      if (!bonus) return

      // check each cell for bonus plant
      for (let row = 1; row <= this.gridRows; row++) {
        for (let col = 1; col <= this.gridCols; col++) {
          // skip if cell is empty
          if (this.gridList['list-' + row + '-' + col][0] == undefined) continue

          // name of plant in current cell
          let gridPlant = this.gridList['list-' + row + '-' + col][0].name

          // see if plant in current cell is bonus for active plant
          bonus.forEach(bonus => {
            if (bonus == gridPlant) {
              console.log(gridPlant + ' is a bonus for ' + plant.name)
              this.highlightBonus(row, col)
            }
          })
        }
      }

      return
    },
    highlightFriends(row, col) {
      // highlight adjacent cells in row
      if (row != 1) {
        let above = document.getElementById('card-' + (row - 1) + '-' + col)
        above.classList.add('friend')
      }
      if (row != this.gridRows) {
        let below = document.getElementById('card-' + (row + 1) + '-' + col)
        below.classList.add('friend')
      }

      // highlight adjacent cells in column
      if (col != 1) {
        let left = document.getElementById('card-' + row + '-' + (col - 1))
        left.classList.add('friend')
      }
      if (col != this.gridCols) {
        let right = document.getElementById('card-' + row + '-' + (col + 1))
        right.classList.add('friend')
      }
      return
    },
    highlightEnemies(row, col) {
      // highlight adjacent cells in row
      if (row != 1) {
        let above = document.getElementById('card-' + (row - 1) + '-' + col)
        above.classList.add('enemy')
      }
      if (row != this.gridRows) {
        let below = document.getElementById('card-' + (row + 1) + '-' + col)
        below.classList.add('enemy')
      }

      // highlight adjacent cells in column
      if (col != 1) {
        let left = document.getElementById('card-' + row + '-' + (col - 1))
        left.classList.add('enemy')
      }
      if (col != this.gridCols) {
        let right = document.getElementById('card-' + row + '-' + (col + 1))
        right.classList.add('enemy')
      }
      return
    },
    highlightBonus(row, col) {
      // highlight adjacent cells in row
      if (row != 1) {
        let above = document.getElementById('card-' + (row - 1) + '-' + col)
        above.classList.add('bonus')
      }
      if (row != this.gridRows) {
        let below = document.getElementById('card-' + (row + 1) + '-' + col)
        below.classList.add('bonus')
      }

      // highlight adjacent cells in column
      if (col != 1) {
        let left = document.getElementById('card-' + row + '-' + (col - 1))
        left.classList.add('bonus')
      }
      if (col != this.gridCols) {
        let right = document.getElementById('card-' + row + '-' + (col + 1))
        right.classList.add('bonus')
      }
      return
    },
    clearClasses() {
      // offload all matches into temp array
      // (deleting directly from friends won't delete all)
      let arr = []
      let friends = document.getElementsByClassName('friend')
      for (let i = 0; i < friends.length; i++) {
        arr.push(friends[i])
      }

      let enemies = document.getElementsByClassName('enemy')
      for (let i = 0; i < enemies.length; i++) {
        arr.push(enemies[i])
      }

      let bonus = document.getElementsByClassName('bonus')
      for (let i = 0; i < bonus.length; i++) {
        arr.push(bonus[i])
      }

      if (arr.length == 0) return

      // remove class from each element in arr
      arr.forEach(x => {
        x.classList.remove('friend')
        x.classList.remove('enemy')
        x.classList.remove('bonus')
      })
    }
  }
})
</script>
<style lang="stylus">
primary = #1976D2
error = #FF5252
success = #4CAF50

.plant:hover {
  cursor: pointer
}
.cell {
  padding: 40px 20px;
  text-align: center;
  border: 1px solid black;
}
.cell.empty {
  color: grey;
  border: 1px dashed grey;
}
.cell p:hover {
  cursor: pointer;
}
.grid .row > div {
  width: 100%;
}
.grid .col {
  padding: 12px 4px;
}

.card {
  text-align: center;
  padding: 40px 20px;

  .v-card__text {
    font-size: 20px;
  }
  .v-card__text:hover {
    cursor: pointer;
  }
  .v-card__actions {
    position: absolute;
    top: 0;
    right: 0;

    .clear {
      color: error;
      font-size: 20px;
      font-weight: bold;
    }
  }
  .v-chip {
    display: none;
  }
  .sortable-ghost {
    display: none;
  }
}
.friend {
  color: primary;
}
.enemy {
  color: error;
}
.bonus {
  color: success !important;
}
</style>
