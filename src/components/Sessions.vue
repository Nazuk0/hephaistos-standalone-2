<template>
  <v-container>
    <v-row>
      <draggable tag="div" v-model="modulesDatas" :animation="200" ghost-class="moving-card">
        <v-col v-for="session in modulesDatas" :key="session.id">
          <v-row>
              <v-col>
                  <v-card class="mx-auto" width="250" height="100" outlined color="#d4af37">
                    <v-row>
                      <v-row>
                        <v-card-title color="grey">
                          {{ session.name }}
                        </v-card-title>
                      </v-row>
                      <v-row>
                        <v-col>
                        {{ getNumberExercises(session.id) }}
                        <v-icon medium color="white darken-2">mdi-gavel</v-icon>
                        <v-icon medium color="white darken-2">mdi-pen</v-icon>
                        </v-col>
                      </v-row>
                    </v-row>
                  </v-card>
              </v-col>
          </v-row>
        </v-col>
      </draggable>
    </v-row>
  </v-container>
</template>

<style lang="scss" scoped>
.moving-card {
    @apply opacity-50;
    @apply bg-gray-100;
    @apply border;
    @apply border-blue-500;
  }
</style>

<script>
import { mapState, mapActions, mapGetters } from 'vuex'
import draggable from 'vuedraggable'
export default {

  components: {
    draggable
  },
  name: 'sessions',
  data: () => ({
    modulesDatas: []
  }),
  props: {
    moduleId: { type: Number }
  },

  computed: {
    sessions: {
      get () {
        return this.$store.state.sessions
      },
      set (value) {
        this.$store.commit('updateList', value)
      }
    },
    ...mapState('sessions', ['sessions']),
    ...mapState('modules', ['modules']),
    ...mapGetters('sessions', ['getSessionsByModuleId']),
    ...mapGetters('exercises', ['getExercisesBySessionId'])
  },
  async mounted () {
    // Lance toutes les requêtes
    await Promise.all(
      this.modules.map(m => this.fetchSessionsForModule({ moduleId: m.id }))
    )
    await Promise.all(
      this.sessions.map(s => this.fetchExercisesForSession({ sessionId: s.id }))
    )
    this.modulesDatas = this.getSessionsByModuleId(this.moduleId)
  },
  methods: {
    ...mapActions('sessions', ['fetchSessionsForModule']),
    ...mapActions('exercises', ['fetchExercisesForSession']),
    getNumberExercises (sessId) {
      for (var number in this.getExercisesBySessionId(sessId)) {
      }
      return parseInt(number) + 1
    }
  }
}
</script>
