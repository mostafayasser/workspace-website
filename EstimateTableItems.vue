<script setup lang="ts">
import type { IEstimate } from "~/types/iEstimate";
import { defineProps, } from "vue";

const props = defineProps<{ estimate: IEstimate }>();

const estimate = ref<IEstimate>(props.estimate)


onMounted(() => {
  console.log('estimateTableItems mounted!!', props.estimate)
  console.log(props.estimate)
  console.log('>>> Workspace Name:')
  console.log(props.estimate.workspace_name)
});

</script>

<template>
  <div style="width: 100%; overflow-x: auto; font-size: 10.5pt;">
    <!-- Service Header -->
    <div v-if="estimate.services_list.length > 0" color-black style="width:100%; display: flex;">
      <!-- Service Items takes 4/5 of screen width to match other screen elements alignments. -->
      <div style="text-align: left;" class="md:ps-5 ps-1 md:w-4/5 w-full">
        ITEMS
      </div>
      <!-- Service Total takes 1/5 of screen width to match other screen elements alignments. -->
      <div style="width: auto; text-align: right; padding-inline-end: 10px;">
        TOTAL
      </div>
    </div>
    <div style="height: 10px;"></div>
    <!-- Service Items -->
    <div
      style="width: 100%; background: #efefef90; box-shadow: 0 1px 3px #ccc; border-top-left-radius: 15px; border-top-right-radius: 15px; display: block;padding-top: 10px; ">
      <div v-for="(item, i) in estimate.services_list" :key="i" style="width: 100%; display: flex; padding-bottom: 10px;">
        <div class="text-left md:ps-5 ps-1 md:w-4/5 w-full">
          {{ item.service_name }}
          <div class="text-left break-all" style="font-size: 13px; font-weight: 300;">
            {{ item.service_description }}
          </div>
        </div>
        <td style="text-align: right; padding-inline-end: 10px;">
          ${{ item.service_total.toFixed(2) }}
        </td>
      </div>
    </div>
  </div>
</template>

