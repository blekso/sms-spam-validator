<template>
  <div class="relative flex items-top justify-center min-h-screen bg-gray-100 sm:items-center sm:pt-0">
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.1.2/dist/tailwind.min.css" rel="stylesheet">
    <div class="max-w-4xl mx-auto sm:px-6 lg:px-8">
      <a class="flex justify-center pt-8 sm:pt-0" href="https://www.ferit.unios.hr/2021/" target="_blank">
        <img src="ferit-logo.png">
      </a>
      <form class="mt-8 bg-white overflow-hidden shadow sm:rounded-lg p-6">
        <h2 class="text-2xl leading-7 font-semibold">
          Welcome to your SMS Spam Validator
        </h2>
        <div class="border-t border-b border-dashed my-4 py-4 ">
          <p class="text-gray-800">
            To get started enter your desired SMS message and click validate, have fun!
          </p>
          <div class="mt-6 flex">
            <div class="mr-4 w-full flex relative">
              <input v-model="inputData" class="shadow w-full appearance-none border rounded py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline " id="message" type="text" placeholder="SMS message">
              <img src="dice.png" class="valign z-10 cursor-pointer" @click="sentence">
            </div>
            <button @click="getValidation" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" type="button">
              Validate
            </button> 
          </div>
        </div>
        <div class="flex">
          <p class="text-gray-800 mr-4">
            Your SMS message is: 
          </p>
          <p v-if="!output" class="text-gray-400">waiting for validation</p>
          <p v-else class="text-green-500">{{output}} with {{probabilities}} probabilities</p>
        </div>
      </form>
      <div class="flex justify-center pt-4 space-x-2">
        <a href="https://github.com/blekso/sms-spam-validator" target="_blank"><svg
          class="w-6 h-6 text-gray-600 hover:text-gray-800 button--github"
          xmlns="http://www.w3.org/2000/svg"
          xmlns:xlink="http://www.w3.org/1999/xlink"
          aria-hidden="true"
          role="img"
          width="32"
          height="32"
          preserveAspectRatio="xMidYMid meet"
          viewBox="0 0 24 24"
        ><path d="M12 2.247a10 10 0 0 0-3.162 19.487c.5.088.687-.212.687-.475c0-.237-.012-1.025-.012-1.862c-2.513.462-3.163-.613-3.363-1.175a3.636 3.636 0 0 0-1.025-1.413c-.35-.187-.85-.65-.013-.662a2.001 2.001 0 0 1 1.538 1.025a2.137 2.137 0 0 0 2.912.825a2.104 2.104 0 0 1 .638-1.338c-2.225-.25-4.55-1.112-4.55-4.937a3.892 3.892 0 0 1 1.025-2.688a3.594 3.594 0 0 1 .1-2.65s.837-.262 2.75 1.025a9.427 9.427 0 0 1 5 0c1.912-1.3 2.75-1.025 2.75-1.025a3.593 3.593 0 0 1 .1 2.65a3.869 3.869 0 0 1 1.025 2.688c0 3.837-2.338 4.687-4.563 4.937a2.368 2.368 0 0 1 .675 1.85c0 1.338-.012 2.413-.012 2.75c0 .263.187.575.687.475A10.005 10.005 0 0 0 12 2.247z" fill="currentColor" /></svg></a>
      </div>
    </div>
  </div>
</template>

<script>
var verbs, nouns, adjectives, adverbs, preposition;
nouns = ["bird", "clock", "boy", "plastic", "duck", "teacher", "old lady", "professor", "hamster", "dog"];
verbs = ["kicked", "ran", "flew", "dodged", "sliced", "rolled", "died", "breathed", "slept", "killed"];
adjectives = ["beautiful", "lazy", "professional", "lovely", "dumb", "rough", "soft", "hot", "vibrating", "slimy"];
adverbs = ["slowly", "elegantly", "precisely", "quickly", "sadly", "humbly", "proudly", "shockingly", "calmly", "passionately"];
preposition = ["down", "into", "up", "on", "upon", "below", "above", "through", "across", "towards"];

export default {
  name: 'Validator',
  data() {
    return {
      inputData: '',
      output: '',
      probabilities: 0,
    }      
  },
  methods: {
    randGen() {
      return Math.floor(Math.random() * 5);
    },
    sentence() {
      var rand1 = Math.floor(Math.random() * 10);
      var rand2 = Math.floor(Math.random() * 10);
      var rand3 = Math.floor(Math.random() * 10);
      var rand4 = Math.floor(Math.random() * 10);
      this.inputData = "The " + adjectives[rand1] + " " + nouns[rand2] + " " + adverbs[rand3] + " " + verbs[rand4];
    },
    async getValidation(){
      const token = 'Bearer FlUaf2XgYe/b3BJkBlsV7wRrvJaXNuJp4Xqax4a25tLG9hygVRd6ctkx8zY1BFx2G4DTnX7MxSiYNs8iOGhg6g=='
      const URL = "https://ussouthcentral.services.azureml.net/workspaces/cdef93b81c194d23b631e9efdb6af565/services/ad000ea1767a49d083be747b4a1cb55b/execute?api-version=2.0&details=true";
      const res = await this.$axios.$post(URL, {"Inputs": {
          "input1": {
            "ColumnNames": [
              "v1",
              "v2"
            ],
            "Values": [
              [
                'yo', this.inputData
              ]
            ]
          }
        },
        "GlobalParameters": {}} ,{ headers: { Authorization: token } });
      console.log(res)
      this.output = res.Results.output1.value.Values[0][1025]
      if(this.output === 'ham'){
        this.probabilities = 100 - res.Results.output1.value.Values[0][1026] * 100
      } else {
        this.probabilities = res.Results.output1.value.Values[0][1026] * 100
      }
      
    }
  }
}
</script>

<style scoped>
.valign {
    position: absolute;
    top: 50%;
    right: 2.5%;
    transform: translateY(-50%);
}
</style>
