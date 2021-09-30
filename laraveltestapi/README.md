Step 1
	 ```composer update```
	 ```npm install```
Step 2	 
 Get an .env file from the .env.example file
	 (the following codes only work in linux)
	 ```cp .env.example .env```
	 ```gedit .env```
Step 3	 
Create the database and enter the information in the .env file

Step 4
Get a laravel key
	 ```php artisan key:generate```

Step 5 	 
Get a jwt key
	 ```php artisan jwt:secret```
Step 6	 
Create tables
	 ```php artisan migrate:fresh```

Step 7	 
Start Laravel server
	```php artisan serve --port=8000```

Email Server Used PostMark

Links: 
Registration

```http://127.0.0.1:8000/api/auth/register```

Required fields;

 1. email
 2. name
 3. password
 4. password_confirmation
 
 Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 


Login

```http://127.0.0.1:8000/api/auth/login```

Required fields;

 1. email
 2. password
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 
 

The transaction was successful and returned us a token. We will proceed through this token. So let's copy this token value.

Now we will make a request to reach our information. We will use the token value here. (Actually we'll always use it after that).

Here we can send the token value with form or headers. 

```http://127.0.0.1:8000/api/auth/me```

Optional fields;

 1. token
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 3. Authorization: Bearer + "your token"
 
 Reset Password

```http://127.0.0.1:8000/api/profile/current/set-password```

Required fields;

 1. newPassword
 2. email
 3. token
 4. currentPassword
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 

Password change operation completed successfully. 

Email Verification. (A mail is sent automatically when a user is registered.)

```http://127.0.0.1:8000/api/email/verify/```

Optional fields;

 1. token
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 3. Authorization : Bearer + "your token"


(Email returned as a message unverified. Because we haven't done the email verification yet.)

Resend Email Verification

```http://127.0.0.1:8000/api/email/resend/```

Optional fields;

 1. token
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 3. Authorization : Bearer + "your token"
 
 
Mail Received...


Example email verify link: 
```http://127.0.0.1:8000/email/verify?queryURL=http%3A%2F%2F127.0.0.1%3A8000%2Fapi%2Femail%2Fverify%2F10%3Fexpires%3D1548685326%26signature%3Da9e04bf0f188a490832439f868cf9d5b4c60dc33dfa155a80145075c328fcd0c```

Example queryURL :
```http://127.0.0.1:8000/api/email/verify/asaasasas-asasas-sasasas?expires=1548678462&signature=baa0af50040f689eccd241157d6b14708b1ec37e2fe94277c9aa45aee9cf8d69```

Use a vue-router and component to capture this queryURL. ```/email/verify/``` retrieves the queryURL of the request to the route.

Example vue-component: 
```
<template>  
 <div> 
	 <div  class="mt-2 alert alert-warning" role="alert">
		 Email verification is taking place.  
	 </div>  
 </div>
</template>  
  
<script>  
  export default {  
        async mounted() { 
            const { queryURL } = this.$route.query;  
			console.log('post this url with your token', queryURL, "Sample = Authorization : Bearer + your token" );  
			  await axios.post(queryURL, {  
                'Access-Control-Allow-Origin':'*',  
				'Content-Type':'X-CSRF-TOKEN',  
				}).then(x => {  
	                console.log(x);  
				}).catch(x => {  
	                console.log(x);  
				});  
		  }  
    }  
</script>
```

Also, done with api path using this link:
http://127.0.0.1:8000/api/email/verify/sasasa-sdsds-ddasas?expires=1548678462&signature=baa0af50040f689eccd241157d6b14708b1ec37e2fe94277c9aa45aee9cf8d69

Optional fields;

 1. token
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 3. Authorization : Bearer + "your token"
 
 

Email seems to be verified. 

Then check this 
```http://127.0.0.1:8000/api/email/verify/```

Email Verified.

Email Logout

```http://127.0.0.1:8000/api/auth/logout/```

Optional fields;

 1. token
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 3. Authorization : Bearer + "your token"

User logout

Refreshing Tokens..

```http://127.0.0.1:8000/api/auth/refresh/```

Optional fields;

 1. token
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json
 3. Authorization : Bearer + "your token"

Forget Password

My resetUrl address is ```127.0.0.1:8000//profile/forgot-password-reset/```

The address of the request is as follows;

```127.0.0.1:8000/api/auth/forgot-password-email/```

Required fields;

 1. email
 2. resetUrl
 
  Headers;
 
 1. Accept: application/json
 2. Content-Type: application/json

IMPORTANT! At the end of resetUrl, we will have to add  to /token/ email/.

Example resetUrl;

```127.0.0.1:8000//profile/forgot-password-reset/<token>/<email>```


<template>  
</template>  
  
<script>  
  import router from 'vue-router';  
  export default {  
        name: "ForgotPasswordEmail",  
		data() {  
            return {  
                token:'',  
				email:'',  
				password:'testpasswords', //Input incoming data (model="password")  
			 }  
		  },  
		 async created() {  
	            this.token = this.$route.params.token;  
				this.email = this.$route.params.email;  
				await axios.post('http://127.0.0.1:8000/api/auth/forgot-password-reset/',  
				  { 
                    token: this.token,  
					email: this.email,  
					password: this.password,  
				  },  
				  {  
	                'Access-Control-Allow-Origin':'*',  
					'Content-Type':'multipart/form-data',  
					'Accept':'application/json',  
				 }).then(response => {  
		              console.log(response.data.message)  
		         })  
	              .catch(error => {  
	                    console.error(error.response.data.message)  
                });  
		  }  
	    }  
</script>
```

