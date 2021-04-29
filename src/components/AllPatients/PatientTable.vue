<template>
  <div>
      <div class="flex justify-end">
        <input
            type="text"
            class="w-1/4 bg-white my-4 rounded p-2 focus:outline-none focus:ring focus:border-blue-300 shadow-md"
            v-model="search"
            placeholder="Search"
            v-on:keyup="getPatients(null)"
        />
      </div>
    <table class="stripe hover border-2 shadow-md border-gray-300 w-full">
      <tr>
        <th v-for="th in headers" :key="th.value">{{ th.text }}</th>
      </tr>
      <tr v-for="patient in patients" :key="patient.id">
        <td>{{ patient.id }}</td>
        <td>{{ patient.firstName }}</td>
        <td>{{ patient.lastName }}</td>
        <td>{{ patient.category }}</td>
        <td>{{ patient.dob }}</td>
        <td>{{ patient.insurance }}</td>
        <td>{{ patient.drug }}</td>
      </tr>
    </table>
    <div class="flex justify-end mt-2">
        <button class="p-2 bg-blue-200 mr-1 rounded-md text-xs hover:bg-blue-300 transition delay-75" 
                title="first page"
                @click="getPatients(pagination.firstPage)">
            <svg class="w-4 h-4 inline-block" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 19l-7-7 7-7m8 14l-7-7 7-7"></path></svg>
        </button>
        <button class="p-2 bg-blue-200 mr-1 rounded-md text-xs hover:bg-blue-300 transition delay-75" 
                title="previous page"
                @click="getPatients(pagination.previousPage)">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"></path></svg>
        </button>
        <button class="p-2 bg-blue-200 mr-1 rounded-md text-xs hover:bg-blue-300 transition delay-75" 
            title="next page"
            @click="getPatients(pagination.nextPage)">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"></path></svg>
        </button>
        <button class="p-2 bg-blue-200 mr-1 rounded-md text-xs hover:bg-blue-300 transition delay-75" 
                title="last page"
                @click="getPatients(pagination.lastPage)">
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 5l7 7-7 7M5 5l7 7-7 7"></path></svg>
        </button>
    </div>
  </div>
</template>

<script>
import  axios  from "axios";
export default {
  data() {
    return {
        search: '',
        pagination: {
            firstPage: null,
            lastPage: null,
            nextPage: null,
            previousPage: null,

        },
        headers: [
            { text: "Id", value: "Id", visible: true },
            { text: "First Name", value: "firstName", visible: true },
            { text: "Last Name", value: "lastName", visible: true },
            { text: "Category", value: "category", visible: true },
            { text: "Date of Birth", value: "dob", visible: true },
            { text: "Insurance", value: "insurance", visible: true },
            { text: "Drug", value: "drug", visible: true },
        ],
        patients: [],
    };
  },
  mounted() {
      this.getPatients(null);
  },
  methods: {
    getPatients(fullUrl = null) {
        let apiUrl = '';
        if(fullUrl !== null) {
            apiUrl = fullUrl;
        } else {
            const url = process.env.VUE_APP_API_URL;
            apiUrl = `${url}/patients`
        }
        if(this.search !== '') {
            apiUrl += `?SearchText=${this.search}`
        }
        axios.get(apiUrl)
            .then(res => {
                res = res.data;
                this.patients = res.data;
                this.pagination.nextPage = res.nextPage;
                this.pagination.firstPage = res.firstPage;
                this.pagination.lastPage = res.lastPage;
                this.pagination.previousPage = res.previousPage;
                console.log(res)
            })
    },
  },
};
</script>

<style>
td {
    border: 1px solid  rgb(218, 218, 218);
    padding: 10px;
}

th {
    padding: 20px;
    border-right: 1px solid rgb(218, 218, 218);
}
</style>