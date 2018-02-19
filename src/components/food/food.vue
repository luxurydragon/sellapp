<template>
  <div class="food" v-show="showFlag" ref="food">
    <transition name="move">
      <div class="food-content">
        <div class="image-header">
          <img v-bind:src="food.image" />
          <div class="back" v-on:click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol v-bind:food="food" v-on:cartAdd="_drop"></cartcontrol>
          </div>
          <div class="buy" v-show="!food.count || food.count===0" v-on:click.stop.prevent="addFirst">加入购物车</div>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingselect v-bind:ratings="food.ratings" v-bind:select-type="selectType" v-bind:only-content="onlyContent" v-bind:desc="desc" v-on:ratingTypeSelect="selectTypeChange" v-on:contentToggle="onlyContentChange"></ratingselect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li class="rating-item" v-for="rating in food.ratings" v-bind:key="rating.rateTime" v-show="needShow(rating.rateType,rating.text)">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img class="avatar" width="12" height="12" v-bind:src="rating.avatar" />
                </div>
                <div class="time">{{formatDate(rating.rateTime)}}</div>
                <p class="text">
                  <span v-bind:class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
  import Vue from 'vue';
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  import split from '../split/split.vue';
  import ratingselect from '../ratingselect/ratingselect.vue';

  const ALL = 2;

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },        
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = false;
        this.$nextTick(() => {
          if(!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {click: true});
          }else {
            this.scroll.refresh();
          }
        });
      },
      hide() {
        this.showFlag = false;
      },
      addFirst(event) {
        this.$emit('cartAdd', event.target);
        Vue.set(this.food, 'count', 1);
      },
      _drop(target) {
        this.$parent.$refs.shopcart.drop(target);
      },
      selectTypeChange(val) {
        this.selectType = val;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      onlyContentChange(val) {
        this.onlyContent = val;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      needShow(type, text) {
        if(this.onlyContent && !text) {
          return false;
        }
        if(this.selectType === ALL) {
          return true;
        }else {
          return this.selectType === type;
        }
      },
      formatDate(time) {
        let date = new Date(time);
        return date.getFullYear() + '-' + date.getMonth() + '-' + date.getDate() + ' ' + date.getHours() + ':' + date.getMinutes();
        /* let date = new Date(time);
        let fmt = 'yyyy-MM-dd hh:mm';
        if(/(y+)/.test(fmt)) {
          fmt = fmt.replace(RegExp.$1, (date.getFullYear()+'').substr(4-RegExp.$1.length));
        }
        let o = {
          'M+': date.getMonth() + 1,
          'd+': date.getDate(),
          'h+': date.getHours(),
          'm+': date.getMinutes(),
          's+': date.getSeconds()
        };
        for(let key in o){
          if(new RegExp('(' + key + ')').test(fmt)) {
            let str = o[key] + '';
            fmt = fmt.replacr(RegExp.$1, (RegExp.$1.length === 1) ? str : ('00' + str).substr(str.length));
          }
        }
        return fmt; 这一段字符串匹配可抽象成一个通用模块来被调用 */
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .food
    position: fixed
    top: 0
    left: 0
    bottom: 48px
    z-index: 30
    width: 100%
    background: #fff
    transform: translate3d(0,0,0)
    transition: all 0.2s linear
    &.move-enter, &.move-leave-to
      transform: translate3d(100%,0,0)
    .food-content
      .image-header
        position: relative
        width: 100%
        height: 0
        padding-top: 100% // padding-top或padding-bottom设为100%会默认高度和宽度相等
        img
          position: absolute
          top: 0
          left: 0
          width: 100%
          height: 100%
        .back
          position: absolute
          top: 10px
          left: 0
          .icon-arrow_lift
            display: block
            padding: 10px
            font-size: 20px
            color: #fff
      .content
        position: relative
        padding: 18px
        .title
          line-height: 14px
          margin-bottom: 8px
          font-size: 14px
          font-weight: 700
          color: rgb(7,17,27)
        .detail
          margin-bottom: 18px
          height: 10px
          line-height: 10px
          font-size: 0
          .sell-count, .rating
            font-size: 10px
            color: rgb(147,153,159)
          .sell-count
            margin-right: 12px
        .price
          font-weight: 700
          line-height: 24px
          .now
            margin-right: 8px
            font-size: 14px
            color: rgb(240,20,20)
          .old
            text-decoration: line-through
            font-size: 10px
            color: rgb(147,153,159)
        .cartcontrol-wrapper
          position: absolute
          right: 12px
          bottom: 12px
        .buy
          position: absolute
          right: 18px
          bottom: 18px
          z-index: 10
          height: 24px
          line-height: 24px
          padding: 0 12px
          box-sizing: border-box
          border-radius: 12px
          font-size: 10px
          color: #fff
          background: rgb(0,160,220)
      .info
        padding: 18px
        .title
          line-height: 14px
          margin-bottom: 6px
          font-size: 14px
          color: rgb(7,17,27)
        .text
          line-height: 24px
          padding: 0 8px
          font-size: 12px
          color: rgb(77,85,93)
      .rating
        padding-top: 18px
        .title
          line-height: 14px
          margin-left: 18px
          font-size: 14px
          color: rgb(7,17,27)
        .rating-wrapper
          padding: 0 18px
          .rating-item
            position: relative
            padding: 16px 0
            border-1px(rgba(7,17,27,0.1))
            .user
              position: absolute
              top: 16px
              right: 0
              line-height: 12px
              font-size: 0
              .name
                display: inline-block
                vertical-align: top
                margin-right: 6px
                font-size: 10px
                color: rgb(147,153,159)
              .avatar
                border-radius: 50%
            .time
              margin-bottom: 6px
              line-height: 12px
              font-size: 10px
              color: rgb(147,153,159)
            .text
              line-height: 16px
              font-size: 12px
              color: rgb(7,17,27)
              .icon-thumb_up, .icon-thumb_down                
                margin-right: 4px
                line-height: 16px
                font-size: 12px
              .icon-thumb_up
                color: rgb(0,160,220)
              .icon-thumb_down
                color: rgb(147,153,159)
          .no-rating
            padding: 16px 0
            font-size: 12px
            color: rgb(147,153,159)
</style>
