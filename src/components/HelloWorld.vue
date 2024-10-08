<script lang="ts">

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      theData: {},
      tempData: {},
      resultSet: [],
      tempResultSet: [],
      currentPage: 1,
      total: 0,
      imgURL: 'https://media.nfsacollection.net/',
      query: 'https://api.collection.nfsa.gov.au/search?limit=25&hasMedia=yes&query=',
      searchString: 'film poster',
      defaultsearchString: 'film poster',
      selectedCategories: []
    }
  },
  mounted() {
    this.fetchData()
    // If there's already results, show them
    if (store) {
      this.resultSet = store.theStoredData
    } else {
      this.resultSet = []
    }
  },
  computed: {
    filteredItems() {
      if (this.selectedCategories.length === 0) {
        return this.resultSet // Return all items if no category is selected
      }
      return this.resultSet.filter(
        (item) =>
          (item['forms'] && this.selectedCategories.includes(item['forms'][0])) ||
          (item['countries'] && this.selectedCategories.includes(item['countries'][0])) ||
          (item['parentTitle'] &&
            item['parentTitle']['genres'] &&
            this.selectedCategories.includes(item['parentTitle']['genres'][0]))
      )
    }
  },
  methods: {
    fetchData() {
      // use dynamic data to modify the API call
      // in this case we use a text box which sets searchString
      // and the currentPage to allow us to loop through the paginated results
      if (this.searchString == '') {
        this.searchString = this.defaultsearchString
      }
      let queryString = this.query + this.searchString + '&page=' + this.currentPage
      console.log('API call: ' + queryString)
      fetch(queryString)
        .then((response) => {
          response.json().then((res) => {
            // build a temporary object, add the data from the current page on each call of fetchData()
            this.$data.tempData = { ...this.$data.tempData, ...res }
            // the same as above but with just the results array instead of the whole data object
            this.$data.tempResultSet = this.$data.tempResultSet.concat(res.results)
            // total items from the meta object (total number of items found in the search)
            this.$data.total = res.meta.count.total
            // if there are items
            if (this.$data.total > 0) {
              // check how many pages of results @ 25 per page
              if (this.currentPage * 25 < 500 && this.currentPage * 25 < this.$data.total) {
                // go to the next page
                this.currentPage++
                // call this function on itself (recursive) ! be careful, this can cause an infinite loop
                this.fetchData()
              } else {
                this.$data.theData = this.$data.tempData
                this.$data.tempData = {}
                console.log(this.$data.resultSet)
                this.$data.resultSet = this.$data.tempResultSet
                this.$data.tempResultSet = []
                // all items loaded, reset page, ready for next query
                this.currentPage = 1
                console.log('Pages: ' + Math.ceil(this.$data.total / 25))
                console.log('finished')
                updateStore(this.$data.resultSet)
              }
            } else {
              console.log('no results')
            }
          })
        })
        .catch((err) => {
          console.error(err)
        })
    },
    clearResults() {
      this.$data.resultSet = []
    }
  }
}

// Use a Pinia store to hold the API results through the whole app
// Access the data by importing the store
import { useSearchDataStore } from '@/stores/searchData'
var store: any
function updateStore(newData: any) {
  // Define a variable to represent the store
  store = useSearchDataStore()
  // Then use store.theStoredData to get/set the collection
  store.theStoredData = newData
  return {
    theStoredData: store.theStoredData
  }
}

</script>

