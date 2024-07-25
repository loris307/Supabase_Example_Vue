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
	</div>
  </template>
  
  <script>
  import { ref, onMounted } from 'vue'
  import { supabase } from '../supabase.js'
  
  export default {
	name: 'UserAuth',
	setup() {
	  const alk = ref([])
	  const user = ref(null)
	  const email = ref('')
	  const password = ref('')
	  const authError = ref(null)
  
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
  
	  onMounted(() => {
		checkUser()
	  })
  
	  return { alk, user, email, password, authError, signIn, signOut }
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
