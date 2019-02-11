
<template>
<div>

<transition-group name="shuffleFast" tag="div" class="posanneau" @dragover.prevent="allowDrop()"  :key="componentKey" >
  <div v-for="tourdehanoi in tourdehanois" :key="tourdehanoi.id" :id="'div'+tourdehanoi.id" class="touredehanoi" >
 <anneau   :qAnneau="tourdehanoi.id" :aryoudragable=tourdehanoi.anneaudrable  v-if="tourdehanoi. anneaupresentegaltaillenonnul!==0" @selected="drop1($event)" ></anneau>
<div class="touredehanoi" v-else></div>
</div>
</transition-group>
<div v-for="n in 3" :class="'tower'+n" :key="n"></div>
<div class="nombredecoup">

  <h4>Nombre de coup</h4>
  <h1>{{coup}}</h1>

  </div>

<div class="action"><p>
<a href="https://fr.wikipedia.org/wiki/Tours_de_Hanoï" target="_blank" noopener noreferrer >Modèle d'une tour de Hanoï </a><br>

Les tours de Hanoï (originellement, la tour d'Hanoïa) sont un jeu de réflexion imaginé par le mathématicien français Édouard Lucas, et consistant à déplacer des disques de diamètres différents d'une tour de « départ » à une tour d'« arrivée » en passant par une tour « intermédiaire », et ceci en un minimum de coups, tout en respectant les règles suivantes : <br>

on ne peut déplacer plus d'un disque à la fois ;<br>
on ne peut placer un disque que sur un autre disque plus grand que lui ou sur un emplacement vide. <br>
On suppose que cette dernière règle est également respectée dans la configuration de départ.</p></div>
 <button class="but1" @click.prevent="deplace()">Solution Optimale</button>
  <button class="but2" @click.prevent="deplace_un_par_un()">Déplacer un par un (itératif) {{this.action}}</button>
</div>

</template>
<script>
import anneau from './anneau.vue'

export default {

  components: {
    anneau

  },
  data () {
    return {
      coup: 0,
      temp: '',
      index: 0,
      index1: 0,
      action: 'continuer',
      storepos: [],
      storeposprec: [],
      storeposWin: [],
      tourdehanois: [],
      show: true,
      componentKey: 0,
      timer: null,
      posi: [],
      id: 1,
      selectedrag: '',
      disqueprochain: 0,
      largeurAnneau: [50, 60, 70, 80, 90],
      posleftAnneau: [8.5, 7, 5, 3, 1.5],
      essai: true
    }
  },
  mounted () {
    this.storeposWin = [3, 6, 9, 12, 15]
    this.tourdehanois = []
    this.id = 0
    for (var i = 0; i < 15; i++) {
      let tourdehanoi = {
        id: i,
        anneaupresentegaltaillenonnul: 0,
        anneaudrable: ''

      }

      this.tourdehanois.push(tourdehanoi)
    }

    this.tourdehanois[0].anneaudrable = true
    this.tourdehanois[0].anneaupresentegaltaillenonnul = 1
    this.tourdehanois[0].id = 0
    for (i = 1; i < 5; i++) { this.tourdehanois[i].anneaudrable = false; this.tourdehanois[i].anneaupresentegaltaillenonnul = i + 1; this.tourdehanois[i].id = i }
    for (i = 5; i < 15; i++) { this.tourdehanois[i].anneaudrable = false; this.tourdehanois[i].anneaupresentegaltaillenonnul = 0; this.tourdehanois[i].id = i }
  },
  beforeDestroy () {
    clearInterval(this.timer)
  },
  methods: {

    forceRerender (n) {
      return 'tower' + n
    },

    numdiv (n1) {
      var indexint = n1
      /* this.index1=indexint; */
      return 'div' + indexint
    },
    allowDrop (ev) {
      ev.preventDefault()
    },
    storePosition () {
      if ((this.tourdehanois[10].anneaupresentegaltaillenonnul !== 0) &&
     (this.tourdehanois[11].anneaupresentegaltaillenonnul !== 0) &&
     (this.tourdehanois[12].anneaupresentegaltaillenonnul !== 0) &&
     (this.tourdehanois[13].anneaupresentegaltaillenonnul !== 0) &&
     (this.tourdehanois[14].anneaupresentegaltaillenonnul !== 0)) { return true } else { return false }
    },
    redesign () {
      var tranleft = ''
      for (var k = 0; k < 5; k++) {
        var targ = 'drag' + k

        document.getElementById(targ).style.top = ''

        tranleft = this.posleftAnneau[k] + 'vw'

        document.getElementById(targ).style.left = tranleft
      }
    },
    reaffecdragable1 () {
      var sianneau = true
      for (var k = 0; k < 5; k++) {
        if (this.tourdehanois[k].anneaupresentegaltaillenonnul !== 0) {
          this.tourdehanois[k].anneaudrable = sianneau
          sianneau = false
        } else {

        }
      }
      sianneau = true
      for (k = 5; k < 10; k++) {
        if (this.tourdehanois[k].anneaupresentegaltaillenonnul !== 0) {
          this.tourdehanois[k].anneaudrable = sianneau
          sianneau = false
        } else {

        }
      }
      sianneau = true
      for (k = 11; k < 15; k++) {
        if (this.tourdehanois[k].anneaupresentegaltaillenonnul !== 0) {
          this.tourdehanois[k].anneaudrable = sianneau
          sianneau = false
        } else {

        }
      }
    },
    tombe () {
      /* var targ1, targfirst, targfinal
      var test = false
      targfirst = targ.parentNode.parentNode.id
      var cibleDiv = Number(targfirst.match(/\d+/g).join('')) */

      for (var k = 0; k < 4; k++) {
        if ((this.tourdehanois[k].anneaudrable) && (this.tourdehanois[k + 1].anneaupresentegaltaillenonnul === 0)) {
          let temp = this.tourdehanois[k]
          this.$set(this.tourdehanois, k, this.tourdehanois[k + 1])
          this.$set(this.tourdehanois, k + 1, temp)
        }
      }
      for (k = 5; k < 9; k++) {
        if ((this.tourdehanois[k].anneaudrable) && (this.tourdehanois[k + 1].anneaupresentegaltaillenonnul === 0)) {
          let temp = this.tourdehanois[k]
          this.$set(this.tourdehanois, k, this.tourdehanois[k + 1])
          this.$set(this.tourdehanois, k + 1, temp)
        }
      }
      for (k = 10; k < 14; k++) {
        if ((this.tourdehanois[k].anneaudrable) && (this.tourdehanois[k + 1].anneaupresentegaltaillenonnul === 0)) {
          let temp = this.tourdehanois[k]
          this.$set(this.tourdehanois, k, this.tourdehanois[k + 1])
          this.$set(this.tourdehanois, k + 1, temp)
        }
      }

      this.reaffecdragable1()
    },
    testanneauLepluspetit (anneauDeplacer, colonneCible) {
      var max = 15
      var test1 = true
      if (colonneCible < 4) { max = 5 } else if (colonneCible < 10) { max = 10 } else { max = 15 }
      for (var k = colonneCible; k < max; k++) {
        if (this.tourdehanois[k].anneaupresentegaltaillenonnul !== 0) {
          if (this.tourdehanois[k].anneaupresentegaltaillenonnul < this.tourdehanois[anneauDeplacer].anneaupresentegaltaillenonnul) {
            k = max
            test1 = false
          } else {
            test1 = true
            k = max
          }
        }
      }
      return test1
    },

    drop1 (ev) {
      if (ev != null) {
        var elmnt = ev

        var data = ''

        document.onmouseup = null
        document.onmousemove = null
        if (ev.offsetLeft < document.body.clientWidth / 3) { data = 0 } else if (ev.offsetLeft < (2 * document.body.clientWidth) / 3) { data = 5 } else { data = 10 }

        if (elmnt !== '') {
          var cibleDiv = Number(elmnt.id.match(/\d+/g).join(''))
          var chgtdisque = 0
          for (var j = 0; j < 15; j++) { if (this.tourdehanois[j].id === cibleDiv) { chgtdisque = j } }
          if (this.deplace1(chgtdisque, data)) {}
        }
      }
      this.redesign()
      this.reaffecdragable1()
    },
    deplace_un_par_un () {
      var test = true
      while ((test) && (this.action !== 'you win')) {
        var h = 0

        if (this.disqueprochain > 4) { this.disqueprochain = 0 }
        var i = 0
        for (var j = 0; j < 15; j++) { if (this.tourdehanois[j].id === this.disqueprochain) { i = j } }
        if (i < 5) { h = 10 } else if (i < 10) { h = 0 } else { h = 5 }
        if (this.tourdehanois[i].anneaudrable) {
          if (this.deplace1(i, h)) {
            this.disqueprochain++; test = false; this.coup++
          } else {
            if (i < 5) { h = 5 } else if (i < 10) { h = 10 } else { h = 0 }
            if (this.deplace1(i, h)) { this.disqueprochain++; test = false; this.coup++ } else {
              this.disqueprochain++
            }
          }
        } else {
          this.disqueprochain++
        }
        if (this.storePosition()) { this.action = 'you win'; clearInterval(this.timer) }
      }
    },

    deplace () {
      this.timer = setInterval(() => { this.deplace_un_par_un() }, 1000)
    },
    deplace1 (data1, target1) {
      if (this.testanneauLepluspetit(data1, target1)) {
        let temp = this.tourdehanois[data1]
        this.$set(this.tourdehanois, data1, this.tourdehanois[target1])
        this.$set(this.tourdehanois, target1, temp)
        this.tombe()
        this.redesign()
        return true
      } else { return false }
    },

    quelAnneau (n) {
      var n1 = this.index++
      console.log(n1)
      this.posi[n] = n
      return n1
    }
  }
}
</script>
<style lang="less" scoped>
@import "./font.less";
.shuffleFast-move {
  transition: transform 1s;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}
@nombredor: 1.618em;

.color-primary-0 {
  color: #aaa639;
} /* Main Primary color */
.color-primary-1 {
  color: #333322;
}
.color-primary-2 {
  color: #6f6d37;
}
.color-primary-3 {
  color: #e5df26;
}
.color-primary-4 {
  color: #fff701;
}

.nombredecoup {
  position: absolute;
  width: pow(@nombredor, 4);
  height: calc(@nombredor*7);

  background-color: @rgba-secondary-2-1;
  border: 1px solid rgba(0, 0, 0, 0.8);
  border-radius: 1vw;

  text-align: center;
   top: 62vh;
  font-family: "Merriweather", serif;
  padding: 1vw;
}
.nombredecoup2 {
  position: relative;

display: inline-block;
  width: 75px;
  height: 100px;
  float: left;
  margin-right: 5px;
  margin-top: 1vh;
  border-radius: 2px;
background-color:red;
}
.action {
  position: absolute;
  width: pow(@nombredor, 8);
  height: pow(@nombredor, 5);
  left: 23vw;
  background-color: @rgba-secondary-2-3;
  border: 1px solid rgba(0, 0, 0, 0.8);
  border-radius: 1vw;
  color: black;
  vertical-align: middle;
  display: inline-block;
  line-height: 1.2; /* on rétablit le line-height */
  text-align: left;
  font-family: "Merriweather", serif;
  top: 62vh;
  padding: 1vw;
}

body {
  background-color: white;
  font-family: "Merriweather", serif;

  color: black;
}

p {
  font-size: small;
  margin-bottom: 1.3em;
}

h1,
h2,
h3,
h4 {
  margin: 1em 0 0.5em;
  font-weight: inherit;
  line-height: 1;
}

h1 {
  color: black;
  margin-top: 0;
  font-size: 6.7em;
}

h2 {
  color: @rgba-secondary-1-2;
  font-size: 4.236em;
}

h3 {
  color: @rgba-secondary-1-2;
  font-size: 2.618em;
}

h4 {
  color: @rgba-secondary-1-2;
  margin-top: 0em;
  font-size: 1.618em;
}

small,
.font_small {
  font-size: 0.618em;
}
.posanneau        {
 display: flex;

  flex-flow: column wrap;
  justify-content: flex-start;
  align-items: center;
  align-content: center;
 background-color:@rgba-secondary-2-0;
position: relative;
top:8vh;
 border-radius: 1vh;
width: 99vw;
height: 45vh;
}
.touredehanoi{
flex: 0 1 auto;

  align-self: auto;
 min-width: 33vw;
  min-height: 9vh;

}

.grid-container {
  display: grid;
  grid-template-columns: auto auto auto;
  background-color: @rgba-secondary-1-4;
  padding: 1vh;
  grid-auto-rows: minmax(8vh, auto);
  border-radius: 1vh;
}
.grid-item {
  background-color: @rgba-secondary-1-4;
  border: 1px solid rgba(0, 0, 0, 0.8);
  padding: 1px;
  font-size: 30px;
  text-align: center;
}

img {
  width: 3vw;
  min-height: 5vh;

  margin-top: 0;
  margin-left: 1vw;
  background-repeat: no-repeat;
  background-size: cover;
  display: inline-block;
  vertical-align: middle;
  flex: 0 1 auto;
}
.tower1 {
    position: absolute;
    top: 11vh;
    left:16vw;
    border-radius: 1vw;
    width: 3vw;
    height: 50vh;
    z-index: 0;
    background: linear-gradient(to right, #d7b889, #b27315, #966f33);
}
.tower2 {
    position: absolute;
    top: 11vh;
    left:49vw;
    border-radius: 1vw;
    width: 3vw;
    height: 50vh;
    z-index: 0;
    background: linear-gradient(to right, #d7b889, #b27315, #966f33);
}
.tower3 {
    position: absolute;
    top: 11vh;
    left:82vw;
    border-radius: 1vw;
    width: 3vw;
    height: 50vh;
    z-index: 0;
    background: linear-gradient(to right, #d7b889, #b27315, #966f33);
}
.but1{
  top:90vh;
  left:10vw;
}
.but2{
  top:90vh;
  left:40vw;
}
button {
  position: absolute;
  color: #fbc831;
  font-family: "Bitter", serif;
  text-shadow: 3px 3px 3px #d17b0f;
  background: #007100;
  background-repeat: no-repeat;
  border: none;
  cursor: pointer;
  overflow: hidden;
  outline: none;
  border-radius: 5px;
  text-decoration: none;
  height: auto;
  font-size: 1.618em;
  font-weight: 800;
  text-align: center;

  width: auto;

  z-index: 2;
  transition: all 0.5s ease;
}
button:hover {
  /*background:#4BA62A;*/
  background-image: linear-gradient(
    to left,
    #007100,
    #1b7e0c,
    #2d8b17,
    #3c9821,
    #4ba62a
  );
  transform-origin: 0% 50%;
  transform: rotateY(4deg) rotateZ(1deg);
}
</style>
