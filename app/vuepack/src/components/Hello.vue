<template>
  <div class="hello">
    <p>{{ msg }}</p>
    <ul>
      <li v-for="consultant in consultants">
        {{ consultant['DC Name'] }} - {{ consultant['Store'] }}
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      userName: '',
      consultants: [],
      ROWS: ['Store', 'Lead Allocation', 'DC Name', 'Region', 'Capacity', 'Store No', 'Store PC', 'Store Tele', 'DC Email']
    }
  },
  computed: {
    msg: function () {
      return 'Hello ' + this.userName + ', welcome to Zendesk Vue.js App.'
    }
  },
  methods: {
    getGoogleSheet: function () {
      var sheetId = '1xBy31E2YCfQpH_qsB-Mwy3oqfT_PvAdD3SN3eb_ddKk'
      var key = 'AIzaSyBQSvfLyFNJCWfIPOUBnZxi-wl-Hn3le5s'
      var range = 'A1:ZZ10000'
      var url = 'https://sheets.googleapis.com/v4/spreadsheets/' + sheetId + '/values/' + range + '?key=' + key
      var app = this

      client.request({
        url: url
      }).then(function (data) {
        // Remove header rows
        data['values'] = data['values'].slice(5)

        // Convert array of rows to an object
        app.consultants = data['values'].map(function (row) {
          var rowObj = {}
          app.ROWS.forEach(function (header, i) {
            rowObj[header] = row[i]
          })
          return rowObj
        })

        console.log(app.consultants)
      },
      function (response) {
        console.error(response)
      })
    }
  },
  mounted: function () {
    client.get('currentUser').then(function (data) {
      this.userName = data['currentUser']['name']
    }.bind(this))
    this.getGoogleSheet()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
p {
  color: #bd322c;
}
ul {
  list-style: none;
  padding-left: 0;
}
</style>
