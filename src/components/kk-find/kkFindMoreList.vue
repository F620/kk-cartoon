<template>
<div class="kkFindMoreList">
  <div class="topNav">
    <img @click="goBack" class="back" src="../../assets/kk-find/kk-find-back.png" alt="<">
    {{countMoreList['1']}}
  </div>
  <div  class="wrap"  v-infinite-scroll="loadMore"
        infinite-scroll-disabled="loading"
        infinite-scroll-distance="100">
    <div v-for="myTopic in topics" class="box" @touchmove="changeGo1" @touchend="changeGo2(myTopic['id'])">
      <div class="bLeft">
        <img v-lazy="myTopic['cover_image_url']">
      </div>
      <div class="bRight">
        <button class="care">+关注</button>
        <p class="title">{{myTopic['title']}}</p>
        <p v-show="true" class="tags">
          <span v-for="tag in myTopic['category']">{{tag}}</span>
        </p>
        <p v-show="false" class="author">{{myTopic['user']['nickname']}}</p>
        <div class="jie">
            <span class="zan">
              <img src="../../assets/kk-find/kk-find-zan.png" alt="">
              <span>{{myTopic['likes_count']>100000?Math.floor(myTopic['likes_count']/10000)+'万':myTopic['likes_count']}}</span>
            </span>
          <span  class="comment">
              <img src="../../assets/kk-find/kk-find-comment.png" alt="">
              <span>{{myTopic['comments_count']>100000?Math.floor(myTopic['comments_count']/10000)+'万':myTopic['comments_count']}}</span>
            </span>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
  export default {
    name: '',
    data () {
      return {
        topics: [],
        offset: '0',
        diMsg: '',
        loading: false, // 加载状态
        isGo: 1
      }
    },
    methods: {
      loadTop () {
      // 加载更多数据
        this.$refs.loadmore.onTopLoaded()
      },
      HuoQuListSort () {
        this.$request({
          type: 'get',
          // api.kuaikanmanhua.com/v1/topic_new/discovery_module_list/262?offset=20&limit=20&style=3
          url: 'kuaikanv1/' + this.countMoreList['0'],
          header: {},
          params: {
            offset: this.offset,
            limit: 20,
            style: 3
          },
          success: function (res) {
//            console.log(res['data']['data'])
            // 加载数据
            this.topics = this.topics.concat(res['data']['data']['topics'])
            // 判断是否全部加载
            if (res['data']['data']['topics'].length < 20) {
              this.diMsg = '全部加载'
              console.log(this.diMsg)
            }
            this.loading = false
          },
          failed: function (err) {
            console.log(err)
          }
        })
      },
      loadMore () {
        if (!this.loading) {
          this.loading = true
          if (this.diMsg === '') {
            this.offset += 20
            setTimeout(() => {
              this.HuoQuListSort()
            }, 2000)
          }
        }
      },
      goBack () {
        window.history.back()
//        this.$router.push({path: '/' + this.countMoreList['2']})
      },
      changeGo1 () {
        this.isGo = 0
        // 默认定义了
      },
      changeGo2 (tarId) {
        if (this.isGo === 1) {
          this.cartoonGo(tarId)
        } else {
          this.isGo = 1
        }
      },
      cartoonGo (tarId) {
        this.$router.push({name: 'kkcartoontitle', params: {id: tarId}})
      }
    },
    mounted () {
      this.loading = true
      this.HuoQuListSort()
    },
    computed: {
      countMoreList () {
        return this.$store.state.countMoreList
      }
    }
  }
</script>

<style scoped lang=less>
  .topNav{
    text-align: center;
    line-height: 40px;
    font-size: 17px;
    width: 100%;
    height: 40px;
    position: fixed;
    top: 0;
    z-index: 20;
    background-color: #fff;
    border-bottom: 1px solid #ccc;
  }
  .topNav .back{
    position: absolute;
    top: 30%;
    left: 5%;
    width: 16px;
    height: 16px;
  }
  .wrap{
    margin-top: 10%;
  }
  .box{
    overflow: hidden;
    border-bottom: 1px solid #ccc;
  }
  .bLeft{
    width: 45%;
    min-width: 90px;
    /*height: 117px;*/
    height: 28.2609vw;
    min-height: 117px;
    margin-right: 5%;
    float: left;
    background-image: url(../../assets/kk-find/kk-mhbg.jpg);
    background-repeat:no-repeat;
    -webkit-background-size: cover;
    background-size: cover;
    background-position: center center;
  }
  image[lazy=loading] {}
  .bLeft img{
    width: 100%;
    height:100%;
  }
  .bRight{
    width: 50%;
    float: left;
    padding-top: 2%;
    position: relative;
  }
  .bRight .care{
    position: absolute;
    top: 10%;
    right: 10%;
    padding: 2%;
    border-radius: 10px;
    background-color: yellow;
    outline: none;
    border: 1px solid #eee;
  }
  .bRight .title{
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
  }
  .bRight .author{
    font-size: 13px;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
    color: #aaa;
  }
  .bRight .tags span{
    font-size: 13px;
    color: #aaa;
    margin-right: 2%;
  }
  .bRight .jie{
    margin-top: 17%;
  }
  .bRight .jie img{
    width: 16px;
    height: 16px;
    position: relative;
    top: 3px;
    z-index: 10;
  }
  .bRight .jie span{
    font-size: 13px;
    color: #666;
  }
  .bRight .jie .zan{
    margin-right: 3%;
  }
</style>
