<script>

import { ref, onMounted } from 'vue'
import { supabase } from '../supabase.js' // Adjust the path to where you created the supabase.js file

export default {
  name: 'UserDisplay',

  setup() {
    const users = ref([])

    const fetchUsers = async () => {
      const { data , error } = await supabase.from('User').select('*')
      if (error) console.error(error)
      else users.value = data
    }

    onMounted(() => {
      fetchUsers()
    })

    return { users }
  }
  
}

</script>
<template>
	<div>
		<h1>Users</h1>
		<div v-for="user in users" :key="user.id">
	  	<p>{{ user.name }}</p>
	</div>
  </div>
</template>