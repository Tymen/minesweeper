<template>
  <div class="home">
    <h1>Minesweeper</h1>
    <label for="bombs">Bombs</label>
    <input name="bombs" id="bombs" :value="bombCount"><br>
    <label for="rows">Rows</label>
    <input name="rows" id="rows" :value="rows"><br>
    <label for="collumns">collumns</label>
    <input name="collumns" id="collumns" :value="collumns"><br>
    <button @click="generateField">Generate</button>
    <div @contextmenu="disableRClick($event)" class="container">
        <div v-for="row in rows" :key="row" :id="`y-${row - 1}`" class="row">
            <div v-for="col in collumns" :key="col" :id="`${row - 1},${col -1}`" @contextmenu="markBomb($event)" v-on:click="clickTile($event)" class="hidden"></div>
        </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
export default {
    name: 'Minesweeper',
    data () {
      return {
          bombCount: 10,
          bombLocations: [],
          rows: 6,
          collumns: 6,
          mapObject: {},
      }
    },
    components: {
    },
    methods : {
        markBomb(e) {
            //do stuff
            e.preventDefault();
            let element = document.getElementById(e.srcElement.id);
            element.classList.contains("flag") ? element.classList.remove("flag") : element.classList.add("flag");
        },
        clickTile(e){
            let el = document.getElementById(e.srcElement.id);
            if(this.bombLocations.includes(e.srcElement.id)){
                this.revealBombs()
                el.classList.remove("flag");
            }else {
                el.innerText = this.mapObject[e.srcElement.id]
                el.classList.add("revealed")
                this.checkForZero(e.srcElement.id)
            }
        },
        checkForZero(cords) {
            let tile = JSON.parse("[" + cords + "]");
            let zero = true;
            let zeroTiles = []
            while(zero){
                for(let i = 0; i < 8; i++){
                    tile = this.defaultAlgorithm(i, tile)
                    if(this.mapObject[`${tile[0]},${tile[1]}`] == 0 && !this.bombLocations.includes(`${tile[0]},${tile[1]}`)){
                        let getEl = document.getElementById(`${tile[0]},${tile[1]}`)
                        if (getEl.innerText == ""){
                            zeroTiles.push(`${tile[0]},${tile[1]}`)
                        }
                        getEl.innerText = this.mapObject[`${tile[0]},${tile[1]}`];
                        getEl.classList.add("revealed")
                    }
                }
                if (zeroTiles.length <= 0){
                    zero = false
                }else {
                    zeroTiles.splice(0,1);
                    tile = JSON.parse("[" + zeroTiles[0] + "]")
                }
            }
        },
        revealBombs() {
            for(let i = 0; i < this.bombLocations.length; i++){
                document.getElementById(this.bombLocations[i]).classList.add("bomb")
            }
        },
        defaultAlgorithm(i, tile) {
            switch(i){
                case 0:
                    tile[0] = tile[0] - 1;
                    break;
                case 1:
                    tile[1] = tile[1] + 1;
                    break;
                case 2:
                    tile[0] = tile[0] + 1;
                    break;
                case 3:
                    tile[0] = tile[0] + 1;
                    break;
                case 4:
                    tile[1] = tile[1] - 1;
                    break;
                case 5:
                    tile[1] = tile[1] - 1;
                    break;
                case 6: 
                    tile[0] = tile[0] - 1;
                    break;
                case 7:
                    tile[0] = tile[0] - 1;
                    break;
            }
            return tile;
        },
        checkBombsAround(cords) {
            let bombs = 0;
            let tile = JSON.parse("[" + cords + "]");
            for(let i = 0; i < 8; i++){
                tile = this.defaultAlgorithm(i, tile)
                if (this.bombLocations.includes(`${tile[0]},${tile[1]}`)){
                    bombs = bombs + 1;
                }
            }
            return bombs;
        },
        disableRClick(e) {
            e.preventDefault();
        },
        generateField() {
            if (document.getElementById("bombs").value && document.getElementById("rows").value && document.getElementById("collumns").value) {
                this.bombCount = parseInt(document.getElementById("bombs").value)
                this.rows = parseInt(document.getElementById("rows").value);
                this.collumns = parseInt(document.getElementById("collumns").value);
            }
            this.bombLocations = [];
            this.mapObject = {};
            this.setBombLocations();
            this.checkTiles();
        },
        checkTiles() {
            for(let y = 0; y < this.rows; y++){
                for(let x = 0; x < this.collumns; x++){
                    var el = document.getElementById(`${y},${x}`);
                    if (!el.innerText == ""){
                        el.innerText = "";
                    }
                    if (el.classList.contains("bomb") || el.classList.contains("flag") || el.classList.contains("revealed")){
                        el.classList.remove("bomb","flag","revealed")
                    }
                    this.mapObject[`${y},${x}`] = this.checkBombsAround(`${y},${x}`)
                }
            }
        },
        setBombLocations(){
            for(let i = 0; i < this.bombCount; i++){

            let cords = this.generateCords()
            let loop = false;
            this.bombLocations.includes(cords) ? loop = true : loop = false;

            while(loop) {
                cords = this.generateCords()
                this.bombLocations.includes(cords) ? loop = true : loop = false;
            }

            this.bombLocations.push(cords); 
        }
        },
        generateCords() {
            return `${Math.floor(Math.random() * this.rows)},${Math.floor(Math.random() * this.collumns)}`;
        }
    },
    mounted() {
        this.generateField();
    }
}
</script>
<style scoped>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}
.row {
    display: flex;
}
.hidden {
    background-color: gray;
    color: white;
    font-size: 20px;
    padding: 10px;
    width: 40px;
    height: 40px;
    margin: 3px;
    cursor: pointer;
    transition: .1s;
}
.hidden:hover {
    background-color: rgb(94, 94, 94);
}
.bomb {
    background-color: red;
}
.bomb:hover {
    background-color: red!important;
}
.flag {
    background-color: rgba(253, 0, 219, 0.596)!important;
}
.revealed {
    background-color: rgb(100, 100, 100);
}
</style>
