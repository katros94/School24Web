<template>
  <!-- <AbsenceTracker msg="Absence Tracker"/> -->
  <RandomAbsences msg="Random Absences"/>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import AbsenceTracker from './components/AbsenceTracker.vue';
import RandomAbsences from './components/RandomAbsences.vue';

@Options({
  components: {
    AbsenceTracker,
    RandomAbsences
  },
})
export default class App extends Vue {}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
