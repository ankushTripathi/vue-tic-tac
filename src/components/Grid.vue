<template>
 <div>
    <div class="gameStatus" :class="gameStatusColor">
        {{ gameStatusMessage }}
    </div>
    <table class="grid">
        <tr>
        <Cell name="1"></Cell>
        <Cell name="2"></Cell>
        <Cell name="3"></Cell>
        </tr>
        <tr>
        <Cell name="4"></Cell>
        <Cell name="5"></Cell>
        <Cell name="6"></Cell>
        </tr>
        <tr>
        <Cell name="7"></Cell>
        <Cell name="8"></Cell>
        <Cell name="9"></Cell>
        </tr>
    </table>
 </div>
</template>

<script>
import Cell from './Cell.vue'
export default {
    components : { Cell },
    data() {
        return {
            activePlayer : 'O',
            gameStatus : 'turn',
            gameStatusMessage : `O's turn`,
            gameStatusColor : 'statusColor',
            moves : 0,
            cells : {
                1:'' , 2: '', 3: '',
                4: '', 5: '', 6: '',
                7: '', 8: '', 9: ''
            },
            winConditions : [
                [1,2,3], [4,5,6], [7,8,9],
                [1,4,7], [2,5,8], [3,6,9],
                [1,5,9], [3,5,7]
            ],
        }
    },
        created () {
        Event.$on('place',(cellNumber)=>{
            this.cells[cellNumber] = this.activePlayer
            this.changePlayer()
            this.moves++
            this.changeGameStatus()
        })

        Event.$on('gridReset',() => 
          Object.assign(this.$data, this.$options.data())
        )
    },
    computed : {
        nonActivePlayer(){
            if(this.activePlayer === 'O'){
                return 'X'
            }
            else
                return 'O'
        }
    },
    methods : {
        changePlayer(){
            this.activePlayer = this.nonActivePlayer
        },
        changeGameStatus(){
            if(this.checkWin()){
                this.gameStatusColor = 'statusWin'
                this.gameStatusMessage = `${this.nonActivePlayer} Wins !`
                console.log(this.gameStatusMessage)
                Event.$emit('win', this.nonActivePlayer)
                Event.$emit('freeze')

                return 
            }else if(this.moves === 9){
                this.gameStatusColor = 'statusDraw'
                this.gameStatusMessage = 'Draw !'

                return
            }else{
                this.gameStatusMessage = `${this.activePlayer}'s turn`
                this.gameStatusColor = 'statusColor'

                return
            }
        },
        areEqual () {
            var len = arguments.length;

            for (var i = 1; i < len; i++){
                if (arguments[i] === '' || arguments[i] !== arguments[i-1])
                    return false;
            }
            return true;
        },
        checkWin () {
            for (let i = 0; i < this.winConditions.length; i++) {
                let wc = this.winConditions[i]
                let cells = this.cells

                if (this.areEqual(cells[wc[0]], cells[wc[1]], cells[wc[2]])) {
                    return true
                }
            }

            return false
        },
    }
}
</script>

<style>
  .grid {
  background-color: #34495e;
  color: #fff;
  width: 100%;
  border-collapse: collapse;
}

.gameStatus {
  margin: 0px;
  padding: 15px;
  border-top-left-radius: 20px;
  border-top-right-radius: 20px;
  background-color: #f1c40f;
  color: #fff;    
  font-size: 1.4em;
  font-weight: bold;
}

.statusTurn {
    background-color: #f1c40f;
}

.statusWin {
    background-color: #2ecc71;
}

.statusDraw {
    background-color: #9b59b6;
}
</style>
