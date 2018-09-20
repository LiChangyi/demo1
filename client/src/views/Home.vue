<template>
  <div class="home-page">
    <h1>Vue+Koa+Mongodb 小练习</h1>
    <CommentBox
    @add_data=add_data></CommentBox>
    <div class="comment-list">
      <Loading v-show="loading_mark"></Loading>
      <p class="comment-list-title">留言列表:</p>
      <CommentItem
       v-for="(item,index) in comment_data" :key="index"
       :item=item
       @delete_this=delete_this></CommentItem>
      <Paginator
      :cur_page=this.pagination.page
      :page_size=this.pagination.size
      :total=this.pagination.total
      @jump=jump
      v-show="!loading_mark"
      ></Paginator>
    </div>
  </div>
</template>

<script>
import CommentBox from "@/components/CommentBox"
import CommentItem from '@/components/CommentItem'
import Paginator from '@/components/Paginator'
import Loading from '@/components/Loading'

export default {
  components: {CommentBox,CommentItem,Paginator,Loading},
  async created(){
    this.get_data();
  },
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      comment_data: [],
      loading_mark: true
    }
  },
  methods: {
    async get_data(){
      this.loading_mark = true;
      this.comment_data = [];
      let res = await this.$http.api_get_leave(this.pagination);
      let {code, msg, data} = res.data;
      if(code == 200){
        this.comment_data = data.list;
        this.pagination = data.pagination;
      }
      this.loading_mark = false;
    },
    delete_this(id){
      let index = this.comment_data.findIndex(item => item._id === id);
      if(index >= 0){
        this.comment_data.splice(index, 1);
        this.pagination.total = Number(this.pagination.total) -1;
      }
    },
    add_data(data){
      this.comment_data.unshift(data);
      this.pagination.total = Number(this.pagination.total) + 1;
    },
    jump(num){
      this.pagination.page = Number(num);
      this.get_data();
    }
  }
}
</script>

<style lang="scss">
.home-page {
  h1{
    margin-bottom: 20px;
  }
  .comment-list {
    width: 800px;
    margin: auto;
    position: relative;
    // overflow: hidden;
  }
  .comment-list-title {
    text-align: left;
    font-size: 22px;
    font-weight: bold;
    margin: 20px auto;  
  }
}
</style>
