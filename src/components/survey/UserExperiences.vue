
<!-- Getting Data (GET Request) and Transforming Response Data
======================================================

-Added a click listener to fire loadExperiences();

- loadExperiences() is called which then use fetch() to get data from Firebase URL

-Getting data with fetch()
     - We dont need method : 'GET' since it is GET by default;
     - Headers / body are not needed in a GET request since we are not sending any data

-fetch( url  ) takes URL as it's only parameter - specifies which address to send the request to

-Sending a HTTP request can take a couple of milliseconds /seconds for the data to arrive 

-fetch( url  ) - Returns a promise which we need to chain with .then()

- .then(response =>{}) - Takes a function which takes an argument of response and returns a Response Object
                  -We can check if the response is ok i.e check whether the request was successful;
                     e.g.  if 404 ; means this is not OK ; Request was not successful;
                  -If OK , we can proceed and read the data with the json() which will parse the data that is part of the response if it is in json format

                  -response.json() also returns a promise and therefore we can return response.json() and then add another .then() which will be triggered  when the return promise is fulfilled;

  -.then( data => {} ) - Also takes in a function but with the actual data as the parameter that the response.json() yielded;
                  -And that is the place where we have access to the data itself 
                  -We can loop through and get the data
  -Updating  ( this.results = newResults ) will not work until you work with Arrow functions - This is because (this) keyword cannot be used in a callback function

-->



<!-- Loading Data When UserExperience Component Mounts
============================================

-Sending and Getting data works and that was the main objective of this course

-But there is a small improvement we can add - We want to fetch all submitted experiences automatically without having to press Load Submitted Experiences button first 

-We can trigger loadExperiences() not just when Load Submitted Experiences button i s clicked but when the entire component is loaded for the first time.

-We can use mounted() hook which is triggered / called when this exact component gets mounted

-->



<!-- Showing a Loading... Message if we are still loading Data
============================================

-Add isLoading data property and initialize it to false

-Set it to true before fetching ; Then set it to false once the data is received

-Show a paragraph ( Loading... ) if isLoading data property is true ; Otherwise show the ul once the data is received

-->


<!-- Handling the No Data state
============================================

-There a re quite a number of ways we can use to check if we have data

  1.) No Data - isLoading is false and the results is falsy and length ===0

      e.g.  v-else-if="!isLoading && ( !results || results.length === 0)"


  2.)  Show Data - isLoading is false and the (results) truthy and the length of the results >  1

      e.g.  v-else-if="!isLoading && results && results.length > 0"

-->

<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading...</p>

      <p v-else-if="!isLoading && ( !results || results.length === 0)" >No stored experiences found. Start adding some</p>
      <ul v-else-if="!isLoading && results && results.length > 0" >
        <survey-result v-for="result in results" :key="result.id" :name="result.name" :rating="result.rating">
        </survey-result>
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

  data(){

    return {
      results : [] ,
      isLoading : false,
    }
  },

  methods : {

    loadExperiences(){
    
        this.loading = true

        fetch('https://vue-http-demo-97b72-default-rtdb.firebaseio.com/surveys.json')
        .then(response =>{

          if(response.status === 200 && response.statusText === 'OK'){

            console.log(response);
              return response.json()
          }          
        })
        .then( data => {

          this.loading = false
          
          const newResults = [];

          for( const id in data ){

            newResults.push({
                id : id,
                name : data[id].name ,
                rating : data[id].rating
            })
          }

          this.results = newResults
          
        } )

    }

  },

  mounted(){
    this.loadExperiences()
  } ,


};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>