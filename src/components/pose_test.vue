<script setup>
import { ref, onMounted, reactive, watch } from "vue";
import { proto } from "./proto_demo.mjs";

const faces = [
  "face_up",
  "face_down",
  "right",
  "left",
  "vertical_up",
  "vertical_down",
];
// Function to calculate instrument settings based on the poses
let instrument_settings = (i) => {
  let n = 1;
  let calc = (x) => (n > 0 ? x - 1 : x);

  let [f, s, v] = [
    calc(i.poses.faces),
    calc(i.poses.sides),
    calc(i.poses.vertical),
  ];

  let face_option =
    f > 2 ? [faces[1]] : f > 1 ? [faces[0]] : f > 0 ? [faces[0], faces[1]] : [];
  let sides =
    s > 2 ? [faces[2]] : s > 1 ? [faces[3]] : s > 0 ? [faces[3], faces[2]] : [];
  let vertical =
    v > 2 ? [faces[5]] : v > 1 ? [faces[4]] : v > 0 ? [faces[4], faces[5]] : [];

  return [...face_option, ...sides, ...vertical];
};

const cubes = ref([]);
const hl = ref([]);
const selectedFace = ref(faces[0]);

// Function to change the selected face for a cube
const chng_face = (i) => {
  let c = cubes.value.filter((ii) => ii[0] != i);
  let h = hl.value.filter((ii) => ii[0] != i);
  c.push([i, `show-${selectedFace.value}`]);
  h.push([i, selectedFace.value]);
  cubes.value = c;
  hl.value = h;
};

// Function to get the current face of a cube
const getCube = (i) => {
  let c = cubes.value.filter((ii) => ii[0] == i);
  return c.length > 0 ? c[0][1] : null;
};

// Function to check if a face of a cube is highlighted
const getHL = (i, face) => {
  let h = hl.value.filter((ii) => ii[0] == i);
  return h.length > 0 && h[0][1] == face ? true : false;
};

// Function to get the selected face of a cube
const getFace = (i) => {
  let h = hl.value.filter((ii) => ii[0] == i);
  return h.length > 0 ? h[0][1] : null;
};

// Reactive variable for filtered entries
const filteredEntries = ref(proto);

// Reactive object for selected filters
const selectedFilters = reactive({
  face_up: true,
  face_down: true,
  right: true,
  left: true,
  vertical_up: true,
  vertical_down: true,
});

// Function to filter the entries based on the selected pose filters
const filterEntries = () => {
  const filters = Object.keys(selectedFilters).filter(
    (key) => selectedFilters[key]
  );
  filteredEntries.value = proto.filter((entry) => {
    const settings = instrument_settings(entry.settings);
    return filters.some((filter) => settings.includes(filter));
  });
};

watch(selectedFilters, filterEntries, { deep: true });
onMounted(filterEntries);
</script>
<template>
  <div>
    <div class="filter-checkboxes">
      <label v-for="pose in faces" :key="pose" class="checkbox-container">
        <input
          type="checkbox"
          :value="pose"
          v-model="selectedFilters[pose]"
          @change="filterEntries"
        />
        {{ pose }}
      </label>
    </div>

    <table class="list">
      <th>face</th>
      <th>options</th>
      <th>result</th>

      <template v-for="(item, idx) of filteredEntries">
        <tr class="title space">
          <td colspan="3">{{ "|   " }}</td>
        </tr>

        <tr class="title">
          <td colspan="3">{{ item.scalpel_id }}</td>
        </tr>

        <tr class="v_up">
          <td>{{ idx }}</td>
          <td class="code">poses: {{ item.settings.poses }}</td>
          <td>
            <div v-for="res of instrument_settings(item.settings)">
              {{ res }}
            </div>
          </td>
        </tr>

        <tr class="scene_container">
          <td>
            <div class="scene">
              <div :class="`cube ${getCube(idx)}`">
                <div
                  v-for="pose in faces"
                  :class="`cube__face cube__face--${pose}${
                    getHL(idx, pose) ? ' highlight' : ''
                  }`"
                >
                  {{ pose.split("_").join(" ") }}
                </div>
              </div>
            </div>
          </td>

          <td>
            <div style="display: flex; flex-direction: row">
              <div class="scene2">
                <div :class="`cube2 ${getCube(idx)}`">
                  <div
                    v-for="pose in faces"
                    :class="`cube2__face cube2__face--${pose}`"
                  >
                    {{
                      pose == getFace(idx)
                        ? String(pose).split("_").join(" ")
                        : null
                    }}
                  </div>
                </div>
              </div>
            </div>

            <div style="display: flex; flex-direction: row">
              <select v-model="selectedFace" @change="chng_face(idx)">
                <option
                  v-for="face in instrument_settings(item.settings)"
                  :value="face"
                >
                  {{ face }}
                </option>
              </select>
            </div>
          </td>
        </tr>
      </template>
    </table>
  </div>
</template>
<style scoped lang="sass">

body
  font-size: 12px
  background-color: #242424

button
  margin: 2px

.list
  border-spacing: 0px
  .title
    border-spacing: 0
    font-size: large
  .v_up
    vertical-align: top
    .code
      color: #bebebe
      font-size: small

.space
  background-color: #242424
  color: #242424

.sect
  display: flex
  flex-direction: column
  max-width: 30vw
  min-width: 29vw
  margin: 20px

.sub
  color: #edff79

table
  padding: 0 10px
  text-transform: lowercase
  font-family: monospace

tr
  background-color: #183666

td
  padding: 10px 0 0 10px

.col
  display: flex
  flex-direction: column
  margin-right: 20px

.active
  background-color: #565ed8

.cube2__face:hover
  background-color: #c4c4ff

.scene_container
  height: 180px

.blue-container:hover
  background-color: #b0d4ff
</style>
