<template>
  <div class="ratingselect">
    <div class="rating-type">
      <span class="block positive" v-bind:class="{'active':selectType===2}" v-on:click="select(2)">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span class="block positive" v-bind:class="{'active':selectType===0}" v-on:click="select(0)">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span class="block negative" v-bind:class="{'active':selectType===1}" v-on:click="select(1)">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div class="switch" v-bind:class="{'on':onlyContent}" v-on:click="toggleContent">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;

  export default {
    props: {
      ratings: {
        type: Array,
        default() {
          return [];
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc: {
        type: Object,
        default() {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          };
        }
      }
    },
    data() {
      return {
        selectTypeChanged: this.selectType,
        onlyContentChanged: this.onlyContent
      };
    },
    computed: {
      positives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE;
        });
      },
      negatives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE;
        });
      }
    },
    methods: {
      select(type) {
        this.selectTypeChanged = type;
      },
      toggleContent() {
        this.onlyContentChanged = !this.onlyContentChanged;
      }
    },
    watch: {
      selectType(val) {
        this.selectTypeChanged = val;
      },
      onlyContent(val) {
        this.onlyContentChanged = val;
      },
      selectTypeChanged(val) {
        this.$emit('ratingTypeSelect', val);
      },
      onlyContentChanged(val) {
        this.$emit('contentToggle', val);
      }
    } // vue2.x的props是单向绑定的，父组件属性发生变化将传递给子组件，反过来则不会，避免了子组件对父组件状态的修改，因此双向绑定得自己完成。（子组件只能被动接收父组件传递过来的数据，并且在子组件内，不能修改由外层传来的props数据）当需要将子组件props内容修改结果返回给父组件时，就要在data中创建props的副本并且在watch里监听props的变化同步props副本的修改，最后将props副本分发给父组件，父组件监听事件获得结果。
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .ratingselect
    .rating-type
      padding: 18px 0
      margin: 0 18px
      border-1px(rgba(7,17,27,0.1))
      font-size: 0
      .block
        display: inline-block
        padding: 8px 12px
        margin-right: 8px
        line-height: 16px
        border-radius: 1px
        font-size: 12px
        color: rgb(77,85,93)
        &.active
          color: #fff
        .count
          margin-left: 2px
          font-size: 8px
        &.positive
          background: rgba(0,160,220,0.2)
          &.active
            background: rgb(0,160,220)
        &.negative
          background: rgba(77,85,93,0.2)
          &.active
            background: rgb(77,85,93)
    .switch
      padding: 12px 18px
      line-height: 24px
      border-bottom: 1px solid rgba(7,17,27,0.1)
      font-size: 0
      color: rgb(147,153,159)
      &.on
        .icon-check_circle
          color: #00c850 
      .icon-check_circle
        vertical-align: top
        margin-right: 4px
        font-size: 24px
      .text
        vertical-align: top
        font-size: 12px 
</style>
