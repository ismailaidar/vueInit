<template>
  <div>
      <div class="grid grid-cols-4 gap-4">
        <select-input class="col-span-1" :elements="headers" @selectValue="toggleHeader"/>
        <div class="col-span-2"></div>
        <input
            type="text"
            class="w-full h-10 bg-white my-4 rounded p-2 focus:outline-none focus:ring focus:border-blue-300 shadow-md col-span-1"
            v-model="search"
            placeholder="Search"
            v-on:keyup="getPatients(null)"
        />
      </div>
    <table class="stripe hover border-2 shadow-md border-gray-300 w-full">
      <tr>
        <th v-for="th in headers" :key="th.value" class="cursor-pointer" @click="sortCol(th.value)" v-show="th.visible">
          {{ th.text }}
          <span v-if="th.sortable" class="inline-block">
            <svg v-if="th.sortType == 'desc'" class="w-2 h-2" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 13l-7 7-7-7m14-8l-7 7-7-7"></path>
            </svg>
            <svg v-if="th.sortType == 'asc'"  class="w-2 h-2" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 11l7-7 7 7M5 19l7-7 7 7"></path>
            </svg>
          </span>
        </th>
      </tr>
      <tr v-for="patient in patients" :key="patient.id">
        <td v-show="isColumnVisible('Id')">{{ patient.id }}</td>
        <td v-show="isColumnVisible('FirstName')">{{ patient.firstName }}</td>
        <td v-show="isColumnVisible('LastName')">{{ patient.lastName }}</td>
        <td v-show="isColumnVisible('Category')">{{ patient.category }}</td>
        <td v-show="isColumnVisible('Dob')">{{ patient.dob }}</td>
        <td v-show="isColumnVisible('Insurance')">{{ patient.insurance }}</td>
        <td v-show="isColumnVisible('Drug')">{{ patient.drug }}</td>
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
import SelectInput from './SelectInput.vue';
export default {
  components: { SelectInput },
  data() {
    return {
        hiddenCols: [],  // to store what cols to show or not
        search: '', // for searchbox
        sortTypes: ['asc', 'desc', 'none'],
        pagination: {
            firstPage: null,
            lastPage: null,
            nextPage: null,
            previousPage: null,

        },
        headers: [
            { text: "Id", value: "Id", visible: true, sortable: true, sortType: 'none' },
            { text: "First Name", value: "FirstName", visible: true, sortable: true, sortType: 'none' },
            { text: "Last Name", value: "LastName", visible: true , sortable: true, sortType: 'none'},
            { text: "Category", value: "Category", visible: true, sortable: true, sortType: 'none' },
            { text: "Date of Birth", value: "Dob", visible: true , sortable: true, sortType: 'none'},
            { text: "Insurance", value: "Insurance", visible: true , sortable: true, sortType: 'none'},
            { text: "Drug", value: "Drug", visible: true, sortable: true, sortType: 'none' },
        ],
        patients: [],
    };
  },
  mounted() {
      this.hiddenCols = []
      this.getPatients(null)
      this.getHiddenCols()
  },
  methods: {
    getHiddenCols() {
        if(localStorage.getItem('hiddenCols') != undefined) {
          this.hiddenCols = [];
          this.hiddenCols = JSON.parse(localStorage.getItem('hiddenCols'));
        }
        for (const el of this.headers) {
          if(this.hiddenCols.some(o => o == el.value)) {
            this.toggleHeader(el)
          }
        }
    },
    getPatients(fullUrl = null) {
        let apiUrl = '';
        if(fullUrl !== null) {
            apiUrl = fullUrl;
        } else {
            const url = process.env.VUE_APP_API_URL;
            apiUrl = `${url}/patients`
        }
        // filtering
        if(this.search !== '') {
            if(apiUrl.includes('?')) {
              apiUrl += `&SearchText=${this.search}`
            } else {
              apiUrl += `?SearchText=${this.search}`
            }
        }
        // sorting
        this.headers.forEach(function(header) {
          if(header.sortType != undefined && header.sortType !== 'none') {
            if(apiUrl.includes('?')) {
              apiUrl += `&Sort=${header.value},${header.sortType}`
            } else {
              apiUrl += `?Sort=${header.value},${header.sortType}`
            }
          }
        })
        axios.get(apiUrl)
            .then(res => {
                res = res.data;
                this.patients = res.data;
                this.pagination.nextPage = res.nextPage;
                this.pagination.firstPage = res.firstPage;
                this.pagination.lastPage = res.lastPage;
                this.pagination.previousPage = res.previousPage;
            })
    },
    sortCol(colValue) {
      const th = this.headers.find(th => th.value == colValue);
      if(th.sortType == 'none') th.sortType = 'asc'
      else if(th.sortType == 'asc') th.sortType = 'desc'
      else th.sortType = 'none'
      this.getPatients(null)
    },
    isColumnVisible(name) {
      const col = this.headers.filter( o => o.value == name)[0];
      return col.visible;
    },
    toggleHeader(header) {
      const col = this.headers.filter( o => o == header)[0];
      col.visible = !col.visible;
      if(col.visible == true) {
        this.hiddenCols = this.hiddenCols.filter(o => o !== col.value)
      } else {
        this.hiddenCols.push(header.value);
      }
      localStorage.removeItem('hiddenCols')
      localStorage.setItem('hiddenCols', JSON.stringify(this.hiddenCols))
    }
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