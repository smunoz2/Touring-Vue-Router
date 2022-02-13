<template>
  <h1>Events for Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'EventList', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
        >&#60; Previous
      </router-link>
      <div v-else class="empty-box" />

      <router-link
        v-for="pages in totalEvents / 2"
        :key="pages"
        id="page-number"
        :to="{ name: 'EventList', query: { page: pages } }"
        :rel="pages"
        >{{ pages }}
      </router-link>

      <router-link
        id="page-next"
        :to="{ name: 'EventList', query: { page: page + 1 } }"
        rel="next"
        v-if="this.hasNextPage"
        >Next &#62;
      </router-link>
      <div v-else class="empty-box" />
    </div>
  </div>
</template>

<script>
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { watchEffect } from 'vue'

export default {
  name: 'EventList',
  props: ['page'],
  components: {
    EventCard
  },
  data() {
    return {
      events: null,
      totalEvents: 0
    }
  },
  created() {
    watchEffect(() => {
      EventService.getEvents(2, this.page)
        .then(response => {
          this.events = response.data
          this.totalEvents = response.headers['x-total-count']
        })
        .catch(error => {
          console.log(error)
        })
    })
  },
  computed: {
    hasNextPage() {
      let totalPages = Math.ceil(this.totalEvents / 2)
      return totalPages > this.page
    }
  }
}
</script>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pagination {
  display: flex;
  width: 290px;
}

.pagination a {
  text-decoration: none;
  color: #2c3e50;
  justify-content: center;
}

#page-prev {
  flex: 1 1 auto;
  text-align: left;
}

#page-next {
  flex: 1 1 auto;
  text-align: right;
}

#page-number {
  flex: 1 1 auto;
  text-align: center;
  align-content: center;
}
.empty-box {
  flex: 3 1 auto;
  align-content: center;
}
</style>
