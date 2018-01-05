<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li class="menu-item" v-for="(item, index) in goods" :key="index" :class="{'current': currentIndex === index}" @click="selectMenu(index, $event)">
          <span class="text border-1px"><span v-show="item.type > 0" class="icon" :class="classMap[item.type]"></span>{{item.name}}</span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li class="food-list food-list-hook" v-for="(item, index) in goods" :key="index">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li class="food-item border-1px" v-for="(food, index) in item.foods" :key="index">
              <div class="icon"><img :src="food.icon" width="57" height="57" alt=""></div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
  </div>
</template>

<script>
import axios from 'axios'
import BScroll from 'better-scroll'
import shopcart from '@/components/shopcart/shopcart'
import cartcontrol from '@/components/cartcontrol/cartcontrol'

const ERR_OK = 0

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0
    }
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i
        }
      }
      return 0
    }
  },
  created() {
    this.classMap = ['deecrease', 'discount', 'special', 'invoice', 'guarantee']

    axios.get('/api/goods').then(res => {
      const result = res.data
      if (result.errno === ERR_OK) {
        this.goods = result.data
        this.$nextTick(() => {
          this._initScroll()
          this._calculateHeight()
        })
      }
    })
  },
  methods: {
    _initScroll() {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        probeType: 3
      })

      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    _calculateHeight() {
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let item of foodList) {
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    selectMenu(index, event) {
      if (!event._constructed) {
        return
      }
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el, 300)
    }
  },
  components: {
    shopcart,
    cartcontrol
  }
}
</script>

<style lang="stylus" scoped>
@import '../../common/stylus/mixin'

.goods
  display: flex;
  position: absolute;
  left: 0;
  top: 174px;
  bottom: 46px;
  width: 100%;
  overflow: hidden;
  .menu-wrapper
    flex: 0 0 80px;
    width: 80px;
    background-color: #f3f5f7;
    .menu-item
      display: table;
      height: 54px;
      width: 56px;
      padding: 0 12px;
      line-height: 14px;
      &.current
        position: relative;
        margin-top: -1px;
        z-index: 10;
        background-color: #fff;
        font-weight: 700;
        .text
          border-none();
      .icon
        display: inline-block;
        vertical-align: top;
        width: 12px;
        height: 12px;
        margin-right: 2px;
        background-size: 12px;
        &.decrease
          bg-image('decrease_3');
        &.discount
          bg-image('discount_3');
        &.guarantee
          bg-image('guarantee_3');
        &.invoice
          bg-image('invoice_3');
        &.special
          bg-image('special_3');
      .text
        display: table-cell;
        width: 56px;
        vertical-align: middle;
        border-1px(rgba(7, 17, 27, 0.1));
        font-size: 12px;
  .foods-wrapper
    flex: 1;
    .title
      padding-left: 14px;
      height: 26px;
      line-height: 26px;
      border-left: 2px solid #d9ddee;
      font-size: 12px;
      color: rgb(147, 153, 159);
      background-color: #f3f5f7;
    .food-item
      display: flex;
      margin: 18px;
      padding-bottom: 18px;
      border-1px(rgba(7, 17, 27, 0.1));
      &:after
        border-none();
        margin-bottom: 0;
      .icon
        display: 0 0 57px;
        margin-right: 10px;
      .content
        flex: 1;
        .name
          margin: 2px 0 8px 0;
          height: 14px;
          line-height: 14px;
          font-size: 14px;
          color: rgb(7, 17, 27);
        .desc, .extra
          line-height: 10px;
          font-size: 10px;
          color: rgb(147, 153, 159);
        .desc
          margin-bottom: 8px;
          line-height: 12px;
        .extra
          .count
            margin-right: 12px;
        .price
          font-weight: 700;
          line-height: 24px;
          .now
            margin-right: 8px;
            font-size: 14px;
            color: rgb(240, 20, 20);
          .old
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147, 153, 159);
        .cartcontrol-wrapper
          position: absolute;
          right: 0;
          bottom: 12px;
</style>


