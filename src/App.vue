<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <table style="width:100%">
  <tr>
    <th>Number</th>
    <th>Title</th>
    <th>Description</th>
    <th>Client</th>
    <th>Priority</th>
    <th>Type</th>
    <th>Assigned To</th>
    <th>Status</th>
  </tr>
  <tr v-for="(issue, index) in issues" :key="index">
    <td>{{index}}</td>
    <td>{{issue.title}}</td>
    <td>{{issue.body}}</td>
    <td v-for="label, i in issue.labels" :key="i">{{label.name}}</td>
    <!-- <td>{{issue.labels[0].length == 0 ? 'undefined' : 'resolved'}}</td>
    <td>{{issue.labels[1].name}}</td>
    <td>{{issue.labels[2].name}}</td> -->
    <td>{{assignees[Math.floor(Math.random() * assignees.length)]}}</td>
    <td>{{issue.state}}</td>
  </tr>
</table>
  <!-- I would like to use a component for the add part -->
  <HelloWorld msg="Welcome to Your Vue.js App"/>
</template>

<script>
import axios from 'axios'
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  data() {
    return {
      issues: [],
      issuesList: [],
      openIssues: null,
      labels: [],
      assignees: ['Jared', 'Jaco', 'Nichola', 'Tjaart'],
    }
  },
  created() {
    axios.request({
      url: 'https://api.github.com/repos/1-grid/GitIntegration',
      method: 'get',
      clientId: 'bf56b82110ed92bfc649',
      clientSecret: '7c10b630520202c13ee98a8551b19b3fb34adb54',
      accessToken: 'bf56b82110ed92bfc649',
    }).then(response => {
      this.openIssues = response.data.open_issues
      for(let x = 1; x <= this.openIssues; x++){
        this.issuesList.push('https://api.github.com/repos/1-grid/GitIntegration/issues/' + x)
      }

      //Prevents axios get request from skipping an issue
      const data = async () => {
        for(const issue of this.issuesList){
          await axios.get(issue).then(result => {
            this.issues.push(result.data)
            this.labels.push(result.data.labels)
          })
        }
      }
      return data()
    }).catch(new Error())//include error message
  },
  components: {
    HelloWorld
  },
  mounted(){
  },
  computed: {
  },
  methods: {
    getLabels(){
      

      console.log(this.issues, 'labels')
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
