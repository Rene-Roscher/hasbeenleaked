<template>
  <div>
    <div class="shadow-xl w-full bg-gray-800 xl:rounded-b-8xl md:rounded-b-4xl">
      <div
          class="max-w-screen-xl px-4 py-12 mx-auto sm:px-6 lg:py-16 lg:px-8 lg:flex lg:items-center lg:justify-between"
      >
        <h2
            class="text-3xl font-extrabold leading-9 tracking-tight text-white sm:text-4xl sm:leading-10"
        >
          Ready to see if you are one of those affected?
          <br/>
          <span class="text-indigo-600">- {{ affectedUsersCount }} users are affected</span>
        </h2>
        <div class="flex mt-8 lg:flex-shrink-0 lg:mt-0">
          <ButtonRepo/>
        </div>
      </div>
    </div>

    <div class="flex justify-center transition ease-in-out duration-500">
      <div class="mt-8 w-8/12 p-12 shadow-xl bg-gray-800 rounded-md">
        <div class="md:space-x-4 space-y-4 md:space-y-0 md:divide-x-2 divide-y-2 md:divide-y-0 divide-gray-700" :class="{'grid md:grid-cols-2 grid-cols-1': isFound}">
          <!-- Search -->
          <div>
            <h1 class="font-semibold text-md text-white md:mb-0 mb-6">Search...</h1>
            <h4 class="font-medium text-sm text-gray-400 md:mb-0 mb-6">Enter your username here to see if you are affected.</h4>
            <div class="col-span-1 mx-auto flex justify-center mt-4">
              <label class="block">
                <span class="text-gray-200">Username</span>
                <input v-model="username"  type="text" class="mt-1 bg-gray-700 text-white block w-full rounded-md shadow-sm border-transparent focus:border-transparent text-center focus:ring focus:ring-indigo-500 focus:ring-opacity-50" placeholder="MontanaBlack88">
              </label>
            </div>
          </div>
          <!-- Profile -->
          <div class="col-span-1 mx-auto flex justify-center p-4" v-if="isFound">
            <div class="flex-col space-y-3">
              <div class="relative inline-block overflow-hidden">
                <img draggable="false" class="inline-block object-cover w-24 h-24 rounded-full" :src="user.logo" alt="User's image"/>
                <span class="absolute inset-16 inline-block w-4 h-4 rounded-full">
                  <checklist-item :type="isLeaked ? 'danger' : 'success'"/>
                </span>
              </div>
              <div class="py-1.5 border-b border-gray-700 text-center">
                <a target="_blank" class="text-gray-200 hover:text-gray-50 hover:underline" :href="'https://twitch.tv/' + user.name">{{ user.display_name }}</a>
              </div>
              <div class="text-base leading-7 font-semibold text-gray-200 text-center" :class="{ 'text-red-300': isLeaked, 'text-green-300': !isLeaked }">
                {{ isLeaked ? 'Leaked' : 'Safe' }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import ButtonRepo from '@/components/ButtonRepo.vue'
import ChecklistItem from '@/components/ChecklistItem.vue'
import axios from 'axios';
import _ from 'lodash';

const IDsUri = 'https://gist.githubusercontent.com/Rene-Roscher/a9febbf18d66102bbd2981562f729bc5/raw/b58366a073b918f82aa6159309e8b4ae619befd4/twitch-leaked-db';

export default {
  components: {
    ChecklistItem,
    ButtonRepo
  },
  data() {
    return {
      username: '',
      isFound: false,
      isLeaked: false,
      user: null,
      leaked: null,
    };
  },
  methods: {
    fetch(nickname) {
      axios.get(`https://api.twitch.tv/kraken/users?login=${nickname}`, {
        headers: {
          'Client-ID': 'ekj09tcx24qymrl1wl5c6er2qjkpryz',
          'Accept': 'application/vnd.twitchtv.v5+json'
        },
      }).then(res => res.data).then(data => {
        let found = this.isFound = data['_total'] === 1;
        if (found) {
          this.user = data.users[0];
        }
      })
    },
    storeAffectedUsers() {
      axios.get(IDsUri)
          .then(res => res.data)
          .then(res => JSON.stringify(res))
          .then(res => (this.leaked = JSON.parse(res)));
    }
  },
  mounted() {
    this.storeAffectedUsers();
    setInterval(this.storeAffectedUsers, 120*1000)
  },
  computed: {
    affectedUsersCount() {
      return this.leaked ? this.leaked.length : 0;
    }
  },
  watch: {
    username: {
      handler: _.throttle(function (nickname) {
        this.fetch(nickname.toLowerCase())
      }, 2000),
      deep: true,
    },
    'user._id': function (id) {
      this.isLeaked = this.leaked.filter(i => i == id).length >= 1
    }
  }
}
</script>
