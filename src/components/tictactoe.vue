
<template>
<div>
  <div class="grid-container" >
  <div :id="numdiv(n)" class="grid-item" v-for="n in 15"  @dragover.prevent="allowDrop($event)" :key="n">

 <anneau  :qAnneau="0" :aryoudragable=aryoudragable1  v-if="n===1" @selected="drop1($event)"   ></anneau>
<anneau  :qAnneau="1" :aryoudragable=aryoudragable2  v-else-if="n===4" @selected="drop1($event)"  ></anneau>
<anneau  :qAnneau="2" :aryoudragable=aryoudragable3  v-else-if="n===7"  @selected="drop1($event)"  ></anneau>
<anneau  :qAnneau="3" :aryoudragable=aryoudragable4  v-else-if="n===10" @selected="drop1($event)"   ></anneau>
<anneau  :qAnneau="4" :aryoudragable=aryoudragable5  v-else-if="n===13"  @selected="drop1($event)"  ></anneau>

  </div>

</div>
<div class="nombredecoup">

  <h4>Nombre de coup</h4>
  <h1>{{coup}}</h1>

  </div>
  <button @click.prevent="deplace()">deplace</button>
  <img src="./logo.png">
<div class="action"><p>
Modèle d'une tour de Hanoï <br>

Les tours de Hanoï (originellement, la tour d'Hanoïa) sont un jeu de réflexion imaginé par le mathématicien français Édouard Lucas, et consistant à déplacer des disques de diamètres différents d'une tour de « départ » à une tour d'« arrivée » en passant par une tour « intermédiaire », et ceci en un minimum de coups, tout en respectant les règles suivantes : <br>

on ne peut déplacer plus d'un disque à la fois ;<br>
on ne peut placer un disque que sur un autre disque plus grand que lui ou sur un emplacement vide. <br>
On suppose que cette dernière règle est également respectée dans la configuration de départ.{{this.action}}</p></div>

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
      index: 0,
      index1: 0,
      action: 'continuer',
      storepos: [],
      storeposprec: [],
      storeposWin: [],
      posi: [],
      selectedrag: '',
      aryoudragable5: false,
      aryoudragable4: false,
      aryoudragable3: false,
      aryoudragable2: false,
      aryoudragable1: true

    }
  },
  mounted () {
    this.storeposWin = [3, 6, 9, 12, 15]
  },
  methods: {
    test () {
      return false
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
      this.storepos = []

      var targ

      for (var k = 1; k <= 15; k++) {
        targ = 'div' + k
        if (
          document.getElementById(targ).childNodes.length === 0 ||
          (document.getElementById(targ).childNodes[0].nodeName ===
            '#comment' &&
            document.getElementById(targ).childNodes.length === 1)
        ) {
        } else {
          this.storepos.push(k)
        }
      }
      if (this.storeposprec.join() === this.storepos.join()) {
      } else {
        this.storeposprec = this.storepos
        this.coup++
      }
      if (this.storepos.join() === this.storeposWin.join()) {
        this.action = 'you win'
      }
    },
    redesign () {
      var data = 0
      for (var k = 0; k < 5; k++) {
        var targ = 'drag' + k

        document.getElementById(targ).style.top = ''
        if (document.getElementById(targ).parentNode.offsetLeft < document.body.clientWidth / 3) { data = 3 } else if (document.getElementById(targ).parentNode.offsetLeft < (2 * document.body.clientWidth) / 3) { data = 35 } else { data = 68 }

        document.getElementById(targ).style.left = data + 3 * (5 - k) - 3.5 + 'vw'
      }
    },
    reaffecdragable1 () {
      var test = true
      for (var k = 1; k < 6; k++) {
        var targ = 'div' + Number((k - 1) * 3 + 1)

        if (document.getElementById(targ).childNodes[1] !== undefined && document.getElementById(targ).childNodes.length !== 0) {
          if (document.getElementById(targ).childNodes[1].id === 'drag0') { this.aryoudragable1 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag1') { this.aryoudragable2 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag2') { this.aryoudragable3 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag3') { this.aryoudragable4 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag4') { this.aryoudragable5 = test; test = false }
        }
        if (document.getElementById(targ).childNodes[0] !== undefined && document.getElementById(targ).childNodes.length !== 0) {
          if (document.getElementById(targ).childNodes[0].id === 'drag0') { this.aryoudragable1 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag1') { this.aryoudragable2 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag2') { this.aryoudragable3 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag3') { this.aryoudragable4 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag4') { this.aryoudragable5 = test; test = false }
        }
      }
      test = true
      for (k = 1; k < 6; k++) {
        targ = 'div' + Number((k - 1) * 3 + 2)

        if (document.getElementById(targ).childNodes[1] !== undefined && document.getElementById(targ).childNodes.length !== 0) {
          if (document.getElementById(targ).childNodes[1].id === 'drag0') { this.aryoudragable1 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag1') { this.aryoudragable2 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag2') { this.aryoudragable3 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag3') { this.aryoudragable4 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag4') { this.aryoudragable5 = test; test = false }
        }
        if (document.getElementById(targ).childNodes[0] !== undefined && document.getElementById(targ).childNodes.length !== 0) {
          if (document.getElementById(targ).childNodes[0].id === 'drag0') { this.aryoudragable1 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag1') { this.aryoudragable2 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag2') { this.aryoudragable3 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag3') { this.aryoudragable4 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag4') { this.aryoudragable5 = test; test = false }
        }
      }
      test = true
      for (k = 1; k < 6; k++) {
        targ = 'div' + Number((k - 1) * 3 + 3)

        if (document.getElementById(targ).childNodes[1] !== undefined && document.getElementById(targ).childNodes.length !== 0) {
          if (document.getElementById(targ).childNodes[1].id === 'drag0') { this.aryoudragable1 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag1') { this.aryoudragable2 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag2') { this.aryoudragable3 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag3') { this.aryoudragable4 = test; test = false }
          if (document.getElementById(targ).childNodes[1].id === 'drag4') { this.aryoudragable5 = test; test = false }
        }
        if (document.getElementById(targ).childNodes[0] !== undefined && document.getElementById(targ).childNodes.length !== 0) {
          if (document.getElementById(targ).childNodes[0].id === 'drag0') { this.aryoudragable1 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag1') { this.aryoudragable2 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag2') { this.aryoudragable3 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag3') { this.aryoudragable4 = test; test = false }
          if (document.getElementById(targ).childNodes[0].id === 'drag4') { this.aryoudragable5 = test; test = false }
        }
      }
    },
    tombe () {
      var targ1, targ, targfirst

      var test = true
      while (test) {
        test = false
        for (var k = 1; k < 13; k++) {
          targ = 'div' + k
          if (
            document.getElementById(targ).childNodes.length === 0 ||
            (document.getElementById(targ).childNodes[0].nodeName ===
              '#comment' &&
              document.getElementById(targ).childNodes.length === 1)
          ) {
          } else {
            if (targfirst === '') {
              targfirst = targ
            }
            targ1 = targ
            targ = 'div' + (k + 3)
            if (
              document.getElementById(targ).childNodes.length === 0 ||
              (document.getElementById(targ).childNodes[0].nodeName ===
                '#comment' &&
                document.getElementById(targ).childNodes.length === 1)
            ) {
              document
                .getElementById(targ)
                .appendChild(
                  document.getElementById(targ1).childNodes[document.getElementById(targ1).childNodes.length - 1]
                )
              test = true
            }
          }
        }
      }

      this.storePosition()
    },
    testanneauLepluspetit (colonneCible, anneauDeplacer) {
      var test = true
      var largeurAnneautest = document.getElementById(anneauDeplacer).offsetWidth
      var cibleDiv = Number(colonneCible.id.match(/\d+/g).join(''))

      for (var k = cibleDiv; k < 13; k += 3) {
        var targ = 'div' + (k + 3)

        if (document.getElementById(targ).childNodes[1] !== undefined && document.getElementById(targ).childNodes[1].offsetWidth !== undefined) {
          var largeurAnneauDessous = document.getElementById(targ).childNodes[1].offsetWidth
          if (Number(largeurAnneautest) > largeurAnneauDessous) {
            test = false
            break
          } else {
            test = true
            break
          }
        } else if (document.getElementById(targ).childNodes[0] !== undefined && document.getElementById(targ).childNodes[0].offsetWidth !== undefined) {
          var largeurAnneauDessous2 = document.getElementById(targ).childNodes[0].offsetWidth
          if (Number(largeurAnneautest) > largeurAnneauDessous2) {
            test = false
            break
          } else {
            test = true
            break
          }
        }
      }
      return test
    },
    drop1 (ev) {
      var elmnt = ev

      var data = ''

      document.onmouseup = null
      document.onmousemove = null
      if (ev.offsetLeft < document.body.clientWidth / 3) { data = 'div1' } else if (ev.offsetLeft < (2 * document.body.clientWidth) / 3) { data = 'div2' } else { data = 'div3' }

      if (elmnt !== '') {
        if (document.getElementById(data).childNodes.length === 0) {
          if (this.testanneauLepluspetit(document.getElementById(data), ev.id)) {
            elmnt.style.top = ''

            document.getElementById(data).appendChild(document.getElementById(ev.id))
          }
        } else if (
          document.getElementById(data).childNodes[0].nodeName === '#comment' &&
          document.getElementById(data).childNodes.length === 1
        ) {
          if (this.testanneauLepluspetit(document.getElementById(data), ev.id)) {
            elmnt.style.top = ''

            document.getElementById(data).appendChild(document.getElementById(ev.id))
          }
        } else {
        }
        this.tombe()

        this.selectedrag = ''
      }
      this.redesign()
      this.reaffecdragable1()
    },

    deplace () {
      var data = 'drag2'
      var target = 'div1'
      if (target !== data && data !== 'null') {
        if (document.getElementById(target).childNodes.length === 0) {
          if (
            this.testanneauLepluspetit(document.getElementById(target), data)
          ) {
            document
              .getElementById(target)
              .appendChild(document.getElementById(data))
          }
        } else if (
          document.getElementById(target).childNodes[0].nodeName ===
            '#comment' &&
          document.getElementById(target).childNodes.length === 1
        ) {
          if (
            this.testanneauLepluspetit(document.getElementById(target), data)
          ) {
            document
              .getElementById(target)
              .appendChild(document.getElementById(data))
          }
        } else {
        }
      }

      this.tombe()
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
  margin-top: 3.5vh;
  font-family: "Merriweather", serif;
  padding: 1vw;
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
  margin-top: 3.5vw;
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
  font-size: 6.854em;
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

.grid-container {
  display: grid;
  grid-template-columns: auto auto auto;
  background-color: @rgba-secondary-1-4;
  padding: 10px;
  grid-auto-rows: minmax(65px, auto);
  border-radius: 1vw;
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
button {
  position: relative;
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
  margin-top: 1vh;
  margin-left: 5vw;

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
