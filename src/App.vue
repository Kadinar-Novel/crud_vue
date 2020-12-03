<template>
  <div>
    <form @submit.prevent="add">
    <div id="container">
      <h1>Customer CRUD</h1>
      <div class="row">
        <div class="col-25">
          <input type="hidden" v-model="form.id">
          <input type="hidden" v-model="form.password2" id="pass2">
          <label for="name">Name </label> 
        </div>
        <div class="col-75">
          <input type="text" v-model="form.name" id="name" placeholder="Fill with customer name" required>
        </div>
      </div>
      <div class="row">
        <div class="col-25">
          <label for="email">Email </label> 
        </div>
        <div class="col-75">
          <input type="email" v-model="form.email" id="email" placeholder="Fill with email customer" required>
        </div>
      </div>
      <div class="row">
        <div class="col-25">
          <label for="password">Password </label> 
        </div>
        <div class="col-75">
          <input type="password" v-model="form.password" id="pass1">
        </div>
      </div>
      <div class="row">
        <div class="col-25">
          <label for="gender">Gender </label> 
        </div>
        <div class="col-75">
          <input type="radio" v-model="form.gender" value="M" > Male / <input type="radio" v-model="form.gender" value="F"> Female
        </div>
      </div>
      <div class="row">
        <div class="col-25">
          <label for="marital">Marital Status </label> 
        </div>
        <div class="col-75">
          <select v-model="form.is_married">
            <option value="Single">Single</option>
            <option value="Married">Married</option>
            <option value="Divorce">Divorce</option>
          </select>
        </div>
      </div>
      <div class="row">
        <div class="col-25">
          <label for="address">Address </label> 
        </div>
        <div class="col-75">
          <input type="text" v-model="form.address" placeholder="Fill with address customer">
        </div>
      </div>
      <div class="row ">
        <button type="submit" v-show="!updateSubmit" class="button-submit">Create</button>  
        <button type="button" v-show="updateSubmit" @click="update(form)" class="button-submit">Update</button>
      </div>
    </div>    
    </form>
    <hr/>
    <div id="table-responsive">
      <table border="1" id="customers">
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
          <td v-if="customer.gender==='M'">Male</td>
          <td v-if="customer.gender==='F'">Female</td>
          <td>{{customer.is_married}}</td>
          <td>{{customer.address}}</td>
          <td><button @click="edit(customer)" class="button-table">Edit</button> ||  <button @click="del(customer)" class="button-table">Delete</button></td>
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
input[type=text],input[type='email'], input[type=password]{
  width: 100%;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
  resize: vertical;
}

label{
  padding: 12px 12px 12px 0;
  display: inline-block;
}

.button-submit{
  background-color: #4CAF50;
  color: white;
  padding: 12px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  float: right;
}

.button-table{
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.container{
  margin: auto;
  width: 50%;
  border-radius: 5px;
  background-color: #f2f2f2;
  padding: 20px;
}

.col-25{
  float: left;
  width: 10%;
  margin-top: 6px;
}

.col-75{
  float: left;
  width: 90%;
  margin-top: 6px;
}

.row:after {
  content: "";
  display: table;
  clear: both;
}

#table-responsive{
  overflow-x:auto;
}

#customers {
  font-family: Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
}

#customers td, #customers th {
  border: 1px solid #ddd;
  padding: 8px;
}

#customers tr:nth-child(even){background-color: #f2f2f2;}

#customers tr:hover {background-color: #ddd;}

#customers th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #4CAF50;
  color: white;
}

@media screen and (max-width: 600px){
  .col-25, .col-75, button{
    width: 100%;
    margin-top: 0;
  }  
}
</style>