
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


<!-- Http Requests & Http Methods (Verbs)
=================================================================================

In the last lecture, we sent a POST request to a REST API.

What is that? A POST request? And a REST API?

There are different "kinds" of Http requests you could say - defined by the method (POST, GET, DELETE, ...) you attach to them (via the "method" you define on an outgoing request).

And the server to which you're sending those requests may then react in which ever way it is configured to react to incoming requests with different methods.

It may store data in a database for an incoming POST request, it may fetch data for a GET request.

Typically, servers are built to work as a "REST API" - that means they have clearly defined "endpoints" (URL + Http method combinations) for which they do different things.

You can learn more about REST APIs (and how to build your own one!) with this free series: 

https://academind.com/learn/node-js/building-a-restful-api-with/what-is-a-restful-api-/

Also make sure you understand, in general, how the web works:

 https://academind.com/learn/web-dev/how-the-web-works/


-->


<!--Using Axios Instead Of "fetch()"
=======================================================================================

In this course, we use the native fetch() API for sending Http requests. It's built into the browser and hence we don't have to install any extra package to use it.

If you prefer third-party libraries like Axios (https://github.com/axios/axios) you can of course also use such libraries though.

For example, you could replace the fetch() code from the last lecture with this Axios code:

Instead of:

fetch('https://vue-http-demo-85e9e.firebaseio.com/surveys.json', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    name: this.enteredName,
    rating: this.chosenRating,
  }),
});
you can write this code with Axios:

import axios from 'axios'; // at the start of your <script> tag, before you "export default ..."
...
axios.post('https://vue-http-demo-85e9e.firebaseio.com/surveys.json', {
  name: this.enteredName,
  rating: this.chosenRating,
});
As you can see, with Axios, you have to write less code. It automatically sets the Content-Type header for you, it also automatically converts the body data to JSON.

BUT - as a downside - you have to add an extra package, which ultimately increases the size of the web app you're shipping in the end.

Later in the module, we'll also start reacting to the response returned by the request.

fetch() returns a Promise, hence we can use then(), catch() and async/ await there. For Axios, it's just the same - it also returns a Promise.


-->


<!-- HANDLING ERROR RESPONSES

-We can also check if there is any error when submitting data.

- For us to test this let's manipulate the url and maybe remove the .json - Firebase will not be able to submit this POST request to this resource 

-We use catch () block to handle browser /technical side errors 
-Let's say network errors or such cases
e.g. 

  .catch( error => {
        console.log(error);
        this.isError =  error.message
      } )

  -However errors from the server side are handled at the response level by checking the statusCode and statusText and then throw new Error('message) which is automatically passed to the catch method

  -Suppose we omit to JSON.stringify and send our body as an object
       body : { name: this.enteredName , rating: this.chosenRating } ,

  -We can handle this error at the response level by checking statusCode /statusText;

  -If there is one we  throw new Error('message) - this message is then passed to the catch method and we can access it by just using      error.message

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

        <p v-if="isError"> {{ isError }}</p>
        <div>
          <base-button>Submit</base-button>
        </div>
      </form>

    </base-card>


  </section>
</template>

<script>


//Uncomment If you want to use axios instead
// import axios from 'axios';
// import VueAxios from 'vue-axios';

export default {

  data() {
    return {
      enteredName: '',
      chosenRating: null,
      invalidInput: false,
      isError : null,
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

      this.isError = null;

      fetch('https://vue-http-demo-97b72-default-rtdb.firebaseio.com/surveys.json' , {
          method : 'POST',
          headers : {
            'Content-Type' : 'application/json'
          } ,
          body : JSON.stringify({ name: this.enteredName , rating: this.chosenRating }) ,
      })
      .then( response => {

        if( response.status !== 200 && response.statusText !== 'OK' ){
          throw new Error('Could not submit data')
        }

      } )
      .catch( error => {
        console.log(error);
        this.isError =  error.message
      } )


      // USING AXIOS
      // axios.post('https://vue-http-demo-97b72-default-rtdb.firebaseio.com/surveys.json', {
      //   name: this.enteredName,
      //   rating: this.chosenRating,
      // });

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