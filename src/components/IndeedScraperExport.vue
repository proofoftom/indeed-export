<template>
  <form class="md-layout">
    <md-card class="md-layout-item md-size-40 md-small-size-65 md-medium-size-50 md-xsmall-size-90">

      <md-card-header>
        <div class="md-title">Search Job Postings</div>
      </md-card-header>

      <md-card-content>
        <div class="md-layout-item">
          <md-field>
            <label for="query">Search Terms</label>
            <md-input type="text" name="query" v-model="query" required/>
            <span class="md-error">This field is required</span>
          </md-field>
        </div>

        <div class="md-layout-item">
          <md-field>
            <label for="location">Location</label>
            <md-input type="text" name="location" v-model="location" />
          </md-field>
        </div>

        <div class="md-layout-item">
          <md-field>
            <label for="jobType">Job Type</label>
            <md-select name="jobType" v-model="jobType">
              <md-option value="fulltime">Full Time</md-option>
              <md-option value="parttime">Part Time</md-option>
            </md-select>
          </md-field>
        </div>

        <div class="md-layout-item">
          <md-field>
            <label for="maxAge">Max Age</label>
            <md-input type="number" name="maxAge" v-model="maxAge" />
            <span class="md-suffix">Days</span>
          </md-field>
        </div>
      </md-card-content>

      <md-card-actions>
        <md-button @click="scrapeResults">Scrape Results</md-button>
      </md-card-actions>
      <md-progress-spinner v-show="scraping" :md-diameter="30" :md-stroke="3" md-mode="indeterminate" />

      <md-card-actions v-if="jsonData !== null && jsonData.length > 0">
        <download-csv :data="jsonData">
          <md-button>
            Export To CSV
          </md-button>
        </download-csv>
      </md-card-actions>
    </md-card>
    
    <md-snackbar :md-duration="4000" :md-active.sync="showSnackbar" md-persistent>
      <span>Results Scraped!</span>
    </md-snackbar>
  </form>
</template>

<script>
import Indeed from 'indeed-scraper';

export default {
  name: 'IndeedScraperExport',
  data() {
    return ({
      query: '',
      location: '',
      jobType: '',
      maxAge: 0,
      scraping: false,
      jsonData: null,
      showSnackbar: false,
    })
  },
  methods: {
    scrapeResults() {
      const queryOptions = {
        query: this.query,
        location: this.location,
        jobType: this.jobType,
        maxAge: this.maxAge,
        sort: 'date',
        limit: '0',
      };

      this.jsonData = null;
      this.scraping = true;

      Indeed.query(queryOptions).then(res => {
        this.scraping = false;
        this.showSnackbar = true;
        this.saveResults(res); // An array of Job objects
      });
    },
    saveResults(res) {
      this.jsonData = res;
      // eslint-disable-next-line
      console.log(this.jsonData, 'Done');
    },
    viewResults() {
      // Todo: Add display for results
    },
  },
}
</script>

<style scoped>
.md-card {
  margin: auto;
  padding-bottom: 20px;
}

.md-card-actions {
  display: inline;
}
</style>
