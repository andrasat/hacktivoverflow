<template>
  <div class="main">
    <h1 class="title main-title">{{ msg }}</h1>
    <div class="columns">
      <div class="column is-three-quarters">
        <article v-if="datas.length > 0" v-for="data in datas" class="box">
          <div class="content is-large">
            <h2 class="subtitle"><router-link :to="{name: 'Question', params: {id: data._id}}">{{ data.title }}</router-link></h2>
            <p>By {{ data.user.username }}</p>
          </div>
          <div v-if="logg" class="field is-grouped">
            <p class="control"><a class="button" @click="vote(1, data._id)"><span class="icon is-small"><i class="fa fa-caret-up"></i></span></a></p>
            <p class="control">{{ data.votes.filter((val)=> {return val.count == 1}).length - data.votes.filter((val)=> {return val.count == -1}).length }}</p>
            <p class="control"><a class="button" @click="vote(-1, data._id)"><span class="icon is-small"><i class="fa fa-caret-down"></i></span></a></p>
          </div>
        </article>
      </div>
      <div class="column is-one-quarters">
        <aside class="menu">
          <router-link v-if="this.$store.state.loggedUser" to="/ask" class="ask button is-primary">Ask a Question</router-link>
        </aside>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'main',
  data () {
    return {
      msg: 'Welcome to Hacktivoverflow',
      datas: [],
      logg: this.$store.state.loggedUser
    }
  },
  methods: {
    getData() {
      let self = this
      axios.get('http://localhost:3000/api/post')
        .then((res)=> {
          self.datas = res.data
        })
        .catch((err)=> {
          console.log(err)
          alert('Server error')
        })
    },
    vote(count, id) {
      let self = this
      axios.post('http://localhost:3000/api/vote/question/'+id, {
        count: count
      }, {headers: {'token': self.$store.state.userToken}}).then((res)=> {
        alert('Vote done!')
      }).catch((err)=> {
        alert('Vote failed')
      })
    }
  },
  mounted() {
    this.getData()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.main-title {
  text-align: center;
}
.columns {
  margin:auto;
}
.ask {
  margin: 1em;
}
</style>
