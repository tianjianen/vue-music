<template>
  <div>
    <div class="search">
      <div class="yuyin"></div>
      <div class="input" @click="showlist">
        <input  type="text" value="" placeholder="搜索音乐、歌曲、电台" id="inputvalue" @keyup="search" >
      </div>
      <div class="music" @click="hidelist">
        <span v-show="lshow">取消</span>
        <img src="../../../static/img/ph.png" alt="" v-show="!lshow" @click="openmusicsong">
      </div>
    </div>
    <div class="searchresult" v-show="lshow">
      <ul v-show="!searchname.length">
        <p class="searchname">热门搜索</p>
        <li class="searchhot" v-for="(item, index) in hotsresult" :key="index" @click="search(item.first, true)">
          {{item.first}}
        </li>
      </ul>
      <ul class="list-ul">
        <li v-for="(item, index) in list" @click="opensong(item)" :key="index">
          <div class="img" :class="{'active': number===index}">
            {{index + 1}}
          </div>
          <div class="title border-1px" >
              <span class="music-name" :class="{'active': number===index}">
                {{item.name}}
              </span>
            <p>
              <i v-show="item.sq"></i>
              <span :class="{'active': number===index}">{{item.artists[0].name}} - {{item.album.name}}</span>
            </p>
          </div>
          <div class="menu border-1px" v-show="item.movie">
            <div class="menu-img"></div>
          </div>
          <div class="menu border-1px">
            <div class="menu-img"></div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import axios from 'axios'
import api from '../../api/index'
export default{
  data() {
    return {
      list: [],
      number: -1,
      lshow: false,
      searchname:'',
      hotsresult:[]
    };
  },
  methods: {
     getColor(){
          var colorElements = "0,1,2,3,4,5,6,7,8,9,a,b,c,d,e,f";
          var colorArray = colorElements.split(",");
          var color ="#";
          for(var i =0;i<6;i++){
              color+=colorArray[Math.floor(Math.random()*16)];
          }
          return color;
      },
    showlist() {
      this.lshow = true;
      axios.get(api.getsearchhot()).then((res) => {
        console.log(res.data.result.hots);
        this.hotsresult  = res.data.result.hots;
      })
    },
    hidelist() {
      this.lshow = false;
      this.list = [];
      document.querySelector('#inputvalue').value = '';
      this.searchname = ''
    },
    openmusicsong() {
      var obj = null;
      this.$emit('openmusicsong', obj);
    },
    opensong(item) {
      if (item) {
        this.lshow = false;
      } else {
        item = null;
      }
      var obj = {
        id: item.id,
        migUrl: item.album.artist.img1v1Url,
        name: item.name,
        songname: item.artists[0].name,
        // audiosrc: item.mp3Url
      };
      this.$emit('musicsearch', obj);
    },
    search() {
      var name = ''
      console.log(this.searchname.length);
      if(arguments[1]){
        this.searchname = arguments[0];
        name = arguments[0];
        document.querySelector('#inputvalue').value = name;
      }else{
        name = document.querySelector('#inputvalue').value;
      }
      this.searchname = name;
      console.log(name);
      if(name.length >0){
        setTimeout(() =>{
          axios.get(api.search(name)).then((res) => {
            // console.log(res);
            var data = res.data;
            var obj = data.result.songs;
            // this.list.splice(0, this.list.length);
            this.list = obj;
            console.log(obj);
            // axios.get(api.getPlayListDetail(obj[1].id)).then((res) => {
            //     console.log(res);
            
            // });
          })
        }, 600)

      }

      // this.$http.get('/key/' + name).then((res) => {
      //   var data = res.data.data;
      //   var obj = JSON.parse(data).result.songs;
      //   this.list.splice(0, this.list.length);
      //   for (var i in obj) {
      //     this.$http.get('/detail/' + obj[i].id).then((res) => {
      //       var listdetail = JSON.parse(res.data.data).songs[0];
      //       this.list.push(listdetail);
      //     }).catch((error) => {
      //       console.log('加载歌曲信息出错:' + error);
      //     });
      //   }
      // });
    }
  },
  created(){
    let leftColor = this.getColor();
    let rightColor = this.getColor();
    let style1 = `linear-gradient(to right, ${leftColor} 0%, ${rightColor} 100%)`
    let style2 = `linear-gradient(to right, ${leftColor} 0%, ${rightColor} 100%)`
    document.querySelector('.search').style.cssText = `background-image: ${style1}`;
    // document.querySelector('.menu-title').style.cssText=`background-image:${style2}`;
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .search
    // background:#d43c33
    background-image: linear-gradient(120deg, #89f7fe 0%, #66a6ff 100%);
    height:46px
    display :flex
    .yuyin
      flex-basis:40px
      background:url(../../../static/img/music.png) no-repeat;
      background-position:center center
      background-size: 32px 32px
    .input
      flex:1
      input
        width:80%
        height:30px
        border-radius:5px
        margin-top:8px
        color:#c6c7c9
        font-size:12px
        padding-left:60px
    .music
      flex-basis: 40px
      text-align:center
      span
        line-height: 46px
        color: #ffffff
      img
        width: 20px
        height: 20px
        margin-left: 10px;
        margin-top: 13px;
  .searchresult
    position :absolute
    top: 46px
    left:0
    bottom:0
    width:100%
    background: #ffffff
    z-index:10
    .searchname
      // color: rgba(255, 255, 255, 0.5);
      font-size: 16px
      font-weight bold
      margin-bottom: 10px
      margin-top 10px
      margin-left 10px
    .searchhot
      display: inline-block
      padding: 10px 10px
      border-radius: 10px
      border 2px solid #fcc
      font-size: 16px
      margin-left 10px
      margin-bottom: 10px;
      margin-right: 10px
    .list-ul
      li
        display:flex
        .img
          &.active
            color: #d33a31
          flex-basis:48px
          text-align :center
          line-height: 56px
          color:#999999
        .title
          flex :1
          border-1px(#ddd)
          .music-name
            &.active
              color: #d33a31
            line-height: 32px
            font-size: 18px
            color :#333
          p
            font-size: 16px
            color:#949494
            i
              display :inline-block
              width:16px
              height:16px
              background-image:url(../../../static/img/sq.png)
              background-position:center center
              background-size: 16px 16px
              background-repeat:no-repeat
              vertical-align:middle
            span
              &.active
                color: #d33a31
              vertical-align:middle
              font-size:12px
        .menu
          flex-basis:48px
          margin-top: 12px
          border-1px(#ddd)
          .menu-img
            width:36px
            height:26px
            background-position:center center
            background-size: 18px 18px
            background-repeat:no-repeat
            border:1px solid #d3d4da
            border-radius:4px
        .menu:nth-child(3)
          .menu-img
            background-image:url(../../../static/img/aee.png)
        .menu:nth-child(4)
          .menu-img
            background-image:url(../../../static/img/more.png)
</style>