<template>
  <div class="search">
    <h1 class="heading">{{ msg }}</h1>

    <div>
      <!-- <form action="" class="search-bar"> -->
      <input class="search-bar" v-model="searchString" placeholder="Search">
      <div class="bttn">
        <button class="enter" @click="fetchData">Search</button>
        <button class="reset" @click="clearResults">Clear Results</button>
      </div>


      <!-- </form> -->
    </div>

    <p>Total: {{ total }}</p>

    <p>Filter Items with Checkboxes</p>

    <div class="section-filters">
      <div class="filters">
        <div class="genre-filter">
          <h2 class="head-cata">Genre</h2>
          <label>
            Drama <input type="checkbox" value="Drama" v-model="selectedCategories" />
          </label>
          <label>
            Comedy <input type="checkbox" value="Comedy" v-model="selectedCategories" />
          </label>
          <label>
            Adventure <input type="checkbox" value="Adventure" v-model="selectedCategories" />
          </label>
          <label>
            Indigineous <input type="checkbox" value="Indigenous as subject" v-model="selectedCategories" />
          </label>
        </div>

        <div class="form-filter">
          <h2 class="head-cata">Form</h2>
          <label>
            Poster <input type="checkbox" value="Poster" v-model="selectedCategories" />
          </label>
          <label>
            Still Image <input type="checkbox" value="Still Image" v-model="selectedCategories" />
          </label>
          <label>
            Lobby Card <input type="checkbox" value="Lobby Card" v-model="selectedCategories" />
          </label>
        </div>
      </div>
    </div>
  </div>

  <ul role="list" class="list-v">
    <!-- create a variable called result, 
      loop through the API results and add a list item for each result.
      Use result to access properties like 'title' and 'name' -->
    <li v-for="(result, index) in filteredItems" :key="result[index]">
      <!-- <li v-for="(result, index) in resultSet" :key="result[index]"> -->
      <p class="title">{{ result['title'] }}</p>
      <p>{{ result['name'] }}</p>
      <!-- check if there's any items in the preview array.  If so, put the biggest image in the view -->
      <!-- v-bind is used to update the src attribute when the data comes in -->
      <Transition>
        <img v-if="result['preview'] && result['preview'][0]" v-bind:src="imgURL + result['preview'][0]['filePath']"
          v-bind:alt="result['name']" v-bind:title="result['name']">
      </Transition>
    </li>
  </ul>
</template>

<style scoped>
img {
  display: inline-block;
  max-width: 100%;
}

ul {
  padding: 0;
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center;
}

li {
  max-width: 300px;
  padding: 0.5rem;
  border: 1px solid #ffffff33;
  justify-content: center;
}

li img {
  margin-top: 10px;
}

p.title {
  color: #fff;
}

h1 {
  font-weight: 500;
  font-size: 2rem;
  position: relative;
  color: #fff;
}

.head-cata {
  color: #fff;
}

h3 {
  font-size: 1.2rem;
}

.search h1,
.search h3 {
  text-align: center;
}

.search-bar {
  max-width: 1000px;
  display: flex;
  justify-content: center;
  align-items: center;
  align-content: center;
  border-radius: 50px;
  flex: 1;
  border: 0;
  outline: none;
}

input {
  cursor: pointer;
  width: 100%;
  max-width: 1000px;
  display: flex;
  justify-content: space-between;
  border-radius: 50px;
  padding: 10px 20px;
  flex: 0;
  border: 0;
  outline: none;
}

label {
  display: flex;
  justify-content: space-between;
}

.filters {
  display: grid;
  gap: 20px;
  grid-template-columns: 1fr 1fr;
  padding-top: 10px;
  padding-bottom: 2rem;
}

.enter {
  margin-top: 5px;
  border-radius: 25px;
  padding: 5px 10px;
  border: 0;
  outline: none;
  cursor: pointer;
}

.bttn {
  display: flex;
  justify-content: space-between;
}


.reset {
  margin-top: 5px;
  display: inline-block;
  border-radius: 25px;
  padding: 5px 10px;
  border: 0;
  outline: none;
  cursor: pointer;
}



@media (max-width: 1023px) {
  .search {
    margin-left: 8rem;
    margin-right: 8rem;
  }

  .search h1,
  .search h3 {
    text-align: center;
  }
}

@media (max-width:817px) {
  .heading {
    margin-left: 2rem;
    margin-right: 2rem;
  }
}

@media (max-width:753px) {
  .heading {
    font-size: 28px;
  }
}

@media (max-width: 768px) {
  .search {
    margin-left: 6rem;
    margin-right: 6rem;
  }

  .reset {
    margin-left: 10px;
  }
}


@media (max-width: 485px) {
  .heading {
    margin-right: 0px;
    margin-left: 0px;
  }

  .search {
    margin-left: 1rem;
    margin-right: 1rem;
  }

  .filters {
    margin-left: 1vh;
  }

  .reset,
  .enter {
    font-size: 12px;
  }

  .heading {
    font-size: 28px;
  }

  .head-cata {
    font-size: 18px;
  }

  p,
  label {
    font-size: 12px;
  }

}

@media (max-width: 320px) {
  .heading {
    font-size: 20px;
  }

  .search-bar {
    border-radius: 25px;
    padding: 5px 10px;
  }

  .filters {
    margin-left: -0.5vh;
    grid-column: auto;
  }
}
</style>