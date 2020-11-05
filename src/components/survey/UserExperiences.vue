<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
			<p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && error">{{ error }}</p>
			<p v-else-if="!isLoading && (!results || results.length === 0)">No stored experiences found. Start adding some survey results first.</p>
			<ul v-else>
			<!-- <ul v-else-if="!isLoading && results && results.length > 0"> -->
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
  // prop s: ['results'],
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
			this.isLoading = true; // reset the Loading state before a new request
			this.error = null; // resetting the error when sending a new request
			/*
			 * fetch returns a Promise, that is an object,
			 * on which we can listen for the data, once it is there,
			 * to then set up code that will be executed when that data is there
			 * We set such a listener with the .then method, on the result of fetch
			 * the .then method takes a function 
			 * that will be executed once the result is there
			 * this function takes the response sent back by the server 
			 * we can try to parse the response
			 */
			fetch('https://vue-http-demo-5c66a.firebaseio.com/surveys.json')
				.then((response) => {
					if (response.ok) {
						return response.json();
					}
				})
				.then((data) => {
					this.isLoading = false;
					console.log(data);
					const results = [];
					for (let id in data) {
						results.push({
							id: id,
							name: data[id].name,
							rating: data[id].rating
						});
					}
					this.results = results;
				})
				.catch( (error) => {
					this.isLoading = false; // it is not loading because we have an error
					console.log(error);
					this.error = 'Failed to fetch data - Please try again later...'
				});
		}
	},
	mounted() {
		this.loadExperiences();
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