<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>

        <loading-message v-if="loading"></loading-message>
        <p v-else-if="!loading && error">{{ error }}</p>
        <h3 v-else-if="noData">No user experiences found. You can start adding some in the form above!</h3>    

      <ul v-else> 
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';
import LoadingMessage from '../UI/LoadingMessage.vue'

export default {
  components: {
    SurveyResult,
    LoadingMessage
  },
  data() {
    return {
      results: [],
      loading: false,
      error: null
    }
  },
  methods: {
    loadExperiences() {
      this.loading = true
      this.error = null
   
      fetch('https://vue-hhtp-demo-71d8c-default-rtdb.europe-west1.firebasedatabase.app/surveys.json').then((response) => {
        if (response.ok) {
          return response.json()
        }
      })
      .then((data) => {
        this.loading = false
        const results = []
        for (const id in data) {
          results.push({
            id: id, 
            name: data[id].name, 
            rating: data[id].rating})
        }
        this.results = results
      })
      .catch((error) => {
        console.log(error)
        this.loading = false 
        this.error = 'Failed to fetch data - please try again'
      })
    }
  },
  computed: {
    noData() {
      return !this.loading && (!this.results || this.results.length === 0)
    }
  },
  mounted() {
    this.loadExperiences()
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>