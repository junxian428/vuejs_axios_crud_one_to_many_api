<template>


<div class="login-box">
  <h2>Login</h2>
  <form @submit.prevent="submit">
    <div class="user-box">
      <input type="text"  name="email" placeholder="" required="">
      <label>Email</label>
    </div>
    <div class="user-box">
      <input type="password" name="password" placeholder="" required="">
      <label>Password</label>
    </div>
    <a>
      <span></span>
      <span></span>
      <span></span>
      <span></span>
      <button type="submit" >Submit</button>
    </a>
  </form>
</div>
<!---
    <main class="form-signin">
      <form @submit.prevent="submit">
        <h1 class="h3 mb-3 fw-normal">Sign In</h1>
  
        <div class="form-floating">
          <label>Email&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp</label>
          <input type="email" class="form-control" name="email" placeholder="name@example.com">
        </div>
        <br>
        <div class="form-floating">
          <label>Password&nbsp</label>
          <input type="password" class="form-control" name="password" placeholder="Password">
        </div>
        <br>
        <br>
        <button class="w-100 btn btn-lg btn-primary blue-ocean" type="submit" >Submit</button>
      </form>
    </main>

  -->




  
  </template>
  
  <script>
  import {useRouter} from "vue-router";
  import axios from "axios";
  import VueCookies from 'vue-cookies';

  //

  export default {
    name: "Login",
    data() {
    return {
      email: '',
      password: ''
    };
    },



    
    setup() {
        const router = useRouter();
      try{
        const submit = async e => {

        //

        //
        try{
        const form = new FormData(e.target);
  
        const inputs = Object.fromEntries(form.entries());
        if(inputs != null){
              console.log(inputs);
            const loginData = {
                email: inputs.email,
                password: inputs.password
            };
            const {data} = await axios.post(process.env.VUE_APP_API_URI, loginData);
            console.log(data);
            console.log(data['access_token']);


            localStorage.setItem('access_Token', data['access_token']);
            VueCookies.set('access_Token',  data['access_token'], '7d'); // Save the access token in a cookie for 7 days

            //this.setCookie('access_Token',  data['access_token'], 7);
          // axios.defaults.headers.common['Authorization'] = `Bearer ${data.access_token}`;
            //console.log(data.token);
            await router.push('/');
        } else{
          localStorage.setItem('access_Token', data['access_token']);
            VueCookies.set('access_Token',  data['access_token'], '7d'); // Save the access token in a cookie for 7 days
          await router.push('/');

        }

      }catch(error){
        console.log(error);
                // Clear email input
        document.querySelector('input[name="email"]').value = '';

        // Clear password input
        document.querySelector('input[name="password"]').value = '';
                await router.push('/login');

        }
      }

      return {
        submit
      }
    }catch(error){
      console.log(error);
    }
  

      
  
    }
  }
  </script>


<style>

button {
  all: unset;
  cursor: pointer;
}

button:focus {
  outline: revert;
}


html {
  height: 100%;
}
body {
  margin:0;
  padding:0;
  font-family: sans-serif;
  background: linear-gradient(#141e30, #243b55);
}

.login-box {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 400px;
  padding: 40px;
  transform: translate(-50%, -50%);
  background: rgba(0,0,0,.5);
  box-sizing: border-box;
  box-shadow: 0 15px 25px rgba(0,0,0,.6);
  border-radius: 10px;
}

.login-box h2 {
  margin: 0 0 30px;
  padding: 0;
  color: #fff;
  text-align: center;
}

.login-box .user-box {
  position: relative;
}

.login-box .user-box input {
  width: 100%;
  padding: 10px 0;
  font-size: 16px;
  color: #fff;
  margin-bottom: 30px;
  border: none;
  border-bottom: 1px solid #fff;
  outline: none;
  background: transparent;
}


.login-box .user-box label {
  position: absolute;
  top:0;
  left: 0;
  padding: 10px 0;
  font-size: 16px;
  color: #fff;
  pointer-events: none;
  transition: .5s;
}

.login-box input[type="text"]::placeholder,
.login-box input[type="password"]::placeholder {
  color: #999; /* Input placeholder text color */
}


.login-box .user-box input:focus ~ label,
.login-box .user-box input:valid ~ label {
  top: -20px;
  left: 0;
  color: #03e9f4;
  font-size: 12px;
  
}

.login-box form a {
  position: relative;
  display: inline-block;
  padding: 10px 20px;
  color: #03e9f4;
  font-size: 16px;
  text-decoration: none;
  text-transform: uppercase;
  overflow: hidden;
  transition: .5s;
  margin-top: 40px;
  letter-spacing: 4px
}

.login-box a:hover {
  background: #03e9f4;
  color: #fff;
  border-radius: 5px;
  box-shadow: 0 0 5px #03e9f4,
              0 0 25px #03e9f4,
              0 0 50px #03e9f4,
              0 0 100px #03e9f4;
}

.login-box a span {
  position: absolute;
  display: block;
}

.login-box a span:nth-child(1) {
  top: 0;
  left: -100%;
  width: 100%;
  height: 2px;
  background: linear-gradient(90deg, transparent, #03e9f4);
  animation: btn-anim1 1s linear infinite;
}

@keyframes btn-anim1 {
  0% {
    left: -100%;
  }
  50%,100% {
    left: 100%;
  }
}

.login-box a span:nth-child(2) {
  top: -100%;
  right: 0;
  width: 2px;
  height: 100%;
  background: linear-gradient(180deg, transparent, #03e9f4);
  animation: btn-anim2 1s linear infinite;
  animation-delay: .25s
}


@keyframes btn-anim2 {
  0% {
    top: -100%;
  }
  50%,100% {
    top: 100%;
  }
}

.login-box a span:nth-child(3) {
  bottom: 0;
  right: -100%;
  width: 100%;
  height: 2px;
  background: linear-gradient(270deg, transparent, #03e9f4);
  animation: btn-anim3 1s linear infinite;
  animation-delay: .5s
}

@keyframes btn-anim3 {
  0% {
    right: -100%;
  }
  50%,100% {
    right: 100%;
  }
}

.login-box a span:nth-child(4) {
  bottom: -100%;
  left: 0;
  width: 2px;
  height: 100%;
  background: linear-gradient(360deg, transparent, #03e9f4);
  animation: btn-anim4 1s linear infinite;
  animation-delay: .75s
}

@keyframes btn-anim4 {
  0% {
    bottom: -100%;
  }
  50%,100% {
    bottom: 100%;
  }
}
</style>
