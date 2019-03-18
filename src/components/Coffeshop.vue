<template>
  <div class="coffee-list">
    <div class="form">
      <input class="input" v-model="name" placeholder="Coffee shop Name">
      <input class="input" v-model="description" placeholder="Coffee Shop Description">
      <button class="button" v-on:click="createCoffeeShop">Create Coffee Shop</button>
    </div>
    <div v-for="item in coffeeShops" :key="item.key" class="list-item">
      <p class="name">{{ item.name }}</p>
      <p class="description">{{ item.description }}</p>
    </div>
  </div>
</template>

<script>
import { API, graphqlOperation } from "aws-amplify";

const ListCoffeeShops = `
  query {
    listCoffeeShops {
      items {
        id name description
      }
    }
  }
`
const CreateCoffeeShop = `
  mutation($name: String! $description: String) {
    createCoffeeShop(input: {
      name: $name description: $description
    }) {
      id name description
    }
  }
`

export default {
    name: "CoffeeShop",
    data() {
        return {
            name: "",
            description: "",
            coffeeShops: []
        }
    },
    async beforeCreate() {
        const {
            data: {
                listCoffeeShops: { items }
            }
        } = await API.graphql(graphqlOperation(ListCoffeeShops))
        this.coffeeShops = items
    },
    methods: {
    async createCoffeeShop() {
        if (this.name === "" || this.description === "") return
        const coffeeShop = { name: this.name, description: this.description }
        const coffeeShops = [...this.coffeeShops, coffeeShop]
        this.coffeeShops = coffeeShops
        this.name = ""
        this.description = ""
        try {
            await API.graphql(graphqlOperation(CreateCoffeeShop, coffeeShop))
            console.log("coffee shop successfully created!")
        } catch (error) {
            console.log(error)  
        }
    }
    }
}
</script>

<style scoped>
.coffee-list {
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    width: 60vw;
    font-size: 18px;
}
.form {
    display: flex;
    flex-direction: column;
}
.button {
    border: none;
    background-color: #ffac31;
    margin: 10px;
    padding: 20px;
}
.input {
border: none;
margin: 10px;
height: 35px;
background-color: transparent;
border-bottom: 1px solid #ddd;
padding: 8px;
}
.list-item {
    margin: 10px;
    border-bottom: 2px solid #ddd;
    background-color: #ddd;
    padding: 20px;
    text-align: left;
}
p {
    margin: 0;
}
.name { 
    font-size: 22px; 
    font-weight: 900;
}
.description { 
    color: rgba(0, 0, 0, .45); 
}
</style>
