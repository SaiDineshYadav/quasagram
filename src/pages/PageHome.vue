<template>
  <q-page class="constrain q-pa-md">
    <div class="row q-col-gutter-lg">
      <div class="col-8">
        <q-card
          class="card-post"
          v-for="post of posts"
          :key="post.id"
          flat
          bordered>
          <q-item>
            <q-item-section avatar>
              <q-avatar>
                <img src="https://cdn.quasar.dev/img/boy-avatar.png">
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label class="text-bold" >Title</q-item-label>
              <q-item-label caption>
                Subhead
              </q-item-label>
            </q-item-section>
          </q-item>

          <q-separator />
          <q-img
            :src="post.imageUrl"
          />

        <q-card-section>
            <div class="text-h6">{{post.caption}}</div>
            <div class="text-caption text-grey">
              {{ post.date | getDateFormat}}
            </div>
          </q-card-section>

        </q-card>
      </div>
      <div class="col-4">
        <q-item class="fixed">
            <q-item-section avatar>
              <q-avatar size="48px">
                <img src="https://cdn.quasar.dev/img/boy-avatar.png">
              </q-avatar>
            </q-item-section>

            <q-item-section>
              <q-item-label class="text-bold" >Title</q-item-label>
              <q-item-label caption>
                Subhead
              </q-item-label>
            </q-item-section>
          </q-item>
      </div>

    </div>

  
  </q-page>
</template>

<script>
import { date } from 'quasar'

export default {
  name: 'PageHome',
  data() {
    return {
      posts: [
        {
          id: 1,
          caption: 'Caption 1',
          date: 1613545542586,
          location: 'Vizag',
          imageUrl: 'https://cdn.quasar.dev/img/parallax2.jpg'
        },
         {
          id: 2,
          caption: 'Caption 1',
          date: 1613545542586,
          location: 'Vizag',
          imageUrl: 'https://cdn.quasar.dev/img/parallax2.jpg'
        },
         {
          id: 3,
          caption: 'Caption 1',
          date: 1613545542586,
          location: 'Vizag',
          imageUrl: 'https://cdn.quasar.dev/img/parallax2.jpg'
        },
         {
          id: 4,
          caption: 'Caption 1',
          date: 1613545542586,
          location: 'Vizag',
          imageUrl: 'https://cdn.quasar.dev/img/parallax2.jpg'
        },
      ]      
    }    
  },
  filters: {
        getDateFormat(value) {
          return date.formatDate(value, 'MMMM D h:mm A')
        }
      }
}
</script>

<style lang="sass">
  .card-post
    .q-img
      min-height: 200px
</style>
