<template>
  <div>
   <EditItem v-on:childMethod="getItems" :opType="opType"></EditItem>
   
   <div class="items">
     <div class="items-box">
      <div class="item-list box-shadow" v-for="item in items" @click="toDetail(item.id, false, item.status)">
        <div class="img">
         <img v-bind:src="item.imgPath" alt="">
         <div class="no-img" v-if="item.imgPath == ''">
          <img src="../assets/img/notice.png">
          <br/><br/>
          <span>商家没有上传图片</span>
        </div>
      </div>
      <div class="img-cover">
       <div class="operate">
        <div v-if="item.status == 1">
          <i class="edit icon" @click='modify(item.id)'></i>
          <i class="del icon" @click="del(item.id, item.title)"></i>
        </div>
        <div v-else class="sale-text">已卖出</div>
      </div>
    </div>
    <p class="title">
      <span>{{item.title}}</span>
      <span>{{item.level}} 成新</span></p>
      <p class="price">￥ {{item.price}}</p>
    </div>
    <div class="item-list add-btn" @click="addItem"></div>
  </div>
</div>
</div>
</template>

<script>
  import EditItem from '@/components/editItem';
  export default {
   components: { 
    EditItem
  },
  data () {
    return {
    	items: [],
    	opType: ''         //传入addItem子组件的信息，为数字的时候为修改，数字表示商品的id，add增加
    }
  },
  mounted: function() {
  	this.getItems('');
  },
  watch: {
  	opType(val) {
  		this.opType = val;
  	}
  },
  methods: {
  	getItems(val) {
     if(val !== ''){
      this.opType = val;
    } else {
      var _this = this;
      $.ajax({
        url: "/api/item/getItems",
        type: "get",
        data: {getPublish: true},
        beforeSend: function(xhr) {
          _this.myFun.setToken(xhr);
        },
        success: function(data){
         for (var i = 0; i < data.length; i++) {
           var src = data[i].imgPath.split('&')[0].substring(1);
           for (var key in data[i]) {
            if(key == 'imgPath') {
             data[i][key] = src;
           }
         }
       }
       _this.items = data;
     },
     error: function(error) {
       console.log("获取资源失败");
       _this.myFun.tokenExpired(error)
     }
   });
    }
  },
  toDetail(id, isCollect, status) {
    if(status == 1) {
      this.$router.push({
        path:'/index/detail',
        query: { id: id, isCollect: isCollect }
      })
    }
  },
  addItem() {
    this.opType = 'add';
    $('.items').animate({"left":"1200px"});
    $('.edit-item').animate({"left": "340px"});
  },
  modify(id) {
    this.opType = id;
    $('.items').animate({"left":"1200px"});
    $('.edit-item').animate({"left": "340px"});
  },
  del(id, title) {
    var _this = this;
    $.ajax({
      url: "/api/item/editItem",
      type: "get",
      data: {itemId: id, itemTitle: title, type: 0},
      beforeSend: function(xhr) {
        _this.myFun.setToken(xhr);
      },
      success: function(data){
       _this.getItems('');
     },
     error: function(error) {
      _this.myFun.tokenExpired(error)
      _this.myFun.showMsg("删除失败", 0);
    }
  });
  }
}
}
</script>

<style lang="less"> 
.sale-text {
  padding: 5px 10px;
}
</style>