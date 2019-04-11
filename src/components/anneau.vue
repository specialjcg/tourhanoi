
<template>

  <div  class="anneaux ball" ref="whoisdragging" :class="num1" :id="nommeid()" :style="chandelarg()"  v-hammer:panstart="dragElementTouch" v-hammer:pan="elementDragTouch" @mousemove="elementDrag($event)" @mousedown="dragElement($event)" v-hammer:panend="closeDragElementTouch" @mouseup="closeDragElement()"></div>

</template>
<script>
export default {
  name: 'anneau',
  props: {qAnneau: Number, aryoudragable: false},
  data () {
    return {
      num1: '',
      larg: 0,
      elmnt1: null,
      elmnt12: null,
      pos1: 0,
      pos2: 0,
      dragging: false,
      whoisdragging: '',
      pos4: 0,
      largeurAnneau: [50, 60, 70, 80, 90],
      posleftAnneau: [8.5, 7, 5, 3, 1.5]

    }
  },
  mounted () {
    /* for (var i = 0; i < 5; i++) {
      var el = document.getElementsByClassName('anneaux ball')[i]
      var self = this
      el.addEventListener('touchstart', function (e) {
        self.dragElementTouch(e)
      })
      el.addEventListener('touchend', function (e) {
        self.closeDragElementTouch(e)
      })
      el.addEventListener('touchmove', function (e) {
        self.elementDragTouch(e)
      })
    } */
  },
  methods: {

    closeDragElement: function () {
      /* stop moving when mouse button is released: */
      this.$emit('selected', document.getElementById(this.whoisdragging))
      this.dragging = false
      this.num1 = ''
      this.whoisdragging = ''
    },
    closeDragElementTouch: function () {
      /* stop moving when mouse button is released: */
      if (this.whoisdragging !== '') {
        this.$emit('selected', document.getElementById(this.whoisdragging))
        this.dragging = false
        this.num1 = ''
        this.whoisdragging = ''
      }
    },
    nommeid () {
      var index = 'drag' + this.qAnneau
      return index
    },
    elementDrag (e) {
      /* e = e || window.event; */

      if ((this.whoisdragging !== '') && this.dragging && this.aryoudragable && e !== undefined) {
        this.pos1 = 0
        this.pos2 = 0

        // calculate the new cursor position:
        this.pos1 = this.pos3 - e.clientX
        this.pos2 = this.pos4 - e.clientY
        this.pos3 = e.clientX
        this.pos4 = e.clientY
        var decal = 0
        // set the element's new position:
        if (this.pos1 < 0) { this.num1 = 'anneauxhover2' } else { this.num1 = 'anneauxhover' }
        if (this.elmnt12.left < document.body.clientWidth / 3) { decal = 0 } else if (this.elmnt12.left < (2 * document.body.clientWidth) / 3) { decal = (document.body.clientWidth) / 3 } else { decal = (2 * document.body.clientWidth) / 3 }

        /*  if (this.elmnt12.left > 1000) { decal = 990 } else if (this.elmnt12.left > 500) { decal = 500 } */
        this.elmnt1.style.top = -this.elmnt12.top - this.elmnt12.height / 2 + e.clientY + 'px'
        this.elmnt1.style.left = -decal - this.elmnt12.width / 2 + e.clientX + 'px'
      }
    },
    elementDragTouch (e) {
      /* e = e || window.event; */

      if ((this.whoisdragging !== '') && this.dragging && this.aryoudragable && e !== undefined) {
        this.pos1 = 0
        this.pos2 = 0

        // calculate the new cursor position:
        this.pos1 = this.pos3 - e.center.x
        this.pos2 = this.pos4 - e.center.y
        this.pos3 = e.clientX
        this.pos4 = e.clientY
        var decal = 0
        // set the element's new position:
        if (this.pos1 < 0) { this.num1 = 'anneauxhover2' } else { this.num1 = 'anneauxhover' }
        if (this.elmnt12.left < document.body.clientWidth / 3) { decal = 0 } else if (this.elmnt12.left < (2 * document.body.clientWidth) / 3) { decal = (document.body.clientWidth) / 3 } else { decal = (2 * document.body.clientWidth) / 3 }

        /*  if (this.elmnt12.left > 1000) { decal = 990 } else if (this.elmnt12.left > 500) { decal = 500 } */
        this.elmnt1.style.top = -this.elmnt12.top - this.elmnt12.height / 2 + e.center.y + 'px'
        this.elmnt1.style.left = -decal - this.elmnt12.width / 2 + e.center.x + 'px'
      }
    },
    dragElementTouch: function (e) {
      if (this.aryoudragable && e !== undefined) {
        this.whoisdragging = e.target.id
        this.elmnt1 = document.getElementById(e.target.id)
        this.elmnt12 = this.elmnt1.getBoundingClientRect()
        this.pos3 = e.center.x
        this.pos4 = e.center.y
        this.dragging = true
        // call a function whenever the cursor moves:
        /* document.onmousemove = self.elementDrag */
      }
    },
    dragElement: function (e) {
      if (this.aryoudragable) {
        this.whoisdragging = e.target.id
        this.elmnt1 = document.getElementById(e.target.id)
        this.elmnt12 = this.elmnt1.getBoundingClientRect()
        this.pos3 = e.clientX
        this.pos4 = e.clientY
        this.dragging = true
        // call a function whenever the cursor moves:
        /* document.onmousemove = self.elementDrag */
      }
    },
    chandelarg () {
      var tran = ''
      var tranleft = ''

      tran = this.largeurAnneau[this.qAnneau] + '%'
      tranleft = this.posleftAnneau[this.qAnneau] + 'vw'

      return { width: tran, left: tranleft }
    }}
}
</script>
<style lang="less" scoped>
@import "./font.less";
.anneaux {
  justify-self: center;
  align-self: center;
  position: relative;

  cursor: move;
  height: 9vh;
z-index: 2;
  background: radial-gradient(
    circle at 10vw 10vw,
    @rgba-primary-1,
    rgb(104, 99, 99)
  );
 transform: translateZ(0);
  border-radius: 1vw;
  transition: width 1s ;
  /*transition: all 0.3s;*/

}

.ball {
  min-width: auto;

 z-index: 2;
margin-top: 0.01vh;
margin-bottom: 0.01vh;
  position: relative;
  background: radial-gradient(
    circle at 50% 120%,
    #81e8f6,
    #76deef 10%,
    #055194 80%,
    #062745 100%
  );
}
.ball:before {
  content: "";
  position: absolute;
  top: 10%;
  left: 5%;
  width: 90%;
  height: 90%;
z-index: 2;
  background: radial-gradient(
    circle at 50% 0px,
    #ffffff,
    rgba(255, 255, 255, 0) 58%
  );
  filter: blur(5px);
 z-index: 2;
}

.tordre {
  transform: translate3d;

}
.tordre1 {
  transform: skew(10deg, 0deg);

}
@media only screen and (min-width: 340px) {
.ball{

height: 5.7vh;

}

}@media only screen and (min-width: 640px) {
.ball{
height: 8.5vh;

}
}

</style>
