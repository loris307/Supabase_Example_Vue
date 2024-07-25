<template>
	<div>
	  <h1>Voddi</h1>
	  <div v-if="user">
		<p>Welcome, {{ user.email }}</p>
		<button @click="signOut">Sign Out</button>
	  </div>
	  <div v-else>
		<input v-model="email" type="email" placeholder="Email" />
		<input v-model="password" type="password" placeholder="Password" />
		<button @click="signIn">Sign In</button>
		<p v-if="authError">{{ authError }}</p>
	  </div>
	  <div v-for="u in alk" :key="u.id" class="alk-item">
		<p>{{ u.Marke }} | {{ u.Alkoholgehalt }}%</p>
	  </div>
	  <button @click="callFunction">Call Supabase Function</button>
	  <p v-if="functionResponse">{{ functionResponse }}</p>
	</div>
  </template>
  
  <script>
  import { ref, onMounted } from 'vue'
  import { supabase } from '../supabase.js'
  //import axios from 'axios'
  
  export default {
	name: 'UserAuth',
	setup() {
	  const alk = ref([])
	  const user = ref(null)
	  const email = ref('')
	  const password = ref('')
	  const authError = ref(null)
	  const functionResponse = ref('')
  
	  const fetchAlk = async () => {
		const { data, error } = await supabase.from('Alk').select('*')
		if (error) console.error(error)
		else alk.value = data
	  }
  
	  const checkUser = async () => {
		const { data: { user: sessionUser }, error } = await supabase.auth.getUser()
		if (error) {
		  console.error(error)
		} else if (sessionUser) {
		  user.value = sessionUser
		  fetchAlk() // Fetch users only if authenticated
		}
	  }
  
	  const signIn = async () => {
		const { data, error } = await supabase.auth.signInWithPassword({
		  email: email.value,
		  password: password.value,
		})
		if (error) {
		  authError.value = error.message
		} else {
		  user.value = data.user
		  fetchAlk() // Fetch users only if authenticated
		}
	  }
  
	  const signOut = async () => {
		const { error } = await supabase.auth.signOut()
		if (error) {
		  console.error('Error signing out:', error)
		} else {
		  user.value = null
		}
	  }
  
	  const callFunction = async () => {
  		try {
    		console.log('Sending request to Supabase function');
    		const response = await fetch('https://qmkytucimctdxmcgrkwi.supabase.co/functions/v1/hello-world', {
     			method: 'POST',
      			headers: {
        			Authorization: `Bearer ...`,
        			'Content-Type': 'application/json',
      			},
				body: JSON.stringify({ name: 'Loris' }),
			});

    		if (!response.ok) {
      			throw new Error(`Server error: ${response.status} ${response.statusText}`);
			}

    		const data = await response.json();
			console.log('Response received:', data);
    		functionResponse.value = data.message;
		} catch (error) {
			console.error('Error calling Supabase function:', error);
			functionResponse.value = `Error calling function: ${error.message}`;
		}
	};



  
	  onMounted(() => {
		checkUser()
	  })
  
	  return { alk, user, email, password, authError, signIn, signOut, callFunction, functionResponse }
	}
  }
  </script>
  
  <style>
  .alk-item {
	margin-top: 30px;
	display: flex;
	align-items: center;
	justify-content: center;
	margin-bottom: 8px;
  }
  </style>
  