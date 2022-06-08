
<!-- SENDING A POST REQUEST TO STORE DATA
===============================================================

- We will use Firebase which is a complete Backend developed by Google that we can use to store data;

- Easy to configure and get started

-We have created vue-http-demo as our project and copied the URL

      e.g. https://vue-http-demo-97b72-default-rtdb.firebaseio.com/

-We need to add an identifier with a .json extension

    e.g. https://vue-http-demo-97b72-default-rtdb.firebaseio.com/surveys.json

-Then use fetch() - which is a built-in Javascript method that we can use to send/ get data form a server;

fetch() - method - Takes the following Parameters
===================================

    1. ) 1st parameter - fetch( url , )

          -url - Specifies which url this request should be sent to

    2. ) 2nd Parameter - Javascript Object where we can configure this request
    
          -This object  contains- { 
            method : String, --HTTP VERBS   -POST/GET/DELETE/PATCH 
            headers : {} ,                                  --Content-Type : application/json
             body : {}                                        --JSON.stringified({})
               } 

         e.g. 

              {  
                method : 'POST' ,
                headers : {
                  'Content-Type' : 'application/json'
                },
                body : JSON.stringify({ name : this.name ,  rating : this.rating  }) , 

              } 

-->



<template>
  <section>

    <base-card>

      <h2>How was you learning experience?</h2>

      <form @submit.prevent="submitSurvey">
        <div class="form-control">
          <label for="name">Your Name</label>
          <input type="text" id="name" name="name" v-model.trim="enteredName" />
        </div>
        <h3>My learning experience was ...</h3>
        <div class="form-control">
          <input type="radio" id="rating-poor" value="poor" name="rating" v-model="chosenRating" />
          <label for="rating-poor">Poor</label>
        </div>
        <div class="form-control">
          <input
            type="radio"
            id="rating-average"
            value="average"
            name="rating"
            v-model="chosenRating"
          />
          <label for="rating-average">Average</label>
        </div>
        <div class="form-control">
          <input type="radio" id="rating-great" value="great" name="rating" v-model="chosenRating" />
          <label for="rating-great">Great</label>
        </div>
        <p
          v-if="invalidInput"
        >One or more input fields are invalid. Please check your provided data.</p>
        <div>
          <base-button>Submit</base-button>
        </div>
      </form>

    </base-card>


  </section>
</template>

<script>
export default {

  data() {
    return {
      enteredName: '',
      chosenRating: null,
      invalidInput: false,
    };
  },

  emits: ['survey-submit'],

  methods: {

    submitSurvey() {
      if (this.enteredName === '' || !this.chosenRating) {
        this.invalidInput = true;
        return;
      }
      this.invalidInput = false;

      // No need to $emit since we are sending this data to the server

      // this.$emit('survey-submit', {
      //   userName: this.enteredName,
      //   rating: this.chosenRating,
      // });

      fetch('https://vue-http-demo-97b72-default-rtdb.firebaseio.com/surveys.json' , {
          method : 'POST',
          headers : {
            'Content-Type' : 'application/json'
          } ,
          body : JSON.stringify({ name: this.enteredName , rating: this.chosenRating }) ,
      })

      this.enteredName = '';
      this.chosenRating = null;
    },
  },

};
</script>

<style scoped>
.form-control {
  margin: 0.5rem 0;
}

input[type='text'] {
  display: block;
  width: 20rem;
  margin-top: 0.5rem;
}
</style>