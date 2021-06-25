<template>
  <div class="container">
    <h3 class="text-center mt-3">Vuelidate ile Form Kontrolü</h3>
    <div class="d-flex justify-content-center align-content-center flex-row">
      <div class="card p-4 mt-3 mr-4 shadow">
        <form style="width: 350px" @submit.prevent="onSubmit">

          <div class="form-group">
            <label>E-mail</label>
            <input 
              @blur="$v.email.$touch()"
              v-model="email" 
              type="email" 
              class="form-control" 
              :class="{'is-invalid' : $v.email.$error}"
              placeholder="Please enter your email."
            >
            <small v-if="!$v.email.required" class="form-text text-danger">This Area Required!!!</small>
            <small v-if="!$v.email.email" class="form-text text-danger">Please Enter a Valid Email Adress...</small>
          </div>

          <div class="form-group">
            <label>Password</label>
            <input 
              v-model="$v.password.$model" 
              type="password" 
              class="form-control" 
              placeholder="Please enter your password."
            >
            <small v-if="!$v.password.required" class="form-text text-danger">This Area Required!!!</small>
            <small v-if="!$v.password.numeric" class="form-text text-danger">Password must be Numbers Only...</small>
            <small v-if="!$v.password.minLength" class="form-text text-danger">Password must be at least {{ $v.password.$params.minLength.min }} Characters...</small>
            <small v-if="!$v.password.maxLength" class="form-text text-danger">Password must be a maximum {{ $v.password.$params.maxLength.max }} Characters...</small>
          </div>

          <div class="form-group">
            <label>Confirm Password</label>
            <input 
              v-model="$v.repassword.$model" 
              type="password" 
              class="form-control" 
              placeholder="Please enter your password again."
            >
            <small v-if="!$v.repassword.numeric" class="form-text text-danger">Password must be Numbers Only...</small>
            <small v-if="!$v.repassword.required" class="form-text text-danger">This Area Required!!!</small>
            <small v-if="!$v.repassword.minLength" class="form-text text-danger">Password must be at least {{ $v.repassword.$params.minLength.min }} Characters...</small>
            <small v-if="!$v.repassword.maxLength" class="form-text text-danger">Password must be a maximum {{ $v.repassword.$params.maxLength.max }} Characters...</small>
            <small v-if="!$v.repassword.sameAs" class="form-text text-danger">Passwords do not match</small>
          </div>

          <div class="form-group">
            <label>Age</label>
            <input 
              v-model="$v.age.$model" 
              type="text" 
              class="form-control" 
              placeholder="Please enter your age."
            >
            <small v-if="!$v.age.required" class="form-text text-danger">This Area Required!!!</small>
            <small v-if="!$v.age.numeric" class="form-text text-danger">Age must be Numbers Only...!!!</small>
            <small v-if="!$v.age.between" class="form-text text-danger">Age must be between {{ $v.age.$params.between.min }} - {{ $v.age.$params.between.max }}!!!</small>
          </div>
          <div class="form-group">
            <label>Category you want to register?</label>
            <select v-model="$v.selectedCategory.$model" class="form-control">
              <option v-for="(category, idx) in categories" :value="category" :key="idx">{{ category }}</option>
            </select>
            <small v-if="!$v.selectedCategory.checked" class="form-text text-danger">You can only create records belonging to the "Software" and "Hardware" category!!!</small>
          </div>

          <a @click="newHobby" class="text-white btn btn-secondary rounded-0 btn-sm">Add interests</a>
          <small v-if="!$v.hobbies.required" class="form-text text-danger">This Area Required!!!</small>
          <small v-if="!$v.hobbies.minLength" class="form-text text-danger">Hobbies must be at least {{ $v.hobbies.$params.minLength.min }}...</small>

          <ul class="list-group mt-3 mb-3 border-0">
            <li v-for="(hobby,index) in hobbies" class="list-group-item d-flex pl-2" :key="index">
              <input 
                type="text" 
                class="form-control mr-2" 
                :class="{'is-invalid' : $v.hobbies.$each[index].$error}"
                v-model="hobby.value"
                @blur="$v.hobbies.$each[index].value.$touch()"
              >
              <button class="btn btn-sm btn-danger rounded-0" @click="hobbies.splice(index, 1)">Delete</button>
            </li>
          </ul>
         
          <button class="btn btn-outline-secondary rounded-0" :disabled="$v.$invalid">Save</button>
        </form>
      
      </div>
      <div class="card p-4 mt-3 shadow" style="width: 400px">
        <!-- <p>{{$v.mail}}</p> -->
        <!-- <p>{{$v.password}}</p> -->
        <p>{{$v.repassword}}</p>
      </div>
    </div>
  </div>
</template>
<script>
  import { required, email, numeric, minLength, maxLength, sameAs, between } from 'vuelidate/lib/validators';
  export default {
    data() {
      return {
        email : null,
        password : null,
        repassword : null,
        age: null,
        selectedCategory : "Software",
        categories : ["Software", "Hardware", "Cloud", "Servers", "Unix", "Linux", "Mac OS", "Windows"],
        hobbies: []
      }
    },
    methods: {
      onSubmit(){
        let form = {
          email : this.email,
          password : this.password,
          category : this.category,
          hobbies : this.hobbies,
        }
        console.log(form)
      },
      newHobby(){
        let hobby = {
          id: Math.random() * Math.random() * 1000,
          value: ''
        }
        this.hobbies.push(hobby)
      }
    },
    validations: {
      email: {
        required: required,
        email: email,
        // uniq: value => {
        //   return value !== 'omur8819@gmail.com';
        // },   

        // Database'deki gecikmeyi simüle etmek için.     
        uniq: value => {
          return new Promise((resolve, reject) => {
            setTimeout(() => {
              resolve(value !== 'omur8819@gmail.com')
            }, 2000)
          })
          // return value !== 'omur8819@gmail.com';
        },        
      },
      password: {
        required: required,
        numeric: numeric,
        minLength: minLength(8),
        maxLength: maxLength(12),
      },
      repassword: {
        required: required,
        numeric: numeric,
        minLength: minLength(8),
        maxLength: maxLength(12),
        sameAs: sameAs('password'),
      },
      age: {
        required: required,
        numeric: numeric,
        between: between(20, 35),
      },
      selectedCategory: {
        checked(val, vm){
          if(vm.selectedCategory === "Software" || vm.selectedCategory === "Hardware"){
            return true
          } else {
            return false
          }
        }
      },
      hobbies: {
        required: required,
        minLength: minLength(2),
        $each: {
          value: {
            required: required,
            minLength: minLength(6),
          }
        }
      }
    },
  }
</script>


<!-- line - 11 changed  @input="$v.email.$touch()" 

     line - 26 changed  @blur="$v.password.$touch()"
                        v-model="password" 
-->