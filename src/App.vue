<script setup>
import Poses from './components/pose_test.vue'
</script>

<template>

  <div style="margin: 10px;">
    Tech test - Tasks:
    <div>
      <div class="task">
        1. display all values given in the 'properties' object for each entry, use your judgment on how to display them
      </div>
      <div class="task">
        2. comment on each function in 'pose_test.vue' and explain what it does
        <br />(extra credit for commenting on the other functions in the file)
      </div>
      <div class="task">
        3. contain the contents of each entry within the blue container
      </div>
      <div class="task">
        4. convert the pose buttons to a dropdown menu using the vue composition api bindings to set the face of the cube (space-out contents of each entry as you see fit)
      </div>
      <div class="task">
        5. create filter checkboxes for each of the poses (face up, face down, right, left, vertical up, vertical down) and filter the list of entries based on the selected options
      </div>
      <div class="task">
        6. change the background color of each blue container on hover by using the vue action and binding the hover sass class
        <br />(you can change all the colours as you please)
      </div>
    </div>
    <div>
      Not all tasks need to be completed, but the more you do the better.
    </div>
  </div>

  <Poses />

</template>

<style scoped lang="sass">
.task
  margin: 10px
  font-size: 12px
  font-weight: bold
</style>
