<template>

  <div class="max-w-screen-md mx-auto px-4 py-10">

    <!-- Status Message -->

    <div v-if="statusMsg || errorMsg" class="mb-10 p-4 bg-secondary rounded-md">

      <p class="text-white">{{ statusMsg }}</p>

      <p class="text-red">{{ errorMsg }}</p>

    </div>

    <!-- Create -->

    <div class="p-8 flex items-start bg-secondary rounded-md shadow-lg">

      <!-- Form -->

      <form
        @submit.prevent="createWorkout"
        class="flex flex-col gap-y-5 w-full"
      >

        <h1 class="text-2xl text-white">Record Workout</h1>

        <!-- Workout Name -->

        <div class="flex flex-col">

          <label for="workout-name" class="mb-1 text-sm text-white">
             Workout Name
          </label>

          <input
            type="text"
            required
            class="p-2 text-gray-500 focus:outline-none"
            id="workout-name"
            v-model="workoutName"
          />

        </div>

        <!-- Workout Type -->

        <div class="flex flex-col">

          <label for="workout-type" class="mb-1 text-sm text-white">
             Workout Type
          </label>

          <select
            @change="workoutChange"
            id="workout-type"
            required
            class="p-2 text-gray-500 focus:outline-none"
            v-model="workoutType"
          >

            <option value="select-workout">Select Workout</option>

            <option value="strength">Strength Training</option>

            <option value="cardio">Cardio</option>

          </select>

        </div>

        <!-- Strength Training Inputs -->

        <div v-if="workoutType === 'strength'" class="flex flex-col gap-y-4">

          <div
            class="flex flex-col gap-x-6 gap-y-2 relative md:flex-row"
            v-for="(item, index) in exercises"
            :key="index"
          >

            <div class="flex flex-col md:w-1/3">

              <label for="exercise-name" class="mb-1 text-sm text-white">
                 Exercise
              </label>

              <input
                type="text"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.exercises"
              />

            </div>

            <div class="flex flex-col flex-1">

              <label for="sets" class="mb-1 text-sm text-white"> Sets </label>

              <input
                type="text"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.sets"
              />

            </div>

            <div class="flex flex-col flex-1">

              <label for="reps" class="mb-1 text-sm text-white"> Reps </label>

              <input
                type="text"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.reps"
              />

            </div>

            <div class="flex flex-col flex-1">

              <label for="weight" class="mb-1 text-sm text-white">
                 Weight (LB's)
              </label>

              <input
                type="text"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.weight"
              />

            </div>

            <img
              @click="deleteExercise(item.id)"
              src="@/assets/images/trash-light-green.png"
              class="h-4 w-auto absolute -left-5 cursor-pointer"
              alt=""
            />

          </div>

          <button
            @click="addExercise"
            type="button"
            class="mt-6 py-2 px-6 rounded-sm self-start text-sm border-solid border-2 border-white duration-200 text-white bg-secondary hover:border-primary hover:bg-primary hover:text-white"
          >
             Add Exercise
          </button>

        </div>

        <!-- Cardio Training Inputs -->

        <div v-if="workoutType === 'cardio'" class="flex flex-col gap-y-4">

          <div
            class="flex flex-col gap-x-6 gap-y-2 relative md:flex-row"
            v-for="(item, index) in exercises"
            :key="index"
          >

            <div class="flex flex-col md:w-1/3">

              <label for="cardio-type" class="mb-1 text-sm text-white">
                 Type
              </label>

              <select
                id="cardio-type"
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.cardioType"
              >

                <option value="#">Select Type</option>

                <option value="run">Runs</option>

                <option value="walk">Walk</option>

              </select>

            </div>

            <div class="flex flex-col flex-1">

              <label for="distance" class="mb-1 text-sm text-white">
                 Distance
              </label>

              <input
                type="text"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.distance"
              />

            </div>

            <div class="flex flex-col flex-1">

              <label for="duration" class="mb-1 text-sm text-white">
                 Duration
              </label>

              <input
                type="text"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.duration"
              />

            </div>

            <div class="flex flex-col flex-1">

              <label for="pace" class="mb-1 text-sm text-white"> Pace </label>

              <input
                type="text"
                required
                class="p-2 w-full text-gray-500 focus:outline-none"
                v-model="item.pace"
              />

            </div>

            <img
              @click="deleteExercise(item.id)"
              src="@/assets/images/trash-light-green.png"
              class="h-4 w-auto absolute -left-5 cursor-pointer"
              alt=""
            />

          </div>

          <button
            @click="addExercise"
            type="button"
            class="mt-6 py-2 px-6 rounded-sm self-start text-sm border-solid border-2 border-white duration-200 text-white bg-secondary hover:border-primary hover:bg-primary hover:text-white"
          >
             Add Exercise
          </button>

        </div>

        <button
          type="submit"
          class="mt-6 py-2 px-6 rounded-sm self-start text-sm border-solid border-2 border-white duration-200 text-white bg-secondary hover:border-primary hover:bg-primary hover:text-white"
        >
           Record Workout
        </button>

      </form>

    </div>

  </div>

</template>

<script>
import { ref } from "vue";
import { uid } from "uid";
import { supabase } from "../supabase/init";

export default {
  name: "create",
  setup() {
    // Create data
    const workoutName = ref("");
    const workoutType = ref("select-workout");
    const exercises = ref([1]);
    const statusMsg = ref(null);
    const errorMsg = ref(null);

    // Add exercise
    const addExercise = () => {
      if (workoutType.value === "strength") {
        exercises.value.push({
          id: uid,
          exercises: "",
          sets: "",
          reps: "",
          weight: "",
        });
        return;
      }
      exercises.value.push({
        id: uid,
        cardioType: "",
        distance: "",
        duration: "",
        pace: "",
      });
    };
    // Delete exercise
    const deleteExercise = (id) => {
      if (exercises.value.length > 1) {
        exercises.value = exercises.value.filter(
          (exercise) => exercise.id !== id
        );
        return;
      }
      errorMsg.value =
        "Error: Cannot remove, need to at least have one exercise";
      setTimeout(() => {
        errorMsg.value = false;
      }, 5000);
    };
    // Listens for chaging of workout type input
    const workoutChange = () => {
      exercises.value = [];
      addExercise();
    };
    // Create workout
    const createWorkout = async () => {
      try {
        const { error } = await supabase.from("workout").insert([
          {
            workoutName: workoutName.value,
            workoutType: workoutType.value,
            exercises: exercises.value,
          },
        ]);
        if (error) throw error;
        statusMsg.value = "Success: Workout Created!";
        workoutName.value = null;
        workoutType.value = "select-workout";
        exercises.value = [];
        setTimeout(() => {
          statusMsg.value = false;
        }, 5000);
      } catch (error) {
        errorMsg.value = `Error: ${error.message}`;
        setTimeout(() => {
          errorMsg.value = false;
        }, 5000);
      }
    };
    return {
      workoutName,
      workoutType,
      exercises,
      statusMsg,
      errorMsg,
      addExercise,
      workoutChange,
      deleteExercise,
      createWorkout,
    };
  },
};
</script>

