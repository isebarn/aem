<template>
  <v-container>
    <v-row>
      <v-text-field
        id="id"
        v-model="search"
        name="name"
        label="label"
      />
      <v-btn color="success" @click="test">
        text
      </v-btn>
    </v-row>
    <v-row>
      <v-col v-if="loading_amazon" cols="6">
        Amazon
        <v-progress-circular
          indeterminate
          color="primary"
        />
      </v-col>
      <v-col v-if="loading_jomafa" cols="6">
        Jomafa
        <v-progress-circular
          indeterminate
          color="primary"
        />
      </v-col>
    </v-row>
    <v-row>
      <v-data-table
        :headers="headers"
        :items="items"
        :items-per-page="-1"
      >
        <template v-slot:item.src="{ item }">
          <img
            :src="item.src"
            style="width: 100px; height: 100px"
            hide-actions
            @click="openURL(item)"
          >
        </template>
      </v-data-table>
    </v-row>
  </v-container>
</template>

<script>

export default {
  data () {
    return {
      search: 'prensa hidraulica 12 toneladas',
      items: [],
      loading_amazon: false,
      loading_jomafa: false,
      headers: [
        {
          text: 'product',
          value: 'src'
        },
        {
          text: 'name',
          value: 'title'
        },
        {
          text: 'price',
          value: 'price'
        },
        {
          text: 'source',
          value: 'source'
        }
      ]
    }
  },

  methods: {
    test () {
      const self = this
      self.loading_amazon = true
      self.loading_jomafa = true
      self.items = []
      this.$axios.get('jomafa', { params: { search: this.search.replace(' ', '+') } })
        .then(function (response) {
          // handle success
          self.items.push(...response.data)
        })
        .catch(function (error) {
          // handle error
          console.log(error)
        })
        .then(function () {
          self.loading_jomafa = false
        })

      this.$axios.get('amazon', { params: { search: this.search.replace(' ', '+') } })
        .then(function (response) {
          // handle success
          console.log(response.data)
          self.items.push(...response.data)
        })
        .catch(function (error) {
          // handle error
          console.log(error)
        })
        .then(function () {
          self.loading_amazon = false
        })
      /* const jomafa = await this.$axios.get('jomafa', { params: { search: this.search.replace(' ', '+') } })
      this.items = jomafa.data */
      // const amazon = await this.$axios.get('amazon', { params: { search: this.search.replace(' ', '+') } })
      // this.items = amazon.data
    },

    openURL (item) {
      window.open(item.url)
    }
  }
}
</script>
