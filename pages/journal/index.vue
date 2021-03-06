<template lang="pug">
  .journal
    .mobile-menu-wrapper
      mobile-menu(title="Journal")
    .journal-title
      | Journal

    .journal-content
      .journal-column
        nuxt-link.journal-item(
          v-for="(post, i) of postsOdd"
          v-bind:to="'/journal/' + post.id",
          :key="i"

          )
          img.journal-image(
            v-bind:src="post.image"
            v-on:load="onImgLoaded"
            v-on:error="onImgLoaded"
            )
          .journal-date
            | {{post.date}}
          .journal-name
            | {{post.title}}
      .journal-column
        nuxt-link.journal-item(
          v-for="(post, i) of postsEven",
          :key="i"
          v-bind:to="'/journal/' + post.id"
          )
          img.journal-image(
            v-bind:src="post.image"
            v-on:load="onImgLoaded"
            v-on:error="onImgLoaded"
            )
          .journal-date
            | {{post.date}}
          .journal-name
            | {{post.title}}
</template>

<script>
  import { mapMutations, mapGetters } from 'vuex';
  import { db } from '~/db'
  import MobileMenu from '~/components/MobileMenu'


  const $posts = db.ref('posts')
  const $categories = db.ref('categories')

  export default {
    name: "JournalComponent",
    layout: 'home',

    fetch ({ store }) {
      return store.dispatch('setPostsRef', $posts).then(() => store.dispatch('setCategoriesRef', $categories))
    },

    components: {
      MobileMenu
    },

    data () {
      return {
        postsOdd: [],
        postsEven: [],
        imgLoaded: 0
      }
    },

    mounted () {
      this.makeMenuUnfixed();
      this.menuClose();
      this.hideContacts();
      let postsRev = this.posts.slice();
      postsRev.reverse();
      for (let i = 0; i < postsRev.length; i++) {
        let post = postsRev[i];
        if (i % 2 == 0)
          this.postsOdd.push(post);
        else
          this.postsEven.push(post);
      }
    },

    methods: {
      onImgLoaded () {
        this.imgLoaded++;
        if (this.imgLoaded == this.posts.length)
          this.onLoad(100);
        else
          this.onLoad(this.imgLoaded * 100 / this.posts.length);
      },

      ...mapMutations([
        'onLoad',
        'makeMenuUnfixed',
        'menuClose',
        'hideContacts'
      ])
    },

    computed: {
      ...mapGetters(['posts'])
    }
  }
</script>

<style lang="scss" scoped rel="stylesheet/scss">
  .journal {
    width: 100%;
    height: 100%;
    background: white;

    overflow-y: scroll;

    padding: 0 10% 11vw;

    .mobile-menu-wrapper {
      width: 100%;
      padding: 30px;
      background: #fff;
      z-index: 20;
      position: fixed;
      top: 0;
      left: 0;
      display: none;
      border-bottom: 1px solid #f5f5f5;
    }

    &-title {
      margin-top: 6vw;
      margin-bottom: 6vw;

      font-size: 2.2vw;
      color: rgba(0,0,0,0.87);
      letter-spacing: 2px;
      line-height: 3vw;

      text-align: center;
    }

    &-content {
      display: flex;
      flex-flow: row wrap;
      justify-content: space-between;
    }

    &-column {
      display: flex;
      flex-flow: column nowrap;
      flex: 0 0 43%;
    }

    // &-item {
    //   flex: 0 0 43%;
    // }

    &-image {
      width: 100%;
    }

    &-date {
      margin-top: 3.2vw;
      padding: 0 3.4vw;

      font-family: 'Marvel', sans-serif;
      font-weight: bold;
      font-size: 1.2vw;
      color: rgba(0,0,0,0.87);
      letter-spacing: 1.55px;
      line-height: 1.4vw;
    }

    &-name {
      margin-top: 0.8vw;
      margin-bottom: 10vw;
      padding: 0 3.4vw;

      font-size: 2vw;
      color: rgba(0,0,0,0.87);
      letter-spacing: 1px;
      line-height: 2.6vw;
    }
  }

  @media (max-width: 767px) {
    .journal {
      padding: 79px 30px 30px 30px;

      .mobile-menu-wrapper {
        display: block;
      }

      .journal-title {
        display: none;
      }

      .journal-content {
        margin-top: 60px;
      }

      .journal-column {
        flex-basis: 100%;
      }

      .journal-date {
        font-size: 18px;
        line-height: 22px;
      }

      .journal-name {
        font-size: 20px;
        line-height: 24px;
      }
    }
  }
</style>
