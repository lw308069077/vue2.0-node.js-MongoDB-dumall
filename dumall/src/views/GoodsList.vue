<template>
  <div>
    <!-- <nav-header></nav-header> -->
    <nav-bread>
        <span>Goods</span>
    </nav-bread>
    <div class="accessory-result-page accessory-page">
        <div class="container">
            <div class="filter-nav">
            <span class="sortby">Sort by:</span>
            <a href="javascript:void(0)" class="default cur">Default</a>
            <a @click="sortGoods" href="javascript:void(0)" class="price">Price <svg class="icon icon-arrow-short"><use xlink:href="#icon-arrow-short"></use></svg></a>
            <a href="javascript:void(0)" class="filterby stopPop">Filter by</a>
            </div>
            <div class="accessory-result">
            <!-- filter -->
            <div class="filter stopPop" id="filter">
                <dl class="filter-price">
                <dt>Price:</dt>
                <dd><a href="javascript:void(0)" @click="setPriceFilter('all')" v-bind:class="{'cur':priceChecked=='all'}">All</a></dd>
                <dd v-for="(item,index) in priceFilter">
                  <a href="javascript:void(0)" @click="setPriceFilter(index)" v-bind:class="{'cur':priceChecked==index}">{{item.startPrice}} - {{item.endPrice}}</a>
                </dd>
                </dl>
            </div>

            <!-- search result accessories list -->
            <div class="accessory-list-wrap">
                <div class="accessory-list col-4">
                    <ul>
                        <li v-for="item in goodsList">
                        <div class="pic">
                            <a href="#"><img :src="'/static/'+item.prodcutImg" alt=""></a>
                        </div>
                        <div class="main">
                            <div class="name">{{item.productName}}</div>
                            <div class="price">￥ {{item.prodcutPrice}}</div>
                            <div class="btn-area">
                            <a href="javascript:;" class="btn btn--m">加入购物车</a>
                            </div>
                        </div>
                        </li>
                    </ul>

                    <div class="load-more" v-infinite-scroll="loadMore" infinite-scroll-disabled="busy" infinite-scroll-distance="10">
                        <img src="./../assets/loading-spinning-bubbles.svg" v-show="loading">
                    </div>
                </div>
            </div>
            </div>
        </div>
        </div>
        <nav-footer></nav-footer>
  </div>
</template>
<script>
import "./../assets/css/base.css";
import "./../assets/css/product.css";
import NavHeader from "@/components/Header";
import NavFooter from "@/components/Footer";
import NavBread from "@/components/Bread";
import axios from "axios";

export default {
  data() {
    return {
      goodsList: [],
      sortFlag: true,
      page: 1,
      pageSize: 8,
      busy: true,
      loading:false,
      priceFilter: [
        {
          startPrice: "0.00",
          endPrice: "100.00"
        },
        {
          startPrice: "100.00",
          endPrice: "500.00"
        },
        {
          startPrice: "500.00",
          endPrice: "1000.00"
        },
        {
          startPrice: "1000.00",
          endPrice: "5000.00"
        }
      ],
      priceChecked: "all"
    };
  },
  components: {
    NavHeader,
    NavFooter,
    NavBread
  },
  mounted: function() {
    this.getGoodsList();
  },
  methods: {
    getGoodsList(flag) {
      var param = {
        page: this.page,
        pageSize: this.pageSize,
        sort: this.sortFlag ? 1 : -1,
        priceLevel: this.priceChecked
      };
      this.loading = true;
      axios.get("/api/goods", { params: param }).then(res => {
        var res = res.data;
        this.loading = false;
        if (res.status == "0") {
          if (flag) {
            this.goodsList = this.goodsList.concat(res.result.list);
            if (res.result.count == 0) {
              this.busy = true;
            } else {
              this.busy = false;
            }
          } else {
            this.goodsList = res.result.list;
            this.busy = false;
          }
        } else {
          this.goodsList = [];
        }
      });
    },
    sortGoods() {
      this.sortFlag = !this.sortFlag;
      this.page = 1;
      this.getGoodsList();
    },
    setPriceFilter(index) {
      this.priceChecked = index;
      this.page = 1;
      this.getGoodsList();
    },
    loadMore() {
      this.busy = true;
      setTimeout(() => {
        this.page++;
        this.getGoodsList(true);
      }, 500);
    }
  }
};
</script>
<style>
.accessory-list ul::after {
  clear: both;
  content: "";
  height: 0;
  display: block;
  visibility: hidden;
}
.load-more {
  height: 100px;
  line-height: 100px;
  text-align: center;
}
</style>
