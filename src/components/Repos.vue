<template id="#repos-template">
    <ul>
      <li v-for="pr in pullrequests" :key="pr.id">
      <PR 
        :id="pr.id"
        :title="pr.title"
        :participants="pr.participants"
        :authoravatar="pr.author.links.avatar.href"
        :authorname="pr.author.display_name"
        :source="pr.source.repository.full_name + '/' + pr.source.branch.name"
        :destination="pr.destination.repository.full_name + '/' + pr.destination.branch.name"
        :state="pr.state" />
      </li>
    </ul>
</template>

<script>
let base64 = require('base-64')

async function fetchRepos (headers) {
  let values = []
  let url = 'https://api.bitbucket.org/2.0/repositories/pathmotion'
  do {
    const response = await fetch(url,
      {
        method: 'GET',
        headers
      })

    const json = await response.json()
    url = json.next
    values = [...values, ...json.values]
  } while (url)

  return await Promise.all(values.map(async (repo) => ({
    ...repo,
    pullrequests: await fetchRepoPRs(repo, headers)
  })))
}

async function fetchRepoPRs (repo, headers) {
  let values = []
  let url = repo.links.pullrequests.href
  do {
    const response = await fetch(url,
      {
        method: 'GET',
        headers
      })

    const json = await response.json()
    url = json.next
    values = [...values, ...json.values]
  } while (url)

  return values.map((pullrequest) => ({
    ...pullrequest,
    repository: repo
  }))
}

async function fetchPRs (username, appPassword) {
  const headers = new Headers()
  headers.set('Authorization', 'Basic ' + base64.encode(username + ':' + appPassword))

  const repositories = await fetchRepos(headers)

  const prs = repositories.reduce((prev, repo) => [...prev, ...repo.pullrequests])

  return prs.sort(function (pra, prb) {
    return new Date(pra.date) - new Date(prb.date)
  })
}

import PR from './PR'
export default {
  template: '#repos-template',
  name: 'repos',
  data: () => ({
    pullrequests: []
  }),
  created: async function () {
    this.pullrequests = await fetchPRs(process.env.BITBUCKET_USER, process.env.BITBUCKET_APP_PASSWORD)

    setTimeout(async () => {
      this.pullrequests = await fetchPRs(process.env.BITBUCKET_USER, process.env.BITBUCKET_APP_PASSWORD)
    }, process.env.REFRESH_TIMEOUT || 1000 * 60)
  },
  components: {
    PR
  }
}
</script>

<style>
li {
  margin: 10px;
}
</style>
