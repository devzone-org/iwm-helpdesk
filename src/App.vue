<template>
  <div id="app" class="p-3 bg-gray-50">
    <!-- This example requires Tailwind CSS v2.0+ -->
    <div class="lg:flex lg:items-center lg:justify-between pb-5">
      <div class="flex-1 min-w-0">
        <h2 class="text-2xl font-bold leading-7 text-gray-900 sm:text-3xl sm:truncate">
          IWM Helpdesk
        </h2>
        <div class="mt-1 flex flex-col sm:flex-row sm:flex-wrap sm:mt-0 sm:space-x-6" v-if="create_ticket == true">
          <div class="mt-2 flex items-center text-sm text-gray-500">
            <!-- Heroicon name: solid/briefcase -->
            <svg
                class="flex-shrink-0 mr-1.5 h-5 w-5 text-gray-400"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
                xmlns="http://www.w3.org/2000/svg">
              <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"></path>
            </svg>

            {{ employee_info["first_name"] }} {{ employee_info["last_name"] }}
          </div>
          <div class="mt-2 flex items-center text-sm text-gray-500">
            <!-- Heroicon name: solid/location-marker -->
            <svg class="flex-shrink-0 mr-1.5 h-5 w-5 text-gray-400"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
                xmlns="http://www.w3.org/2000/svg">
              <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M20 13V6a2 2 0 00-2-2H6a2 2 0 00-2 2v7m16 0v5a2 2 0 01-2 2H6a2 2 0 01-2-2v-5m16 0h-2.586a1 1 0 00-.707.293l-2.414 2.414a1 1 0 01-.707.293h-3.172a1 1 0 01-.707-.293l-2.414-2.414A1 1 0 006.586 13H4"></path>
            </svg>
            {{ employee_info["email"] }}
          </div>
          <div class="mt-2 flex items-center text-sm text-gray-500">
            <!-- Heroicon name: solid/location-marker -->
            <button type="button" @click="removeInfo">
              <svg xmlns="http://www.w3.org/2000/svg" class="flex-shrink-0 mr-1.5 h-5 w-5 text-red-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
              </svg>
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="shadow rounded-md overflow-hidden" v-if="create_ticket == true">
      <div class="bg-white py-6 px-4 space-y-6">
        <div class="grid grid-cols-6 gap-6">
          <div class="col-span-6">
            <label for="domain" class="block text-sm font-medium text-gray-700"
            >Domain</label>
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
        <button
            type="button"
            @click="sendRequest"
            class="bg-indigo-600 border border-transparent rounded-md shadow-sm py-2 px-4 inline-flex justify-center text-sm font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
        >
          Create Ticket
        </button>
      </div>
    </div>

    <div class="shadow sm:rounded-md sm:overflow-hidden" v-if="create_ticket == false">
      <div class="bg-white py-6 px-4 space-y-6 sm:p-6">
        <div>
          <h3 class="text-lg leading-6 font-medium text-gray-900">
            Employee Information
          </h3>
        </div>

        <div class="grid grid-cols-6 gap-6">
          <div class="col-span-6 sm:col-span-3">
            <label for="first_name" class="block text-sm font-medium text-gray-700">Employee ID</label>
            <input
                type="text"
                name="first_name"
                v-model="employee_id"
                autocomplete="given-name"
                class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
            />
          </div>
          <div class="col-span-3">
            <div class="mt-5 flex">
              <button
                  type="button"
                  @click="searchEmployee"
                  class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:w-auto sm:text-sm"
              >
                Search
              </button>
              <button
                  type="button"
                  class="w-full inline-flex justify-center rounded-md border border-transparent shadow-sm px-4 py-2 bg-red-600 text-base font-medium text-white hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500 sm:ml-3 sm:w-auto sm:text-sm"
                  @click="resetEmployee"
              >
                Reset
              </button>
            </div>
          </div>

          <div class="col-span-6">
            <!-- This example requires Tailwind CSS v2.0+ -->

            <table class="min-w-full divide-y divide-gray-200">
              <tbody class="bg-white divide-y divide-gray-200">
              <tr>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 bg-gray-50"
                >
                  Name
                </td>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 bg-gray-50"
                >
                  {{ employee_info["first_name"] }}
                  {{ employee_info["last_name"] }}
                </td>
              </tr>

              <tr>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 bg-gray-50"
                >
                  Email
                </td>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 bg-gray-50"
                >
                  {{ employee_info["email"] }}
                </td>
              </tr>

              <tr>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 bg-gray-50"
                >
                  Department
                </td>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 bg-gray-50"
                >
                  {{ employee_info["department"] }}
                </td>
              </tr>

              <tr>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 bg-gray-50"
                >
                  Designation
                </td>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 bg-gray-50"
                >
                  {{ employee_info["designation"] }}
                </td>
              </tr>

              <tr>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 bg-gray-50"
                >
                  Cadre
                </td>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 bg-gray-50"
                >
                  {{ employee_info["cadre"] }}
                </td>
              </tr>

              <tr>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900 bg-gray-50"
                >
                  Location
                </td>
                <td
                    class="px-6 py-4 whitespace-nowrap text-sm text-gray-500 bg-gray-50"
                >
                  {{ employee_info["location"] }}
                </td>
              </tr>
              </tbody>
            </table>
          </div>
          <div class="col-span-6">
            <div class="col-span-6" v-if="errors.length > 0">
              <!-- This example requires Tailwind CSS v2.0+ -->
              <div class="rounded-md bg-red-50 p-4">
                <div class="flex">
                  <div class="">
                    <div class="mt-2 text-sm text-red-700">
                      <ul class="list-disc pl-5 space-y-1">
                        <li :key="error" v-for="error in errors">
                          {{ error }}
                        </li>
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

          <div class="col-span-6" v-if="employee_info.id">
            <button
                type="button"
                @click="approveEmployee"
                class="bg-indigo-600 border border-transparent rounded-md shadow-sm py-2 px-4 inline-flex justify-center text-sm font-medium text-white hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
              Confirm
            </button>
          </div>
        </div>
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
      employee_info: [],
      create_ticket: false,
      employee_id: "",
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
    approveEmployee: function () {
      var data = JSON.stringify({
        username: this.userinfo.username,
        employee_id: this.employee_info["id"],
      });
      var config = {
        method: "post",
        url: "http://interwood.test/helpdesk/employee/update/app",
        data: data,
      };
      let that = this;

      that.success = "";
      that.errors = [];
      that.error = "";
      that.create_ticket = false;
      axios(config)
          .then(function (response) {
            // var obj = response.data;
            if (response.data.status == 1) {
              that.success = "Employee has been linked to helpdesk.";
              that.disabled = false;
              that.create_ticket = true;
            } else {
              that.errors = [];
              that.errors = response.data.errors;
              that.disabled = false;
            }
          })
          .catch(function (error) {
            console.log(error);
            that.error = "Something went wrong." + error;
            that.disabled = false;
          });
    },
    resetEmployee: function () {
      this.employee_info = [];
      this.employee_id = "";
    },
    searchEmployee: function () {
      var config = {
        method: "get",
        url: "http://interwood.test/helpdesk/search/employee/" + this.employee_id,
      };
      let that = this;
      this.disabled = true;
      that.success = "";
      that.error = "";
      that.create_ticket = false;
      axios(config)
          .then(function (response) {
            // var obj = response.data;

            if (response.data.status == 1) {
              that.success = "Employee has been loaded.";
              that.disabled = false;

              that.employee_info = response.data.response;
            } else {
              that.error = "No record found";
              //that.errors = response.data.errors;
              that.disabled = false;
            }
          })
          .catch(function (error) {
            console.log(error);
            that.error = "Something went wrong." + error;
            that.disabled = false;
          });
    },
    loadDefault: function () {
      var data = JSON.stringify({
        username: this.userinfo.username,
      });
      var config = {
        method: "post",
        url: "http://interwood.test/helpdesk/employee/load/default",
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
              that.create_ticket = true;
              that.employee_info = response.data.response;
            } else {
              that.create_ticket = false;
              that.errors = [];
              //that.errors = response.data.errors;
              that.disabled = false;
            }
          })
          .catch(function (error) {
            console.log(error);
            that.create_ticket = false;
            that.error = "Something went wrong." + error;
            that.disabled = false;
          });
    },
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
        url: "http://interwood.test/helpdesk/create",
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
            that.error = "Something went wrong." + error;
            that.disabled = false;
          });
    },
    removeInfo:function (){
      var data = JSON.stringify({
        username: this.userinfo.username,
        employee_id: this.employee_info["id"],
      });
      var config = {
        method: "post",
        url: "http://interwood.test/helpdesk/employee/remove/app",
        data: data,
      };
      let that = this;

      that.success = "";
      that.errors = [];
      that.error = "";
      that.create_ticket = true;
      axios(config)
          .then(function (response) {
            // var obj = response.data;
            if (response.data.status == 1) {
              that.employee_info=[];
              that.employee_id="";
              that.success = "Employee has been unlinked from helpdesk.";
              that.disabled = false;
              that.create_ticket = false;
            } else {
              that.errors = [];
              that.errors = response.data.errors;
              that.disabled = false;
            }
          })
          .catch(function (error) {
            console.log(error);
            that.error = "Something went wrong." + error;
            that.disabled = false;
          });
    },
  },
  mounted() {
    this.userinfo = os.userInfo();
    this.success = "";
    this.error = "";
    this.loadDefault();
  },
};
</script>
 
