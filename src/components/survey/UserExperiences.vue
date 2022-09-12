<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading... </p>
      <p v-else-if="checkError">{{ error }}</p>
      <p v-else-if="checkData">There are no any stored data</p>
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

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      error: null
    }
  },
  methods: {
    loadExperiences() {
      this.isLoading = true
      fetch('https://vue-http-demo-32a3d-default-rtdb.firebaseio.com/survey.json')
          .then(res => {
            if (res.ok) {
              return res.json()
            }
          })
          .then(data => {
            const formatData = []
            for (const id in data) {
              formatData.push({id, name: data[id].name, rating: data[id].rating})
            }
            this.results = formatData
          })
          .catch(() => this.error = 'There is a problem with fetching data. Try a little later.')
          .finally(() =>  this.isLoading = false)
    }
  },
  computed: {
    checkData() {
      return !this.isLoading && (!this.results || this.results && this.results.length === 0)
    },
    checkError() {
      return !this.isLoading && this.error
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