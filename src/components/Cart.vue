<template>
  <b-container>
    <b-row>
      <b-col cols class="cartcontent">
        <b-table :items="items"  :fields="fields" class="mt-2">
          <template v-slot:cell(index)="data">
              {{data.index+1}}
          </template>
          <template v-slot:cell(bookid)="data">
              <b-link :to="{name:'book',params:{id:data.value}}">{{data.value}}</b-link>
          </template>
          <template v-slot:cell(bookname)="data">
              <b-link :to="{name:'book',params:{id:data.item.bookid}}">{{data.value}}</b-link>
          </template>
          <template v-slot:cell(quantity)="data">
            <b-input-group>
              <b-button class="little-btn" size="sm" slot="prepend" @click="add(data.item.bookid)">+</b-button>
              <span style="width:2rem">{{data.value}}</span>
              <b-button class="little-btn" size="sm" slot="append" @click="substract(data.item.bookid)">-</b-button>
            </b-input-group>
          </template>
          <template v-slot:cell(total)="data">
              {{data.item.price*data.item.quantity}}
          </template>
        </b-table>
        <h5 class="text-right pr-2">合计:<strong>{{total}}</strong>元</h5>
        <transition name="slide-fade">
          <b-alert  style="height:2rem" :variant="checkoutStatus?'success':'danger'" :show="msg">
            <b style="position:relative;top:-0.6rem;font-size:0.8rem">{{msg}}</b>
          </b-alert>
        </transition>
        <b-form-row class="mt-2">
        <b-col>
          <b-button block v-bind:class="{enable:checkoutStatus}" @click="checkout" variant="success">结算</b-button>
        </b-col><b-col>
          <b-button block v-bind:class="{enable:items}" @click="clearAll" variant="outline-danger">清空</b-button>
        </b-col>
        </b-form-row>
      </b-col>
    </b-row>
  </b-container>
</template>
<script>
import {mapState,mapGetters,mapMutations} from 'vuex'
export default {
    data(){
        return{
            fields:[
                {key:'index',label:'序号'},
                {key:'bookname',label:'书名'},
                {key:'bookid',label:'书籍编号'},
                {key:'price',label:'单价',sortable:true},
                {key:'quantity',label:'数量',sortable:true},
                {key:'total',label:'总价'}
            ]
        }
    },
  computed: {
    ...mapState({
      items: state=>state.items,
      checkoutStatus: state => state.checkoutStatus,
      msg:state=>state.msg
    }),
    ...mapGetters({
      total: 'cartTotal',
    })
  },
  methods: {
    clearAll(){
        this.$store.commit('setCartItems',{items:[]})
    },
    checkout () {
      this.$eventHub.$emit('updateOrder',this.items);
      this.$store.dispatch('checkout')
    },
    ...mapMutations({
        add:'incrementItemQuantity',
        substract:'decrementItemQuantity'
    })
  }
}
</script>
<style lang="less">
.cartcontent{
    width: 35rem!important;
    background-color: white;
    padding: 15px;
}
.little-btn{
    width: 1.5rem;
    height: 1.5rem;
    padding: 1px!important;
}
</style>

