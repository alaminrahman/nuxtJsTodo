<template>
 <div class="relative flex items-top justify-center min-h-screen bg-gray-100 sm:items-center sm:pt-0">
    <div class="w-full">
      <!-- component -->
      <div class="h-100 w-full flex items-center justify-center bg-teal-lightest font-sans">
        <div class="bg-white rounded shadow p-6 m-4 w-full lg:w-3/4 lg:max-w-lg">
            <div class="mb-4">
                <h1 class="font-bold text-3xl text-gray-600 text-center">Todo List</h1>
           
                <div class="flex mt-4">

                    <input class="shadow appearance-none border rounded w-full py-2 px-3 mr-4 text-grey-darker" placeholder="Add Todo" v-model="content">

                    <button class="flex-no-shrink p-2 border-2 rounded-lg text-teal border-teal text-white bg-blue-500 hover:bg-blue-700" @click="submit">Add</button>
                </div>
            </div>
            <div>
                <div 
                  class="flex mb-4 items-center" 
                  v-for="(todo, index) in todos"
                  :key="index"
                  >
                    <p 
                    :class="[todo.is_done ? 'line-through text-green-600' : '', `w-full text-grey-darkest font-semibold text-gray-600`]"
                    >
                    {{ todo.content }}
                    </p> <!--done -->

                    <button v-if="todo.is_done" class="flex-no-shrink p-2 ml-4 mr-2 border-2 rounded-lg border-green text-white bg-green-500 hover:bg-green-700" @click="updatedStatus(todo, index)">Done</button>
                    <button v-else class="flex-no-shrink w-1/3 p-2 ml-4 mr-2 border-2 rounded-lg border-grey" @click="updatedStatus(todo, index)">Not Done</button>

                    <button class="flex-no-shrink p-2 ml-2 border-2 rounded-lg text-red border-red text-white bg-red-500 hover:bg-red-700" @click="deleteTodo(todo, index)">Remove</button>
                </div>
            </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import axios from 'axios';

  export default{
    name: 'NuxtTutorial',

    data(){
      return {
        content: '',
        todos:[],
      }
    },
    methods: {
      submit: function(){
        this.$nuxt.$loading.start();

        let api = `${this.$axios.defaults.baseURL}todo/store`
        this.$axios.$post(api, {
            content:this.content
          }).then( res => {
            console.log('Store')
            this.todos.unshift(res)
            this.content = ''
        }).catch(error => {
          console.log(error)
        }).finally(() => {
          //loader stop
          this.$nuxt.$loading.finish();
        })
      },

      updatedStatus: function(todo, index){
        this.$nuxt.$loading.start();

        let api = `${this.$axios.defaults.baseURL}todo/update/` + todo.id

        this.$axios.$put(api).then( res => {
            this.todos[index].is_done = res.is_done;
            console.log('update')
        }).catch(error => {
          console.log(error)
        }).finally(() => {
          //loader stop
          this.$nuxt.$loading.finish();
        })
        
      },

      deleteTodo: function(todo, index){
        //loader show with progress bar
        this.$nuxt.$loading.start();

        let api = `${this.$axios.defaults.baseURL}todo/delete/` + todo.id

        this.$axios.$delete(api).then( res => {
            this.todos.splice(index, 1)
            console.log('delete')
        }).catch(error => {
          console.log(error)
        }).finally(() => {
          //loader stop
          this.$nuxt.$loading.finish();
        })
        
      },
    },

    loading: {
      color: 'blue',
      height: '5px'
    },

    async fetch(){
      this.todos = await fetch(`${this.$axios.defaults.baseURL}todo/index`).then(res => res.json())
    },

    
  }
  
</script>