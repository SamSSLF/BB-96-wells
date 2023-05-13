<template>
  <div class="grid grid-cols-[250px_1fr] gap-x-4 w-screen items-center">
    <div class="col-span-1 text-black h-screen overflow-auto">
      <ul>
        <li
          v-for="(sample, index) in uniqueSamples"
          :key="index"
          @click="selectSample(sample)"
          class="cursor-pointer hover:bg-sky-300 hover:text-sky-900 py-3 px-8 mx-2 my-2 rounded-lg"
          :class="{
            'bg-yellow-200 hover:bg-yellow-200 hover:text-black':
              sample === selectedSample,
          }"
        >
          {{ sample }}
        </li>
      </ul>
    </div>
    <div class="col-span-1 col-start-2 overflow-auto mx-auto">
      <div class="flex w-full justify-center mb-8">
        <p class="">{{ selectedSample }}</p>
      </div>
      <table
        class="table-fixed text-sm text-gray-500 dark:text-gray-400 text-ellipsis w-5 h-5 overflow-hidden whitespace-nowrap text-center"
      >
        <thead class="text-lg text-gray-900 uppercase dark:text-gray-400 ">
          <tr>
            <th
              class="text-ellipsis w-12 h-5 overflow-hidden whitespace-nowrap"
            ></th>
            <th
              v-for="header in tableHeaders"
              :key="header"
              scope="col"
              class="px-8 py-6 text-ellipsis w-5 h-5 overflow-hidden whitespace-nowrap text-center"
            >
              {{ header }}
            </th>
          </tr>
        </thead>

        <tbody class="text-lg">
          <tr
            class="bg-white dark:bg-gray-800"
            v-for="(row, rowIndex) in tableRows"
            :key="rowIndex"
          >
            <th
              scope="row"
              class="font-medium text-gray-900 whitespace-nowrap dark:text-white text-ellipsis w-24 h-5 overflow-hidden"
            >
              {{ String.fromCharCode("A".charCodeAt(0) + rowIndex) }}
            </th>
            <td
              v-for="(cell, cellIndex) in row"
              :key="cellIndex"
              class="px-4 py-4 text-center hover:bg-sky-300 hover:text-sky-900 text-ellipsis w-5 h-5 overflow-hidden whitespace-nowrap border-solid border-[1px] border-gray-300 text-white cursor-default"
              :class="{
                'bg-yellow-200 font-bold text-yellow-900 hover:bg-yellow-200 hover:text-yellow-900': cell === selectedSample,
              }"
              @click="selectSample(cell)"
            >
              <p>{{ cellIndex + 1 }}{{ String.fromCharCode("A".charCodeAt(0) + rowIndex)}}</p>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import Papa from "papaparse";
// import { initFlowbite } from "flowbite";

const tableRows = ref([]);
const tableHeaders = ref([]);
const selectedSample = ref("sample-12");
const uniqueSamples = ref([]);

function selectSample(sample) {
  selectedSample.value = sample;
}

// initialize components based on data attribute selectors
onMounted(() => {
  // initFlowbite(); // make flowbite components interactive
  console.log(`the component is now mounted.`);
  Papa.parse("./src/assets/nn_plate_layout.csv", {
    download: true,
    header: true,
    complete: (results) => {
      // console.log(results.data);
      const dataMap = new Map();
      const samplesSet = new Set(); // Set to hold unique sample values

      // Create a map of the data with the coordinate as the key.
      for (const row of results.data) {
        dataMap.set(`${row.x},${row.y}`, row.sample_id);
        samplesSet.add(row.sample_id);
      }

      uniqueSamples.value = Array.from(samplesSet).sort();

      // Determine the size of the 2D space.
      const maxX = Math.max(...results.data.map((row) => row.x));
      const maxY = Math.max(...results.data.map((row) => row.y));

      // Generate the table headers.
      for (let x = 1; x <= maxX; x++) {
        tableHeaders.value.push(x);
      }

      // Create the table rows.
      for (let y = 1; y <= maxY; y++) {
        const tableRow = [];
        for (let x = 1; x <= maxX; x++) {
          tableRow.push(dataMap.get(`${x},${y}`) || "");
          console.log(dataMap.get(`${x},${y}`));
        }
        tableRows.value.push(tableRow);
      }
    },
  });
  console.log(`next line`);
});
</script>
