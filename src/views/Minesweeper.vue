<template>
  <div class="home">
    <h1>Minesweeper</h1>
    <button @click="generateField">Generate</button>
    <div @contextmenu="disableRClick($event)" class="container">
        <div id="y-0" class="row">
            <div id="0,0" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="0,1" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="0,2" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="0,3" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="0,4" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
        </div>
        <div id="y-1" class="row">
            <div id="1,0" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="1,1" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="1,2" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="1,3" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="1,4" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
        </div>
        <div id="y-2" class="row">
            <div id="2,0" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="2,1" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="2,2" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="2,3" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="2,4" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
        </div>
        <div id="y-3" class="row">
            <div id="3,0" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="3,1" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="3,2" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="3,3" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="3,4" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
        </div>
        <div id="y-4" class="row">
            <div id="4,0" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="4,1" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="4,2" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="4,3" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
            <div id="4,4" @contextmenu="markBomb($event)" v-on:click="check($event)" class="hidden"></div>
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
          bombCount: 0,
          bombLocations: [],
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
        check(e) {
            let bombs = 0;
            let tile = JSON.parse("[" + e.srcElement.id + "]");
            let element = document.getElementById(e.srcElement.id);
            if (this.bombLocations.includes(`${tile[0]},${tile[1]}`)){
                element.classList.add("bomb");
                element.classList.remove("flag");
            }
            for(let i = 0; i < 8; i++){
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
                console.log(`${tile[0]},${tile[1]}`);
                if (this.bombLocations.includes(`${tile[0]},${tile[1]}`)){
                    bombs = bombs + 1;
                    console.log(`Bomblocation: ${tile[0]},${tile[1]}`);
                }
            }
            console.log("there are" + bombs + "around")
            element.innerText = bombs;
            console.log("you clicked " + e.srcElement.id)
        },
        disableRClick(e) {
            e.preventDefault();
        },
        generateField() {
            console.log(this.bombCount);
        },
        generateCords() {
            return `${Math.floor(Math.random() * 5)},${Math.floor(Math.random() * 5)}`;
        }
    },
    created() {
        this.bombCount = 4;
        for(let i = 0; i < this.bombCount; i++){
            let cords = this.generateCords()

            let loop = false;
            this.bombLocations.includes(cords) ? loop = true : loop = false;
            while(loop) {
                cords = this.generateCords()
                this.bombLocations.includes(cords) ? loop = true : loop = false;
            }

            this.bombLocations.push(cords); 
            console.log(cords)
        }
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
    margin: 5px;
}
.bomb {
    background-color: red;
}
.flag {
    background-color: rgba(253, 0, 219, 0.596)!important;
}
</style>
