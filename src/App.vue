<template>
  <div id="app">
    <form @submit.prevent="add">
    <div>
      <h1>Customer CRUD</h1>
      <div class="isiLabel">
        <input type="hidden" v-model="form.id">
        <input type="hidden" v-model="form.password2" id="pass2">
        <label >Name </label> <input type="text" v-model="form.name" required>
      </div>
      <div class="isiLabel">
        <label >Email </label> <input type="email" v-model="form.email" required>
      </div>
      <div class="isiLabel">
        <label >Password </label> <input type="password" v-model="form.password" id="pass1" @keyup="fillPass()">
      </div>
      <div class="isiLabel">
        <label >Gender </label> <input type="radio" v-model="form.gender" value="M" > Male / <input type="radio" v-model="form.gender" value="F"> Female
      </div>
      <div class="isiLabel">
        <label>Marital Status </label> 
          <select v-model="form.is_married">
            <option value="Single">Single</option>
            <option value="Married">Married</option>
            <option value="Divorce">Divorce</option>
          </select>
      </div>
      <div class="isiLabel">
        <label >Address </label> <input type="text" v-model="form.address" size="50">
      </div>
      <div>
        <button type="submit" v-show="!updateSubmit">Create</button>  
        <button type="button" v-show="updateSubmit" @click="update(form)">Update</button>
      </div>
    </div>    
    </form>
    <hr/>
    <div>
      <table border="1">
        <tr>
          <th>Nama</th>
          <th>Email</th>
          <th>Gender</th>
          <th>Status</th>
          <th>Address</th>
          <th>Action</th>
        </tr>
        <tr v-for="customer in customers" :key="customer.id">
          <td>{{customer.name}}</td>
          <td>{{customer.email}}</td>
          <td>{{customer.gender}}</td>
          <td>{{customer.is_married}}</td>
          <td>{{customer.address}}</td>
          <td><button @click="edit(customer)">Edit</button> ||  <button @click="del(customer)" >Delete</button></td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
/* eslint-disable */ 
import axios from 'axios'
export default {
  data(){
    return{
        form: {
          id: '',
          name: '',
          email: '',
          password: '',
          password2: '',
          gender: '',
          is_married: '',
          address: ''
        },
        customers: '',
        updateSubmit: false
    }
  },
  mounted() {
    this.load()
  },
  methods: {
    load(){
        axios.get('http://localhost:8000/show_all_customer').then(res => {
        this.customers = res.data.result
      }).catch ((err) => {
        console.log(err);        
      })
    },
      add(){
      axios.post('http://localhost:8000/create_customer', this.form).then(res => {
          alert(res.data.status.message)
          this.load()
          this.form.name = ''
          this.form.email = ''
          this.form.password = ''
          this.form.password2 = ''
          this.form.gender = ''
          this.form.is_married = ''
          this.form.address = ''
      }).catch ((err) => {
        alert(err.response.data.email);    
      })
    },
    edit(customer){ 
        this.updateSubmit = true
        this.form.id = customer.id 
        this.form.name = customer.name 
        this.form.email = customer.email 
        this.form.password = customer.password 
        this.form.password2 = ''
        this.form.gender = customer.gender 
        this.form.is_married = customer.is_married 
        this.form.address = customer.address 
    },
    update(form){ 
       return axios.put('http://localhost:8000/update_customer/' + form.id , {
          name: this.form.name,
          email: this.form.email,
          password: this.form.password,
          gender: this.form.gender,
          is_married: this.form.is_married,
          address: this.form.address
        }).then(res => {
        alert(res.data.status.message)
        this.load()
        this.form.id = ''
        this.form.name = ''
        this.form.email = ''
        this.form.password = ''
        this.form.password2 = ''
        this.form.gender = ''
        this.form.is_married = ''
        this.form.address = ''
        this.updateSubmit = false
      }).catch((err) => {
        console.log(err);
        
      })
    },
    del(customer){
      if(confirm('Are you sure?'))
      axios.delete('http://localhost:8000/delete_customer/' + customer.id).then(res =>{
          alert(res.data.status.message)
          this.load()
          let index = this.customers.indexOf(form.name)
          this.customers.splice(index,1)          
      })
    },
    fillPass(){
      document.getElementById('pass2').value = document.getElementById('pass1').value;
    }
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 10px;
}

.isiLabel {
  padding-right: 5px;
  padding-bottom: 5px;
}
</style>