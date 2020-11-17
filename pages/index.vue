<template>
  <div class="">

    <header class="navbar is-primary">
        <div class="navbar-brand">
            <span class="navbar-item">
                <span class="fa fa-language"/><span class="is-size-3">うえむらポートフォリオ</span>
            </span>
        </div>
        <div class="navbar-end">
          <div class="navbar-item">
              <div class="field has-addons">
                <div class="control">
                    <input class="input" type="text" name="search" placeholder="キーワード検索"/>
                </div>
                <div class="control">
                    <button class="button s-primary is-inverted is-outlined"><i class="fa fa-search"/>検索</button>
                </div>
              </div>
          </div>
        </div>
    </header>

    <div class="main">
      <!-- <Logo /> -->
    <div>
      <section class="section">
        <form>
          <br/>
          <p class="label">投稿してください</p>
          <input 
            id="title"
            name="title" 
            v-model="params.title"
            class="input mt-4 mb-4" 
            type="text" 
            placeholder="タイトルを入力してください。"/>
          <textarea
            id="content"
            name="content" 
            v-model="params.content"
            class="textarea" 
            placeholder="内容を入力してください。" 
            cols="40" 
            rows="4"/>
          <br/>
        </form>
        <button class="button is-primary is-rounded" @click="tapOnPostAction()" >投稿</button>
      </section>

      <section class="section">
        <div class="table-container">
          <table class="table is-responsive">
            <thead>
              <tr>
                <th>タイトル</th>
                <th>投稿内容</th>
                <th>投稿時間</th>
                <th>削除</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="blog in blogs">
                <th>{{ blog.title }}</th>
                <th>{{ blog.content }}</th>
                <th>{{ blog.createdAt }}</th>
                <button class="button is-danger" @click="tapOnDeleteAction(blog.id)">削除</button>
              </tr>
            </tbody>
          </table>
        </div>
      </section>
    </div>

    </div>
  </div>
</template>

<script>
export default {
   components: {},
  data() {
    return {
      isConfirm: false,
      endpoint:
        'https://uemr.microcms.io/api/v1/blogs',
      xApiKey: 'c8e6074e-af06-4c30-8523-cf67005a55ad',
      xWriteApiKey: '17a2519f-3938-4803-aebf-e29ceb4097e7',
      blogs: [],
      searchKeyword: '', 
      params: {
        title: '',
        content: ''
      },
    }
  },
  mounted() {
    this.$nextTick(function () {
      // ビュー全体がレンダリングされた後にのみ実行されるコード
      this.doReadBlogs()
    })
  },
  watch: {
  },
  methods: {
    async doReadBlogs() {
      const self = this
      const headers = {
        'X-API-KEY': this.xApiKey,
        "Content-Type": "application/json"
      }
      await this.$axios.get(this.endpoint, {
          headers: headers
      })
      .then(function(response) {
        self.blogs = response.data.contents
      })
      .catch(function(error) {
        console.log('ERROR!! occurred in Backend.')
        console.log(error)
      })
    },
    async doWriteBlogs() {
      const headers = {
        'Content-Type': 'application/json',
        'X-WRITE-API-KEY': this.xWriteApiKey
      }
      return await this.$axios.post(this.endpoint, JSON.stringify(this.params), { headers: headers });
    },
    async doDestoryBlogs(blogId) {
      const headers = {
        'Content-Type': 'application/json',
        'X-WRITE-API-KEY': this.xWriteApiKey
      }
      return await this.$axios.delete(`${this.endpoint}/${blogId}`, {headers: headers});
    },
    async tapOnPostAction() {
      try {
          await this.doWriteBlogs()
          location.reload(true);
        } catch (e) {
          console.log(e);
        }
    },
    async tapOnDeleteAction(blogId) {
      console.log(blogId);
      try {
          await this.doDestoryBlogs(blogId)
          location.reload(true);
        } catch (e) {
          console.log(e)
        }
    }
  }
}
</script>

<style>
.main {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  text-align: center;
}

.links {
  padding-top: 15px;
}
</style>
