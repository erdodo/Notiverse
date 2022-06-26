<template>
  <div>
    <div class="container p-5">
      <div v-if="parent_id != 0">
        <button class="btn btn-primary" @click="parent_id = grand_id">
          Geri gel {{ parent_id }}
        </button>
      </div>
      <h1> {{detail.title}} </h1>
      <div
        v-if="detail"
        class="mt-5"
      >
      <textEditor 
        v-if="detail.content" 
        :content="detail.content" 
        :parent_id="parent_id" 
        @goToChild="goToChildEditor($event)"
        @newChild="newChild($event)"/>
      </div>
      <div v-if="parent_id == 0">
        <div v-for="r in records" :key="r" class="card p-3 my-3">
          <h4 class="cursor-pointer" @click="goToChildList(r)">
            {{ r.title }}
          </h4>
        </div>
      </div>
    </div>
    <div>
      
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from "axios";
import textEditor from '@/components/textEditor.vue'

export default {
  name: "Home",
  data() {
    return {
      records: {},
      parent_id: 0,
      detail: {},
      editor:null
    };
  },
  created() {
    this.getList();
  },
  mounted(){
  },
  methods: {
    getList() {
      var filters = {};
      if (this.parent_id == 0) {
        filters = {
          notiverse_id: { type: 100, guiType: "select", filter: null },
        };
      } else {
        filters = {
          notiverse_id: {
            type: 1,
            guiType: "select",
            filter: [this.parent_id],
          },
        };
      }
      axios
        .post(
          "https://192.168.1.57/api/v1/vjlNVizwxFoTEEK8d1/tables/notiverse",
          {
            params: JSON.stringify({
              page: 1,
              limit: "10",
              column_array_id: "0",
              column_array_id_query: "0",
              sorts: {},
              filters: filters,
            }),
          }
        )
        .then((res) => {
          this.records = res.data.data.records;
        });
    },
    getData() {
      axios
        .post(
          "https://192.168.1.57/api/v1/vjlNVizwxFoTEEK8d1/tables/notiverse/" +
            this.parent_id,
          { params: '{"column_set_id":"0"}' }
        )
        .then((res) => {
          this.detail = res.data.data.record;
        });
    },
    goToChildList(r) {

      this.grand_id = this.parent_id;
      this.parent_id = r.id;
    },
    goToChildEditor(data){
      this.grand_id = this.parent_id;
      this.parent_id=data.action
    },
    newChild(id){
      this.parent_id=id;
    }
  },
  watch: {
    parent_id() {
      this.getList();
      this.getData();
    },
  },
  components:{
    textEditor
  }
};
</script>
<style>
.child {
  border: 1px solid black;
  padding: 13px;
  border-radius: 5px;
  cursor: pointer;
  width: 300px;
}
table,
tr,
th,
td {
  border: 1px solid rgb(116, 116, 116);
}
</style>
