<template>
  <form>
    <md-card class="md-layout-item md-size-40 md-small-size-65 md-medium-size-50 md-xsmall-size-90">

      <md-card-header>
        <div class="md-title">Scrape Job Postings</div>
      </md-card-header>

      <md-card-content>
        <div class="md-layout-item">
          <md-field>
            <label for="query">Publisher ID</label>
            <md-input type="text" name="query" v-model="publisherId" required/>
            <span class="md-error">This field is required</span>
          </md-field>
        </div>

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

        <div class="md-layout-item">
          <md-field>
            <label for="maxAge">Limit</label>
            <md-input type="number" name="maxAge" v-model="limit" />
            <span class="md-suffix">Jobs</span>
          </md-field>
        </div>
      </md-card-content>

      <md-card-actions>
        <md-button @click="scrapeResults">Scrape Results</md-button>
      </md-card-actions>
      <md-progress-spinner v-show="scraping" :md-diameter="30" :md-stroke="3" md-mode="indeterminate" />

      <md-card-actions v-if="resultsScraped">
        <download-csv :data="jobs">
          <md-button>
            Export To CSV
          </md-button>
        </download-csv>
      </md-card-actions>
    </md-card>

    <md-card class="md-layout-item md-size-90" v-if="resultsScraped">
      <md-card-header>
        <div class="md-title">Results</div>
        <md-table>
          <md-table-head>Company</md-table-head>
          <md-table-head>Job URL</md-table-head>
          <md-table-head>Job Title</md-table-head>
          <md-table-row v-for="(job, index) in jobs" :key="index">
            <md-table-cell> {{ job.company }} </md-table-cell>
            <md-table-cell> {{ job.url }} </md-table-cell>
            <md-table-cell> {{ job.jobtitle }} </md-table-cell>
          </md-table-row>
        </md-table>
      </md-card-header>
    </md-card>
    
    <md-snackbar :md-duration="4000" :md-active.sync="showSnackbar" md-persistent>
      <span v-if="resultsScraped">Results Scraped!</span>
      <span v-else>An error occurred. Please make sure you entered a search term.</span>
    </md-snackbar>
  </form>
</template>

<script>
import Indeed from 'indeed-scraper';

export default {
  name: 'IndeedScraperExport',
  data() {
    return ({
      query: 'QA Automation Engineer',
      location: 'United States',
      jobType: 'fulltime',
      maxAge: 90,
      limit: 25,
      scraping: false,
      jobs: null,
      showSnackbar: false,
      publisherId: '',
    })
  },
  computed: {
    resultsScraped() {
      return (this.jobs !== null && this.jobs.length > 0);
    }
  },
  methods: {
    scrapeResults() {
      const queryOptions = {
        query: this.query,
        location: this.location,
        jobType: this.jobType,
        maxAge: this.maxAge,
        sort: 'date',
        limit: this.limit,
        publisher: this.publisherId,
      };

      this.jobs = null;
      this.scraping = true;

      Indeed.query(queryOptions).then(results => {
        this.scraping = false;
        this.showSnackbar = true;
        this.jobs = results;
      });
    },
  },
}
</script>

<style scoped>
.md-card {
  margin: auto;
  padding-bottom: 20px;
  margin: 20px auto;
}

.md-card-actions {
  display: inline;
}

.md-table-cell {
  text-align: left;
}
</style>
