<template>
    <div id="movie-detail">
      <div class="top">
        <span class="icon-back" @click="$router.go(-1)"></span>
        <span class="name">{{jsonData.name}}</span>
      </div>
      <div class="info">
        <img :src="server+jsonData.poster" alt="">
        <div class="describe">
          <div class="name">{{jsonData.name}}</div>
          <div class="small type">类型：{{jsonData.type}}</div>
          <div class="small ellipsis">主演：{{jsonData.actor}}</div>
          <div class="small play-time">片长：{{jsonData.movie_long}}</div>
          <div class="small show-time">上映：{{jsonData.public_date}}上映</div>
        </div>
      </div>
      <div class="intro">
        <div class="title">简介</div>
        <div class="content">
          {{jsonData.intro}}
        </div>
      </div>
      <div class="buy">
        <div class="btn" @click="$router.push({path:'/select_cinema',query:{movie_id:$route.query.movie_id}})">特惠购票</div>
      </div>
    </div>
</template>

<script>
    import {
      getMovieDetail,
    } from '../../api/index'
    import Vue from 'vue'
    import {Indicator} from 'mint-ui'
    import {Rate} from  'element-ui'
    Vue.use(Rate)
    export default {
        name: "MovieDetail",
        data(){
          return{
            starValue:0,
            averageScore:0,
            commentNum:0,
            //服务器地址
            server:'http://localhost:3000',
            jsonData:[],
            currentUserCommentDate:[],
            otherUserCommentDate:[],
          }
        },
        created(){
          Indicator.open('Loading...');
          this.loadMovieDetail();
        },
        methods:{
          //加载电影详细信息
          async loadMovieDetail(){
            if(this.$route.query.movie_id){
              let json = await getMovieDetail(this.$route.query.movie_id);
              if (json.success_code===200){
                this.jsonData = json.data[0];
                //判断电影是否上映
                new Date()-new Date(this.jsonData.public_date)>=0?this.isShowMovie = true:this.isShowMovie = false;
                if (this.isShowMovie){
                  this.starValue = this.jsonData.score*0.5.toFixed(1);
                }
              }
            }
            Indicator.close();
          },
        }
    }
</script>

<style scoped lang="stylus" ref="stylesheet/stylus">
  #movie-detail
    width 100%
    height 100%
    background linear-gradient(to bottom, #242342 0%,#11182B 100%)
    color #fff
    font-size .3125rem
    padding 1rem 0
    .top
      width 100%
      height 1rem
      display flex
      justify-content center
      align-items center
      margin-bottom .2rem
      position fixed
      top 0
      left 0
      background-color #242342
      z-index 999
      .icon-back
       font-size .4rem
       position absolute
       left .3rem
    .info
      width 90%
      padding .2rem
      box-sizing border-box
      height 3rem
      margin 1rem auto 0
      background-color #fff
      display flex
      justify-content space-around
      align-items center
      color #333
      border-radius .2rem
      img
        width 28%
        box-sizing border-box
        border-radius .05rem
      .describe
        width 62%
        .name
          font-size .345rem
          font-weight 700
          margin-bottom .12rem
        .small
          font-size .28rem
          margin-bottom .12rem
    .intro
      width 90%
      height auto
      margin .2rem auto
      box-sizing border-box
      padding .25rem
      background-color #3a3a52
      border-radius .2rem
      .title
        font-weight bolder
        color #fff
        margin-bottom .25rem
      .content
        text-align justify
        font-size .28rem
        line-height .4rem
    .buy
      position fixed
      bottom 0
      width 100%
      height 1rem
      background-color #fff
      color #fff
      display flex
      justify-content center
      align-items center
      .btn
        width 90%
        height .8rem
        background-color #dd2727
        display flex
        justify-content center
        align-items center
        border-radius .4rem
        font-weight light
</style>
