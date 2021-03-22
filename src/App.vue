<template>
  <div id="app" class="p-3">
    <div class="pb-5">
      <h3 class="text-lg leading-6 font-medium text-gray-900">IWM Helpdesk</h3>
    </div>

    <div class="shadow sm:rounded-md sm:overflow-hidden">
      <div class="bg-white py-6 px-4 space-y-6 sm:p-6">
        <div class="grid grid-cols-6 gap-6">
          <div class="col-span-6">
            <label for="domain" class="block text-sm font-medium text-gray-700"
              >Domain</label
            >
            <select
              v-model="selectedDomain"
              @change="onDomainChange"
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            >
              <option value=""></option>
              <option
                v-for="(domain, index) in categories"
                :value="index"
                :key="index"
              >
                {{ domain.label }}
              </option>
            </select>
          </div>

          <div class="col-span-6">
            <label
              for="last_name"
              class="block text-sm font-medium text-gray-700"
              >Category</label
            >
            <select
              v-model="selectedOption"
              class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            >
              <option value=""></option>
              <option v-for="(option, index) in options" :key="index">
                {{ option }}
              </option>
            </select>
          </div>

          <div class="col-span-6">
            <label for="about" class="block text-sm font-medium text-gray-700">
              Description
            </label>
            <div class="mt-1">
              <textarea
                v-model="description"
                id="about"
                name="about"
                rows="6"
                class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
              ></textarea>
            </div>
          </div>

          <div class="col-span-6" v-if="errors.length > 0">
            <!-- This example requires Tailwind CSS v2.0+ -->
            <div class="rounded-md bg-red-50 p-4">
              <div class="flex">
                <div class="">
                  <div class="mt-2 text-sm text-red-700">
                    <ul class="list-disc pl-5 space-y-1">
                      <li :key="error" v-for="error in errors">{{ error }}</li>
                    </ul>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <div class="col-span-6" v-if="error">
            <!-- This example requires Tailwind CSS v2.0+ -->
            <div class="rounded-md bg-red-50 p-4">
              <div class="flex">
                <div class="">
                  <div class="mt-2 text-sm text-red-700">
                    {{ error }}
                  </div>
                </div>
              </div>
            </div>
          </div>

          <div class="col-span-6" v-if="success">
            <!-- This example requires Tailwind CSS v2.0+ -->
            <div class="rounded-md bg-green-50 p-4">
              <div class="flex">
                <div class="flex-shrink-0">
                  <!-- Heroicon name: solid/check-circle -->
                  <svg
                    class="h-5 w-5 text-green-400"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 20 20"
                    fill="currentColor"
                    aria-hidden="true"
                  >
                    <path
                      fill-rule="evenodd"
                      d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"
                      clip-rule="evenodd"
                    />
                  </svg>
                </div>
                <div class="ml-3">
                  <p class="text-sm font-medium text-green-800">
                    {{ success }}
                  </p>
                </div>
                <div class="ml-auto pl-3">
                  <div class="-mx-1.5 -my-1.5">
                    <button
                      @click="removeSuccess"
                      class="inline-flex bg-green-50 rounded-md p-1.5 text-green-500 hover:bg-green-100 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-green-50 focus:ring-green-600"
                    >
                      <span class="sr-only">Dismiss</span>
                      <!-- Heroicon name: solid/x -->
                      <svg
                        class="h-5 w-5"
                        xmlns="http://www.w3.org/2000/svg"
                        viewBox="0 0 20 20"
                        fill="currentColor"
                        aria-hidden="true"
                      >
                        <path
                          fill-rule="evenodd"
                          d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z"
                          clip-rule="evenodd"
                        />
                      </svg>
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="px-4 py-3 bg-gray-50 text-right sm:px-6">
        <div v-if="disabled"></div>
        <button
          v-else
          type="button"
          @click="sendRequest"
          class="bg-indigo-600 border border-transparent rounded-md shadow-sm py-2 px-4 inline-flex justify-center text-sm font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
        >
          Create Ticket
        </button>
      </div>
    </div>
  </div>
</template>


 
<script>
//import HelloWorld from './components/HelloWorld.vue'
import "./assets/tailwind.css";
var axios = require("axios");
let os = require("os");

export default {
  name: "App",
  components: {},
  data: function () {
    return {
      userinfo: [],
      disabled: false,
      categories: [
        {
          label: "SAP",
          options: [
            "Authorization",
            "Business Partner Update",
            "Control Deployment",
            "Data Required",
            "Document Closure/Cancel",
            "Duplicate / Invalid Ticket",
            "Order Release",
            "Report Development",
            "Report Update",
            "System Adminstration",
          ],
        },
        {
          label: "IT Operations / Infrastructure",
          options: [
            "Hardware Issue",
            "Software Issue",
            "Meeting Arrangement",
            "Email Issue",
            "Network Issue",
            "Passive Network",
            "System Administrator",
            "PC Shifting",
            "Printer",
            "RDP Issue",
          ],
        },
        {
          label: "Admin",
          options: [],
        },
        {
          label: "Product",
          options: ["Description", "Dimention", "Picture", "Pricing"],
        },
        {
          label: "Engineering",
          options: [],
        },
      ],
      options: [],
      selectedDomain: "",
      selectedOption: "",
      domain: "",
      success: "",
      error: "",
      description: "",
      errors: [],
    };
  },
  methods: {
    onDomainChange: function () {
      this.options = [];
      this.domain = "";
      if (this.selectedDomain !== "") {
        this.options = this.categories[this.selectedDomain].options;
        this.domain = this.categories[this.selectedDomain].label;
      }
    },
    removeSuccess: function () {
      this.success = "";
    },
    sendRequest: function () {
      var data = JSON.stringify({
        username: this.userinfo.username,
        domain: this.domain,
        category: this.selectedOption,
        description: this.description,
        payload: {
          userinfo: os.userInfo(),
          hostname: os.hostname(),
          network: os.networkInterfaces(),
        },
      });
      var config = {
        method: "post",
        url: "http://192.168.1.40/helpdesk/create",
        data: data,
      };
      let that = this;
      this.disabled = true;
      that.success = "";
      that.error = "";
      axios(config)
        .then(function (response) {
          // var obj = response.data;

          if (response.data.status == 1) {
            that.domain = "";
            that.category = "";
            that.domain = "";
            that.selectedOption = "";
            that.selectedDomain = "";
            that.description = "";
            that.success = "Ticket has been created.";
            that.disabled = false;
          } else {
            that.errors = [];
            that.errors = response.data.errors;
            that.disabled = false;
          }
        })
        .catch(function (error) {
          console.log(error);
          that.error = "Something went wrong.";
          that.disabled = false;
        });
    },
  },
  mounted() {
    this.userinfo = os.userInfo();
    this.success = "";
    this.error = "";
  },
};
</script>
 
