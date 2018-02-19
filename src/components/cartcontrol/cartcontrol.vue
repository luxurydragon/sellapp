<template>
  <div class="cartcontrol">
    <div class="cart-decrease icon-remove_circle_outline" v-show="food.count>0" v-on:click.stop.prevent="decreaseCart"></div>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" v-on:click.stop.prevent="addCart"></div>
  </div>
</template>

<script>
  import Vue from 'vue';

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1); // 设置对象的属性。如果对象是响应式的，确保属性被创建后也是响应式的，同时触发视图更新。这个方法主要用于避开 Vue 不能检测属性被添加的限制。
        }else {
          this.food.count++;
        }
        this.$emit('cartAdd', event.target); // this.$emit用来分发事件到父组件
      },
      decreaseCart() {
        if (this.food.count) {
          this.food.count--;
        }
      }
    } 
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size: 0
    .cart-decrease, .cart-add
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px
      color: rgb(0,160,220)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      line-height: 24px
      text-align: center
      font-size: 10px
      color: rgb(147,153,159)
    .cart-add
      color: rgb(0,160,220)
</style>
