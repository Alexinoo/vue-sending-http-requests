
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
  -Updating this.results = newResults will not work until you work with Arrow functions

-->
<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <ul>
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

  data(){

    return {
      results : []
    }
  },

  methods : {

    loadExperiences(){

        fetch('https://vue-http-demo-97b72-default-rtdb.firebaseio.com/surveys.json')
        .then(response =>{

          if(response.status === 200 && response.statusText === 'OK'){

            console.log(response);
              return response.json()
          }          
        })
        .then( data => {
          
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