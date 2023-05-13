<template>
    <!-- <div class="relative overflow-x-auto"> -->
      
    <!-- </div> -->
  
  
    
  </template>
  
  <script setup>
  import { ref, onMounted } from "vue";
  import Papa from "papaparse";
  // import { initFlowbite } from "flowbite";
  
  const tableRows = ref([]);
  const tableHeaders = ref([]);

  const selectedSample = ref("sample-12")
  
  function selectSample(sample) {
    selectedSample.value = sample
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
  
        // Create a map of the data with the coordinate as the key.
        for (const row of results.data) {
          dataMap.set(`${row.x},${row.y}`, row.sample_id);
        }
  
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
  