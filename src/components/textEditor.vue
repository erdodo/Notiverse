<template>
  <div> 
    <div v-if="json" class="my-5 card p-3 icerik">
        <div v-for="(cnt,key) in this.json" :key="key">
            <input type="text"  
                v-if="cnt.type=='text'"
                class="input"
                :class="cnt.class" 
                :id="'icerik-'+cnt.id" 
                v-model="cnt.value" 
                @keydown.enter="yeniSatır(key)" 
                @keyup.delete="satirSil(key)"
                @keyup="yaziliyor(cnt,$event)"
                >
            <input 
                v-if="cnt.type=='child'"
                :id="'icerik-'+cnt.id" 
                class="child-button"
                :class="cnt.class"
                @click="goToChild(cnt)"
                v-model="cnt.value"
            > 
            <div v-if="selectState[cnt.id] == true" class="select-menu">
                <ul>
                    <li @click="rowChange(key,{id:cnt.id,action:cnt.action,link:cnt.link,type:'text',class:'h-1',value:cnt.value})">Header 1</li>
                    <li @click="rowChange(key,{id:cnt.id,action:cnt.action,link:cnt.link,type:'text',class:'h-2',value:cnt.value})">Header 2</li>
                    <li @click="rowChange(key,{id:cnt.id,action:cnt.action,link:cnt.link,type:'text',class:'h-3',value:cnt.value})">Header 3</li>
                    <li @click="rowChange(key,'page')">Page</li>
                </ul>
            </div>
        </div>
    </div>
    
  </div>
</template>

<script>
import axios from 'axios'
export default {
    props:['content','parent_id'],
    data(){
        return{
            json:JSON.parse(this.content),
            selectState:[]
        }
    },
    methods:{
        yeniSatır(key){
            for(var i = this.json.length; i> key+1;i--){
                this.json[i] = this.json[i-1]
                this.json[i].id =this.json[i].id+1
            }
            this.json[key+1]={id: key+2, action: "",class: "",link: "",type: "text",value: ""}
            
            var yeni_satir_ref='icerik-'+(this.json[key+1].id)
            console.log(yeni_satir_ref);
            setTimeout(() => {document.getElementById(yeni_satir_ref).focus()}, 100);
            this.save();
        },
        satirSil(key){
            if(this.json[key].value==''){
                this.json.splice(key, 1);
            var satir_sil_ref='icerik-'+(this.json[key-1].id)
            setTimeout(() => {
                    document.getElementById(satir_sil_ref).focus()
            }, 100);
            //this.save();
            }
        },
        save(){
            axios.post('https://192.168.1.57/api/v1/vjlNVizwxFoTEEK8d1/tables/notiverse/'+this.parent_id+'/update',{
                content:JSON.stringify(this.json),
                column_set_id:0
            })
        },
        yaziliyor(data,e){
            console.log(this.parent_id,data);
            console.log(e);
            if(data.value.includes('/')){
                this.selectState[data.id]=true;
            }else{
                this.selectState[data.id]=false;
            }
        },
        goToChild(child){
            this.$emit('goToChild',child)
        },
        rowChange(key,value){
            if(value == 'page'){
                axios.post('https://192.168.1.57/api/v1/vjlNVizwxFoTEEK8d1/tables/notiverse/store',{
                    notiverse_id: this.parent_id,
                    title: 'Page',
                    image_old: null,
                    content: JSON.stringify([{"id":1,"value":"","type":"text","class":"","link":"","action":""}]),
                    background_image_old: null,
                    state: 1,
                    column_set_id: 0,
                }).then(res => {
                    this.json[key]={id:this.json[key].id,action:res.data.data.id,link:'',type:'child',class:'',value:'Page'}
                    this.save()
                    this.$emit('newChild',res.data.data.id)
                    
                })
            }else{
                this.json[key]=value;
            }
            
        }
    },
    watch:{
        content(){
            console.log(this.content)
            this.json={};
            this.json=JSON.parse(this.content)
        }
    }
}
</script>

<style>
.icerik .input{
    outline: none;
    border: none;
    width: 100%;
}
.child-button{
    border: 1px solid brown !important;
    width: 100%;
}
.select-menu{
    position: absolute;
    background: aliceblue;
    margin: 20px;
    border-radius: 7px;
    padding: 10px;
    border: 1px solid blue;
}
.h-1{
    font-size: 30px;
}
.h-2{
    font-size: 25px;
}
.h-3{
    font-size: 20px;
}
</style>