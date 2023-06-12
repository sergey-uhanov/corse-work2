<template>
  <div class="container column">
    <form class="card card-w30">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="typeBlock">
          <option selected value="AppAddTitle"  >Заголовок</option>
          <option value="AddSubTitle">Подзаголовок</option>
          <option value="AddImg">Аватар</option>
          <option value="AddText">Текст</option>
        </select>
      </div>

      <div class="form-control">
        <label for="valueText">Значение</label>
        <textarea id="valueText" rows="3" v-model="textareaValue"></textarea>
      </div>

      <button
          class="btn primary"
          @click.prevent="addParts"
          :disabled="checkLength"
      >Добавить</button>
    </form>
    <div class="card card-w70" v-if="this.components[0]">
      <component v-for="(item, index) in components"
                 :key="index"
                 :content="item.text"
                 :is="item.currentComponent">
      </component>
    </div>
    <div class="card card-w70" v-else>
      <h3>Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>

  <div class="container">
    <p v-if="!openComments">
      <button class="btn primary" @click="loadComments">Загрузить комментарии</button>
    </p>
    <div class="card" v-else>
      <h2>Комментарии</h2>
      <ul class="list">
<AddComments
    v-for="(comment , idx) in listComments"
    :key="idx"
    :eMail="comment.email"
    :commentText="comment.body"

></AddComments>
      </ul>
    </div>
    <div class="loader"  v-if="loading"></div>
  </div>
</template>

<script>
import AppAddTitle from "@/components/AppAddTitle.vue";
import AddSubTitle from "@/components/AddSubTitle.vue";
import AddImg from "@/components/AddImg.vue";
import AddText from "@/components/AddText.vue";
import AddComments from "@/components/AddComments.vue";
export default {
 data(){
   return{
     typeBlock:'AppAddTitle',
     textareaValue:'',
     components:[],
     openComments: false,
     loading: false,
     listComments: String
   }
 },
  computed:{
   checkLength(){
     return this.textareaValue.length < 3
   }
  },
  created() {
   this.fetchData()
  },
  methods:{
   async fetchData(){
     try {
       let response = await fetch('https://my-project-dc3d7-default-rtdb.firebaseio.com/blocks.json',{
         method: 'GET'
       })
       let data = await response.json()
       this.components = Object.values(data)
     }catch (error){
       this.components.push(error)
     }
    },
    addParts(){
      fetch('https://my-project-dc3d7-default-rtdb.firebaseio.com/blocks.json',{
        method: 'POST',
        body: JSON.stringify({text: this.textareaValue,
          currentComponent:  this.typeBlock}),
      })
      this.components.push({
        text: this.textareaValue,
        currentComponent:  this.typeBlock
      })
      this.typeBlock = 'AppAddTitle'
      this. textareaValue=''
    },
    async loadComments() {
      this.loading = true;
      const data = await fetch('https://jsonplaceholder.typicode.com/comments?_limit=42.json')
      this.listComments = await data.json()

      console.log(this.listComments)
      try {
        // Выполняйте необходимые действия для загрузки комментариев
      } catch (error) {
        console.log(error);
      } finally {
        this.loading = false;
        this.openComments = true;
      }
    },
    },
  components:{
   AppAddTitle,AddSubTitle,AddImg,AddText,AddComments
  }

}
</script>

<style>
  .avatar {
    display: flex;
    justify-content: center;
  }

  .avatar img {
    width: 150px;
    height: auto;
    border-radius: 50%;
  }
</style>
