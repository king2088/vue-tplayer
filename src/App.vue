<template>
  <div id="app">
    <div class="list"><span>{{music.name}}-{{music.singer}}</span><a @click="isShow(),isBg()"><img src="./assets/player/list.png" alt=""></a></div>
    <div class="music">
      <div class="text">{{music.name}}<span>{{music.singer}}</span></div>
      <div class="img"><img id="pic" :src="music.img"></div>
    </div>
    <div class="player">
      <div class="ico"><img src="./assets/player/backward.png" id="pre" @click="isPre"></div>
      <div class="ico"><img src="./assets/player/play.png" id="play" @click="isPlay"><img src="./assets/player/pause.png" id="stop" @click="isStop"></div>
      <div class="ico"><img src="./assets/player/forward.png" id="next" @click="isNext"></div>
    </div>
    <audio id="audio" :src="music.url" preload autoplay @ended="isNext"></audio>
    <transition name="fade">
      <div class="songList" v-show="showList">
         <div class="items">
           <div class="title">歌曲列表 <span id="close" @click="isClose"><img src="./assets/player/close.png"></span></div>
           <div class="item" v-for="(item, index) in MList">
             <a :id="'item-'+index" @click="clickSong(index)">
               <span class="l">{{MList[index].name}}</span>
               <span class="r">{{MList[index].singer}}</span>
               <div class="clear"></div>
             </a>
           </div>
         </div>
       </div>
    </transition>
  </div>
</template>

<script>
export default {
    name: 'app',
    data(){
        return{
         apiUrl: 'http://www.egtch.com/t_works/Vuedata/data.php',
             id: 0,
             bg:'',
          music: '',
          MList:'',
       showList: false
        }
    },
    created(){
        this.getMusic(this.id)
        this.isBg()
    },
    watch:{
        'id':'getMusic',
        'bg':'isBg'
    },
    methods:{
        getMusic(p){
            this.$http.get(this.apiUrl).then(response => {
                // get body data
                response = response.body
                this.MList = response.music
                /*alert(this.MList)*/
                if(p < response.music.length) {
                    this.music = response.music[p]
                }
                if(p > response.music.length){
                    this.id = 0
                    this.music = response.music[this.id]
                }
                if(p < 0){
                    this.id = response.music.length
                    this.music = response.music[this.id]
                }
            })
        },
        /*查看当前audio是否正在播放，如果停止，那么播放下一曲*/

        isPlay(){
          document.getElementById("audio").play();
          document.getElementById("play").style.display="none";
          document.getElementById("stop").style.display="inline";
          document.getElementById("pic").style.animationPlayState="running";
          return false
        },
        isStop(){
           document.getElementById("audio").pause();
           document.getElementById("stop").style.display="none";
           document.getElementById("play").style.display="inline";
           document.getElementById("pic").style.animationPlayState="paused";
           return false
        },
        isNext(){
            this.id++;
            /*为了防止停止状态下，点击下一曲的时候，停止图标不出现，并且专辑图片不转动*/
            document.getElementById("play").style.display="none";
            document.getElementById("stop").style.display="inline";
            document.getElementById("item-"+this.bg).style.background='none';
            document.getElementById("pic").style.animationPlayState="running";
        },
        isPre(){
            this.id--;
            document.getElementById("play").style.display="none";
            document.getElementById("stop").style.display="inline";
            document.getElementById("pic").style.animationPlayState="running";
        },
        isShow(){
            this.showList = true
        },
        isClose(){
            this.showList = false
        },
        isBg(){
            this.bg = this.id
            document.getElementById("item-"+this.bg).style.background='#F2F2F2';
        },
        /*点击列表中的歌曲，并播放*/
        clickSong(i){
            this.id = i;
            document.getElementById("item-"+this.bg).style.background='none';
            document.getElementById("play").style.display="none";
            document.getElementById("pic").style.animationPlayState="running";
            this.isBg()
        }
    }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
html,body
  height 100%
  width 100%
  max-width 600px
  margin 0 auto
  .clear
    clear both
  #app
    font-family 'Microsoft YaHei','\5FAE\8F6F\96C5\9ED1'
    font-weight lighter
    -webkit-font-smoothing antialiased
    -moz-osx-font-smoothing grayscale
    color #2c3e50
    height 100%
    width 100%
    overflow hidden
    .list
      width 100%
      height 60px
      line-height 60px
      background #F2F2F2
      border-bottom 1px #333 solid
      span
        padding-left 20px
        display inline-block
        color darkblue
      & > a
        display block
        float right
        & > img
          margin-top 8px
          width 40px
    .player
      width 100%
      max-width 600px
      height 30%
      opacity .8
      position fixed
      background #F2F2F2
      border-top 3px #000 solid
      border-bottom 10px brown solid
      bottom 0
      font-size 0
      border-top-left-radius 50%
      border-top-right-radius 50%
      text-align center
      display flex
      .ico
        flex 1
        padding-top 50px
        text-align center
        #play
          width 60%
          max-width 64px
          margin-top 35px
          display none
        #stop
          margin-top 35px
          width 60%
          max-width 64px
        & > img
          border 0
          width 32px
          vertical-align middle
    .music
      width 100%
      height 100%
      background url("./assets/player/bg.jpg")
      position relative
      top 0
      left 0
      .text
        width 100%
        text-align center
        padding-top 5%
        font-size 30px
        span
          font-size 14px
          display block
          line-height 2
      .img
        width 200px
        height 200px
        border-radius 50%
        background #333
        margin 0 auto
        position absolute
        outline none
        bottom 46%
        left 50%
        transform translateX(-100px)
        & > img
          width 90%
          height 90%
          padding 5%
          border-radius 50%
          animation: rotation 10s linear infinite;
          -moz-animation: rotation 10s linear infinite;
          -webkit-animation: rotation 10s linear infinite;
          -o-animation: rotation 10s linear infinite;
    .songList
      position: fixed;
      top: 0;
      z-index: 100;
      width: 100%;
      max-width 600px
      margin 0 auto
      height: 100%;
      overflow: auto;
      transition: all .5s;
      background: rgba(7, 17, 27, .4);
      backdrop-filter: blur(10px); /*ios 模糊背景*/
      .items
        background #ffffff
        width 100%
        height 55%
        margin 0 auto
        padding 60px 0 0 0
        overflow auto
        border-bottom 2px #000 solid
        .title
          width 100%
          max-width 600px
          height 50px
          line-height 50px
          background #FFF
          border-bottom 1px #DDD solid
          text-align center
          padding-top 10px
          font-size 18px
          position fixed
          top 0
          #close
            float right
            width 40px
            & > img
              width 40px
        .item
          width 100%
          margin 0 auto
          border-top 1px #EEE solid
          line-height 2
          & > a
            color black
            padding 8px 5%
            display block
            width 90%
            & > span
              display inline-block
              width 50%
            .l
              float left
              font-size 16px
            .r
              float right
              text-align right
              font-size 12px
              color #CCC
  @-webkit-keyframes rotation
    from
      transform rotate(0deg)
    to
      -webkit-transform rotate(360deg)
  .fade-enter-active
    transition all .3s ease
  .fade-leave-active
    transition all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0)
  .fade-enter, .fade-leave-active
    transform translateY(-20px)
    opacity 0
</style>
