<template>

  <div v-if="dataLoaded" class="container mt-10 px-4">

    <!-- No Data -->

    <div v-if="data.length === 0" class="w-full flex flex-col items-center">

      <h1 class="text-2xl">Looks empty here...</h1>

      <router-link
        :to="{ name: 'Create' }"
        class="mt-6 py-2 px-6 rounded-sm self-start text-sm border-solid border-2 border-white duration-200 text-white bg-secondary hover:border-primary hover:bg-primary hover:text-white"
      >
         Create Workout
      </router-link>

    </div>

    <!-- Data -->

    <div
      v-else
      class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6"
    >

      <router-link
        class="flex flex-col items-center bg-primary-variant p-8 shadow-md cursor-pointer"
        :to="{ name: 'ViewWorkout', params: { workoutId: workout.id } }"
        v-for="(workout, index) in data"
        :key="index"
      >

        <!-- Cardio Image -->

        <img
          v-if="workout.workoutType === 'cardio'"
          src="@/assets/images/running-light-green.png"
          class="h-24 w-auto"
          alt=""
        />

        <!-- Strength Image -->

        <img
          v-if="workout.workoutType === 'strength'"
          src="@/assets/images/dumbbell-light-green.png"
          class="h-24 w-auto"
          alt=""
        />

        <p
          class="mt-6 py-1 px-3 text-xs text-white bg-secondary shadow-md rounded-lg"
        >
           {{ workout.workoutType }}
        </p>

        <h1 class="mt-8 mb-2 text-center text-xl text-white">
           {{ workout.workoutName }}
        </h1>

      </router-link>

    </div>

  </div>

</template>

<script>
import { ref } from "vue";
import { supabase } from "../supabase/init";

export default {
  name: "Home",
  components: {},
  setup() {
    // Create data / vars
    const data = ref([]);
    const dataLoaded = ref(null);
    // Get data
    const getData = async () => {
      try {
        const { data: workout, error } = await supabase
          .from("workout")
          .select("*");
        if (error) throw error;
        data.value = workout;
        dataLoaded.value = true;
        // console.log(data.value);
      } catch (error) {
        console.warn(error.message);
      }
    };
    // Run data function
    getData();
    return { data, dataLoaded, getData };
  },
};
</script>

