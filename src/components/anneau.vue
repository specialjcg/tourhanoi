
<template>

  <div  class="anneaux ball" :class="num1" :id="nommeid()" :style="chandelarg()" @mousemove="elementDrag($event)" @mousedown="dragElement($event)" @mouseup="closeDragElement()"></div>

</template>
<script>
export default {
  props: {qAnneau: Number, aryoudragable: false},
  data () {
    return {
      num1: '',
      larg: 0,
      elmnt1: null,
      pos1: 0,
      pos2: 0,
      dragging: false,
      whoisdragging: '',
      pos4: 0

    }
  },

  methods: {

    closeDragElement: function () {
      /* stop moving when mouse button is released: */
      this.$emit('selected', document.getElementById(this.whoisdragging))
      this.dragging = false
      this.num1 = ''
      this.whoisdragging = ''
    },
    nommeid () {
      var index = 'drag' + this.qAnneau
      return index
    },
    elementDrag (e) {
      /* e = e || window.event; */
      if ((this.whoisdragging !== '') && this.dragging && this.aryoudragable) {
        var elmnt = document.getElementById(e.target.id)

        // calculate the new cursor position:
        this.pos1 = this.pos3 - e.clientX
        this.pos2 = this.pos4 - e.clientY
        this.pos3 = e.clientX
        this.pos4 = e.clientY
        // set the element's new position:
        if (this.pos1 < 0) { this.num1 = 'tordre' } else { this.num1 = 'tordre1' }

        elmnt.style.top = elmnt.offsetTop - this.pos2 + 'px'
        elmnt.style.left = elmnt.offsetLeft - this.pos1 + 'px'
      }
    },
    dragElement: function (e) {
      if (this.aryoudragable) {
        this.whoisdragging = e.target.id
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

      if (this.larg === 0) {
        tran = 6 * (Number(this.qAnneau) + 1) + '%'
        var model = 6 * (Number(this.qAnneau) + 1)
        this.larg = model
      } else {
        tran = Number(this.larg) + '%'
        tranleft = 3 * (5 - Number(this.qAnneau) + 1) - 3.5 + 'vw'
      }

      return { width: tran, left: tranleft }
    }}
}
</script>
<style lang="less" scoped>
@import "./font.less";
.anneaux {
  justify-self: center;
  align-self: center;
  position: absolute;

  cursor: move;
  height: 5vh;

  background: radial-gradient(
    circle at 10vw 10vw,
    @rgba-primary-1,
    rgb(104, 99, 99)
  );
 transform: translateZ(0);
  border-radius: 1vw;
  transition: width 0.5s ease;
}

.ball {
  width: 100%;
  height: 3.5vw;
  margin: 0;

  position: absolute;
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

  background: radial-gradient(
    circle at 50% 0px,
    #ffffff,
    rgba(255, 255, 255, 0) 58%
  );
  filter: blur(5px);
  z-index: 2;
}

.tordre {
  transform: skew(-34deg, 0deg);
}
.tordre1 {
  transform: skew(34deg, 0deg);
}
</style>
