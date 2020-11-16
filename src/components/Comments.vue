<template>
  <div class="home">
    <div class="header_search">
      <b-form-input
        class="header_searchInput"
        @input="search_text()"
        v-model="search.text"
        type="text"
        placeholder="Search by Name"
      ></b-form-input>
      <div class="sort_btn">
        <b-form-select
          @input="sort()"
          v-model="search.filter"
          :options="options"
        />
      </div>
    </div>

    <b-container class="home_row">
      <b-card-group deck v-for="comment in comments" :key="comment.id">
        <b-card
          :title="comment.name"
          img-src="https://via.placeholder.com/150/92c952"
          img-height="150"
          img-width="50"
          img-alt="Image"
          img-top
          tag="article"
          style="max-width: 20rem;"
          class="mb-5 mr-5 product"
        >
          <b-card-text>
            {{ comment.body }}
          </b-card-text>
        </b-card>
      </b-card-group>
    </b-container>
  </div>
</template>

<script>
import gql from "graphql-tag";
export default {
  name: "Comments",
  props: {},
  data() {
    return {
      comments: [],
      comments_data: [],
      result: [],
      search: { filter: null, text: "" },
      options: [
        { value: null, text: "Sort By" },
        { value: "a", text: "Ascending" },
        { value: "d", text: "descending" },
      ],
    };
  },

  apollo: {
    comments: {
      query: gql`
        query($options: PageQueryOptions) {
          comments(options: $options) {
            data {
              id
              name
              body
            }
          }
        }
      `,
      variables: {
        options: {
          paginate: {
            page: 1,
            limit: 50,
          },
        },
      },
      manual: true,
      result({ data }) {
        this.comments = { ...data.comments.data };
        this.comments_data = { ...data.comments };
      },
    },
  },

  methods: {
    search_text() {
      var inside = this;
      this.comments = this.comments_data.data.filter(function(product) {
        if (
          product.name
            .toLowerCase()
            .indexOf(inside.search.text.toLowerCase()) != "-1"
        ) {
          return product;
        }
      });
    },
    sort() {
      if (this.search.filter == "a") {
        this.comments = this.comments_data.data.sort(function(a, b) {
          if (a.name < b.name) {
            return -1;
          }
          if (a.name > b.name) {
            return 1;
          }
          return 0;
        });
      } else if (this.search.filter == "d") {
        this.comments = this.comments_data.data.sort(function(a, b) {
          if (a.name < b.name) {
            return 1;
          }
          if (a.name > b.name) {
            return -1;
          }
          return 0;
        });
      }
    },
  },
};
</script>