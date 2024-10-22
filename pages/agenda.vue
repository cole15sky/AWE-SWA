   <template>
    <div >
      <div>
        <navbar />
      </div>
      <div v-if="loading">Loading...</div>
      <div v-if="error">{{ error }}</div>
  
      <div v-if="data">
        <div v-for="item in data" :key="item.id">
          <h2>{{ item.title }}</h2>
          <p>{{ item.description }}</p>
          <p><strong>Type:</strong> {{ item.type }}</p>
          <p><strong>Start:</strong> {{ formatDate(item.startDate) }}</p>
          <p><strong>End:</strong> {{ formatDate(item.endDate) }}</p>
  
          <div v-if="item.agendaToSpeakers.length">
            <h3>Speakers:</h3>
            <ul>
              <li v-for="speaker in item.agendaToSpeakers" :key="speaker.speakers.id">
                <strong>{{ speaker.speakers.name }}</strong> ({{ speaker.speakers.designation }})
                <img :src="speaker.speakers.profileImage" alt="Speaker Image"/>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  definePageMeta({
  colorMode: 'light',
})
  
  const data = ref(null);
  const error = ref(null);
  const loading = ref(true);
  
  const getData = async () => {
    const url = 'https://swa-2024-dev.up.railway.app/api/agenda/web';
    try {
      const response = await fetch(url);
      if (!response.ok) {
        throw new Error(`Response status: ${response.status}`);
      }
      const json = await response.json();
      data.value = json;
    } catch (err) {
      error.value = err.message;
    } finally {
      loading.value = false;
    }
  };
  
  // Fetch data when the component is mounted
  onMounted(() => {
    getData();
  });
  
  // Helper function to format date
  const formatDate = (dateString) => {
    const date = new Date(dateString);
    return date.toLocaleString();
  };
  </script>
