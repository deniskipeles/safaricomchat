
<template>
    <div :style="styles.wrapper">
        <div style="margin: auto">
        <form @submit.prevent="handleSubmit">
            <label>Email :</label>
            <input type="email" v-model="email" required>

            <label>Full Name :</label>
            <input type="text" v-model="fullName" required>

            <label>Username :</label>  
            <input type="text" v-model="username" required>            
            <div v-if="usernameError" class="error">{{ usernameError }} </div>
            
            <label>Password :</label>  
            <input type="password" v-model="password" required>            
            <div v-if="passwordError" class="error">{{ passwordError }} </div>

            <!-- <label>Role :</label>
            <select v-model="role">
                <option value="designer">Web Designer</option> 
                <option value="developer">Web Developer</option> 
            </select>

            <div>
                <input type="checkbox" value="highschool" v-model="education" >
                <label>High School</label>
                <input type="checkbox" value="bechelors" v-model="education" >
                <label>Bachelor's Degree</label>
                    <input type="checkbox"  value="masters" v-model="education" >
                <label>Master's Degree</label>
            </div>      

            <div>
                <input type="checkbox" v-model="terms" required>
                <label>Please accept terms and conditions</label>
            </div> -->
            <div class="button">
                <button class="submit" type="submit">Sign up here</button>
            </div>
        </form>
        <hr/>
        <p style="margin: auto">
          <a href="/">login</a>
        </p>
    </div>
    </div>
</template>
    
    
<script>
import { CometChat } from "@cometchat-pro/chat";
import {COMETCHAT_CONSTANTS} from '../../CONSTS'
import * as style from "../AppHome/style";
import PocketBase from 'pocketbase';

export default{

    name:"Register",

    data() {
        return {
            email: '',
            password: '',
            fullName: '',
            username: '',
            usernameError:'',
            passwordError: ''
        }
    },
    computed:{
        styles() {
            return {
                wrapper: style.wrapperStyle(),
                title: style.titleStyle(),
                subtitle: style.subtitleStyle(),
                userWrapper: style.userWrapperStyle(),
                userSelector: style.userSelectorStyle(),
                avatarWrapper: style.avatarWrapperStyle(),
                loginText: style.loginTextStyle(),
                input: style.inputStyle(),
                loginButton: style.loginButtonStyle(),
            };
        },
    },
    methods: {
        
        async handleSubmit() {
            //Validate password field length
            this.passwordError = this.password.length > 6 ? 
            '' : 'Password should be more than 6 characters long!';
            if(!this.passwordError) {
                let authKey = COMETCHAT_CONSTANTS.AUTH_KEY;
                var uid = this.username;
                var name = this.fullName;

                const client = new PocketBase('https://bnet.fly.dev');

                const data = { 
                    username:this.username,
                    email:this.email,
                    password:this.password
                 };

                const record = await client.records.create('auth_users', data);

                var user = new CometChat.User(uid);
                await user.setName(name);
                if (record.id) {
                    CometChat.createUser(user, authKey).then(
                        user => {
                            console.log("user created", user);
                            location.href = "/";
                        },error => {
                            console.log("error", error);
                        }
                    )
                }
            }
        }
    }
    
}
</script>
    
    
<style scoped> 

form {
    max-width: 600px;
    margin: 30px auto;
    background: #fff;
    text-align: left;
    padding: 20px;
    border-radius: 10px;
}

label {
    color: #aaa;
    display:inline-block;
    margin: 25px 0 15px;
    text-transform: uppercase;
}

input, select {
    display: block;
    padding: 10px 6px;
    width: 100%;
    box-sizing: bordre-box;
    border: none;
    border-bottom: 1px solid #ddd;
    color: #555;
}

input[type="checkbox"] {
    display: inline-block;
    width:16px;
    margin: 0 10px 0;
    position: relative;
    top: 2px;
}

.pill {
    display: inline-block;
    margin: 20px 10px 0 0 ;
    padding: 6px 12px;
    border-radius: 20px;
    font-size: 12px;
    cursor: pointer;
    background: #eee;
}

button {
    background: rgb(7, 24, 7);
    border: 0;
    padding: 10px 20px;
    color: white;
    border-radius: 20px;
}

.submit {
    text-align: center;
}

.error {
    color: #ff0000;
    margin-top: 10px;
    font-size: 0.8em;
    font-weight: bold;
}
</style>