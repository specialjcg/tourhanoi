/* eslint-disable indent */

<template>
<div @mouseup="redesign()" v-hammer:panend="redesign">
<div class="titre"><h3>Tour de Hanoï</h3></div>
<div v-if="action==='you win'" class="shake-horizontal titre1">{{this.action}}</div>
<transition-group name="shuffleFast" tag="div" class="posanneau" @dragover.prevent="allowDrop()"  :key="componentKey" mode="out-in">
  <div v-for="tourdehanoi in tourdehanois" :key="tourdehanoi.id" :id="'div'+tourdehanoi.id" class="touredehanoi" >
 <anneau   :qAnneau="tourdehanoi.id" :aryoudragable=tourdehanoi.removableRing  v-if="tourdehanoi. ringInPlaceOfSizeNotNull!==0" @selected="drop1($event)" ></anneau>
<div class="touredehanoi" v-else></div>
</div>
<div v-for="n in 3" :class="'tower'+n" :key="'tow'+n"></div>
</transition-group>

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
      ringPositionWhenSolved: [],
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
    this.ringPositionWhenSolved = [3, 6, 9, 12, 15]
    this.tourdehanois = []
    this.id = 0
    for (var i = 0; i < 15; i++) {
      let tourdehanoi = {
        id: i,
        ringInPlaceOfSizeNotNull: 0,
        removableRing: ''

      }

      this.tourdehanois.push(tourdehanoi)
    }

    this.tourdehanois[0].removableRing = true
    this.tourdehanois[0].ringInPlaceOfSizeNotNull = 1
    this.tourdehanois[0].id = 0
    for (i = 1; i < 5; i++) { this.tourdehanois[i].removableRing = false; this.tourdehanois[i].ringInPlaceOfSizeNotNull = i + 1; this.tourdehanois[i].id = i }
    for (i = 5; i < 15; i++) { this.tourdehanois[i].removableRing = false; this.tourdehanois[i].ringInPlaceOfSizeNotNull = 0; this.tourdehanois[i].id = i }
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
      if ((this.tourdehanois[10].ringInPlaceOfSizeNotNull !== 0) &&
     (this.tourdehanois[11].ringInPlaceOfSizeNotNull !== 0) &&
     (this.tourdehanois[12].ringInPlaceOfSizeNotNull !== 0) &&
     (this.tourdehanois[13].ringInPlaceOfSizeNotNull !== 0) &&
     (this.tourdehanois[14].ringInPlaceOfSizeNotNull !== 0)) { return true } else { return false }
    },
    redesign () {
      var tranleft = ''
      for (var k = 0; k < 5; k++) {
        var targ = 'drag' + k

        document.getElementById(targ).style.top = ''

        tranleft = this.posleftAnneau[k] + 'vw'
        document.getElementById(targ).__vue__.whoisdragging = ''
        document.getElementById(targ).style.left = tranleft
        document.getElementById(targ).className = 'anneaux ball'
      }
    },

    testIfTheRingIsRingIsMoving () {
      var sianneau = true
      for (var numberOfRingsLocation = 0; numberOfRingsLocation < 5; numberOfRingsLocation++) {
        if (this.tourdehanois[numberOfRingsLocation].ringInPlaceOfSizeNotNull !== 0) {
          this.tourdehanois[numberOfRingsLocation].removableRing = sianneau
          sianneau = false
        } else {

        }
      }
      sianneau = true
      for (numberOfRingsLocation = 5; numberOfRingsLocation < 10; numberOfRingsLocation++) {
        if (this.tourdehanois[numberOfRingsLocation].ringInPlaceOfSizeNotNull !== 0) {
          this.tourdehanois[numberOfRingsLocation].removableRing = sianneau
          sianneau = false
        } else {

        }
      }
      sianneau = true
      for (numberOfRingsLocation = 11; numberOfRingsLocation < 15; numberOfRingsLocation++) {
        if (this.tourdehanois[numberOfRingsLocation].ringInPlaceOfSizeNotNull !== 0) {
          this.tourdehanois[numberOfRingsLocation].removableRing = sianneau
          sianneau = false
        } else {

        }
      }
    },
    testIfTheLocationIsValidForMoving (numberOfRingsLocation) {
      return ((this.tourdehanois[numberOfRingsLocation].removableRing) && (this.tourdehanois[numberOfRingsLocation + 1].ringInPlaceOfSizeNotNull === 0))
    },
    ringDisplacementByFallingDown (numberOfRingsLocation) {
      let temp = this.tourdehanois[numberOfRingsLocation]
      this.$set(this.tourdehanois, numberOfRingsLocation, this.tourdehanois[numberOfRingsLocation + 1])
      this.$set(this.tourdehanois, numberOfRingsLocation + 1, temp)
    },
    testringDropOnAPositionAndFallsOnTheTarget (numberOfRingsLocation) {
      if (this.testIfTheLocationIsValidForMoving(numberOfRingsLocation)) {
        this.ringDisplacementByFallingDown(numberOfRingsLocation)
      }
    },
    ringDropOnAPositionAndFallsOnTheTarget () {
      for (var numberOfRingsLocation = 0; numberOfRingsLocation < 14; numberOfRingsLocation++) {
        if ((numberOfRingsLocation !== 4) && (numberOfRingsLocation !== 9)) {
          this.testringDropOnAPositionAndFallsOnTheTarget(numberOfRingsLocation)
        }
      }
      clearInterval(this.timer)
      this.testIfTheRingIsRingIsMoving()
    },
    testanneauLepluspetit (anneauDeplacer, colonneCible) {
      var max = 15
      var test1 = true
      if (colonneCible < 4) { max = 5 } else if (colonneCible < 10) { max = 10 } else { max = 15 }
      for (var k = colonneCible; k < max; k++) {
        if (this.tourdehanois[k].ringInPlaceOfSizeNotNull !== 0) {
          if (this.tourdehanois[k].ringInPlaceOfSizeNotNull < this.tourdehanois[anneauDeplacer].ringInPlaceOfSizeNotNull) {
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
          if (this.moveARingFromOnePositionToATarget(chgtdisque, data)) {}
        }
      }
      this.redesign()
      this.testIfTheRingIsRingIsMoving()
    },
    deplace_un_par_un () {
      var test = true
      while ((test) && (this.action !== 'you win')) {
        var h = 0

        if (this.disqueprochain > 4) { this.disqueprochain = 0 }
        var i = 0
        for (var j = 0; j < 15; j++) { if (this.tourdehanois[j].id === this.disqueprochain) { i = j } }
        if (i < 5) { h = 10 } else if (i < 10) { h = 0 } else { h = 5 }
        if (this.tourdehanois[i].removableRing) {
          if (this.moveARingFromOnePositionToATarget(i, h)) {
            this.disqueprochain++; test = false
          } else {
            if (i < 5) { h = 5 } else if (i < 10) { h = 10 } else { h = 0 }
            if (this.moveARingFromOnePositionToATarget(i, h)) { this.disqueprochain++; test = false } else {
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
      this.timer = setInterval(() => { this.deplace_un_par_un() }, 1600)
    },

    moveARingFromOnePositionToATarget (data1, target1) {
      if (this.testanneauLepluspetit(data1, target1)) {
        if ((target1 === 0) && (data1 > 5)) { this.coup++ }
        if ((target1 === 5) && ((data1 > 10) || (data1 < 5))) { this.coup++ }
        if ((target1 === 10) && (data1 < 10)) { this.coup++ }

        let temp = this.tourdehanois[data1]
        this.$set(this.tourdehanois, data1, this.tourdehanois[target1])
        this.$set(this.tourdehanois, target1, temp)
        this.timer = setInterval(() => {
          this.ringDropOnAPositionAndFallsOnTheTarget()
        }, 800)

        return true
      } else { return false }
    }
  }
}
</script>
<style lang="less" scoped>
@import "./font.less";
.shuffleFast{
backface-visibility: hidden;
  z-index: 4;

}
.shuffleFast-move {
  /*transition: transform 1s;*/
  transition: all 600ms ease-in-out 50ms;
  z-index:3;
}

/* appearing */
.shuffleFast-enter-active {
  transition: all 400ms ease-out;
   z-index:3;
}

/* disappearing */
.shuffleFast-leave-active {
  transition: all 200ms ease-in;
  position: absolute;
  z-index: 3;
}

/* appear at / disappear to */
.shuffleFast-enter,
.shuffleFast-leave-to {
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
  width: pow(@nombredor, 5);
  height: calc(@nombredor*7);

  background-color: rgba(210,255,82,1);
  box-shadow: 3px 3px 20px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(0, 0, 0, 0.8);
  border-radius: 1vw;

  text-align: center;
   top: 62vh;
  font-family: "Merriweather", serif;
  padding: 1vw;
}
.titre {
  position: absolute;
top:0;
margin: 0;
  width: 100vw;
  height: 1vh;

  margin-left:auto;
  margin-right: auto;
font-family: "Merriweather", serif;

}
.titre1 {
  position: relative;
top:0;
margin: 0;
  width: 50vw;
  height: 5vh;
background-color:@rgba-secondary-2-4;
  margin-left:auto;
  margin-right: auto;
font-family: "Merriweather", serif;
  font-size: 2em;
}
.action {
  position: absolute;
  width: pow(@nombredor, 8);
  height: pow(@nombredor, 5);
  left: 23vw;
  background-color: rgba(210,255,82,1);
  border: 1px solid rgba(0, 0, 0, 0.8);
  box-shadow: 3px 3px 20px rgba(0, 0, 0, 0.5);
  border-radius: 1vw;
  color: black;
  vertical-align: middle;
  display: inline-block;
  line-height: 1.2; /* on rétablit le line-height */
  text-align: left;
  font-family: "Merriweather", serif;
  top: 62vh;
  padding: 1vw;
  padding-top: 0;

}

body {
  background-color: white;
  font-family: "Merriweather", serif;

  color: black;
}

p {
  font-size: 1em;

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
  color: @rgba-secondary-1-4;
  font-size: 2.618em;
  margin:inherit;
}

h4 {
  color: @rgba-secondary-1-4;
  margin-top: 0em;
  font-size: 1.618em;
}

small,
.font_small {
  font-size: 0.618em;
}
.posanneau        {
 display: flex;
position:absolute;
  flex-flow: column wrap;
  justify-content: flex-start;
  align-items: center;
  align-content: center;
 background-color:rgba(241,231,103,1);
 box-shadow: 3px 3px 20px rgba(0, 0, 0, 0.5);
position: relative;
top:0vh;
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
    top: 0;
    left:15vw;
    border-radius: 1vw;
    width: 3vw;
    height: 45vh;
    z-index: 1;
    background: linear-gradient(to right, #d7b889, #b27315, #966f33);
}
.tower2 {
    position: absolute;
    top: 0;
    left:48vw;
    border-radius: 1vw;
    width: 3vw;
    height: 45vh;
    z-index: 1;
    background: linear-gradient(to right, #d7b889, #b27315, #966f33);
}
.tower3 {
    position: absolute;
    top: 0;
    left:81vw;

    border-radius: 1vw;
    width: 3vw;
    height: 45vh;
    z-index: 1;
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
.shake-horizontal {
  -webkit-animation: shake-horizontal 0.8s cubic-bezier(0.455, 0.030, 0.515, 0.955) infinite;
animation: shake-horizontal 0.8s cubic-bezier(0.455, 0.030, 0.515, 0.955) infinite;
}
/* ----------------------------------------------
 * Generated by Animista on 2019-2-11 18:32:51
 * w: http://animista.net, t: @cssanimista
 * ---------------------------------------------- */

/**
 * ----------------------------------------
 * animation shake-horizontal
 * ----------------------------------------
 */
@-webkit-keyframes shake-horizontal {
  0%,
  100% {
    -webkit-transform: translateX(0);
            transform: translateX(0);
  }
  10%,
  30%,
  50%,
  70% {
    -webkit-transform: translateX(-10px);
            transform: translateX(-10px);
  }
  20%,
  40%,
  60% {
    -webkit-transform: translateX(10px);
            transform: translateX(10px);
  }
  80% {
    -webkit-transform: translateX(8px);
            transform: translateX(8px);
  }
  90% {
    -webkit-transform: translateX(-8px);
            transform: translateX(-8px);
  }
}
@keyframes shake-horizontal {
  0%,
  100% {
    -webkit-transform: translateX(0);
            transform: translateX(0);
  }
  10%,
  30%,
  50%,
  70% {
    -webkit-transform: translateX(-10px);
            transform: translateX(-10px);
  }
  20%,
  40%,
  60% {
    -webkit-transform: translateX(10px);
            transform: translateX(10px);
  }
  80% {
    -webkit-transform: translateX(8px);
            transform: translateX(8px);
  }
  90% {
    -webkit-transform: translateX(-8px);
            transform: translateX(-8px);
  }
}
@media only screen and (min-width: 340px) {
  .nombredecoup {
  position: relative;
  width: pow(@nombredor, 4);
  height: calc(@nombredor*6);

  background-color: rgba(210,255,82,1);
  box-shadow: 3px 3px 20px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(0, 0, 0, 0.8);
  border-radius: 1vw;

  text-align: center;
   top: 1vh;
  font-family: "Merriweather", serif;
  padding: 1vw;
}
.but1{
  top:70vh;
  left:0;
}
.but2{
  top:70vh;
  left:40vw;
}
button{

 font-size: .8em;
}
.action {
  position: absolute;
  width: pow(@nombredor, 5.5);
  height: pow(@nombredor, 5.1);
  left: 36vw;
  background-color: rgba(210,255,82,1);
  border: 1px solid rgba(0, 0, 0, 0.8);
  box-shadow: 3px 3px 20px rgba(0, 0, 0, 0.5);
  border-radius: 1vw;
  color: black;
  vertical-align: middle;
  display: inline-block;
  line-height: 1.2; /* on rétablit le line-height */
  text-align: left;
  font-family: "Merriweather", serif;
  top: 40vh;
  padding: 1vw;
  padding-top: 0;

}
p{
font-size: 0.6em;

}
.nombredecoup h1 {
  font-size:inherit;
  color: black;
  margin: inherit;
  font-size: pow(@nombredor, 3);
}
.posanneau  {
  height:30vh;
}
.tower1 {

    height: 30vh;

}
.tower2 {

    height: 30vh;

}
.tower3 {

    height: 30vh;

}
.touredehanoi{

  min-height: 6vh;

}
}

@media only screen and (min-width: 640px) {
 .nombredecoup {
  position: absolute;
  width: pow(@nombredor, 5);
  height: calc(@nombredor*7);

  background-color: rgba(210,255,82,1);
  box-shadow: 3px 3px 20px rgba(0, 0, 0, 0.5);
  border: 1px solid rgba(0, 0, 0, 0.8);
  border-radius: 1vw;

  text-align: center;
   top: 62vh;
  font-family: "Merriweather", serif;
  padding: 1vw;
}
.but1{
  top:90vh;
  left:10vw;
}
.but2{
  top:90vh;
  left:40vw;
}
.action {
  position: absolute;
  width: pow(@nombredor, 8);
  height: pow(@nombredor, 5);
  left: 23vw;
  background-color: rgba(210,255,82,1);
  border: 1px solid rgba(0, 0, 0, 0.8);
  box-shadow: 3px 3px 20px rgba(0, 0, 0, 0.5);
  border-radius: 1vw;
  color: black;
  vertical-align: middle;
  display: inline-block;
  line-height: 1.2; /* on rétablit le line-height */
  text-align: left;
  font-family: "Merriweather", serif;
  top: 62vh;
  padding: 1vw;
  padding-top: 0;

}
p {
  font-size: 1em;

}
button{
 font-size: 1.618em;
}
.nombredecoup h1 {
  color: black;
  margin-top: 0;
  font-size: 6.7em;
}
.posanneau{
 height:45vh;}

.tower1 {
    position: absolute;
    top: 0;
    left:15vw;
    border-radius: 1vw;
    width: 3vw;
    height: 45vh;
    z-index: 1;
    background: linear-gradient(to right, #d7b889, #b27315, #966f33);
}
.tower2 {
    position: absolute;
    top: 0;
    left:48vw;
    border-radius: 1vw;
    width: 3vw;
    height: 45vh;
    z-index: 1;
    background: linear-gradient(to right, #d7b889, #b27315, #966f33);
}
.tower3 {
    position: absolute;
    top: 0;
    left:81vw;

    border-radius: 1vw;
    width: 3vw;
    height: 45vh;
    z-index: 1;
    background: linear-gradient(to right, #d7b889, #b27315, #966f33);
}
.touredehanoi{
flex: 0 1 auto;

  align-self: auto;
 min-width: 33vw;
  min-height: 9vh;

}
}
</style>
