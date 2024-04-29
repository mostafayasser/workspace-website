<template>
    <div style="width: 100%; position: relative"></div>
    <div class="paper ps-5 pr-5 py-5" style="width: 100%;  border-radius: 10px; box-shadow: 0 0 6px #ccc ">
        <div v-if="estimate.attachments.length > 0" mb-5>
            <h1 text-left font-bold mt-5>Attachments</h1>
            <div class="flex" style="
        height: 100%;
        overflow-x: auto; /* enable horizontal scrolling */
        margin: 0px;
        width: 100%;
        overflow-y: hidden; /* disable vertical scrolling */
        padding-top: 10px;
      ">
                <div v-for="(attachemnt, i) in estimate.attachments" :key="i" pr-2>
                    <el-image v-if="attachemnt.type === 'image'" style="height: 100px; width: 100px; border-radius: 10px;"
                        ma-auto :hide-on-click-modal="true" fit="cover" :src="attachemnt.url"
                        :preview-src-list="[attachemnt.url]" /> <!-- preview-src-list only shows selected image -->
                    <!-- show pdf icon clickable to open pdf at new tab-->
                    <a v-else-if="attachemnt.file_extension === 'pdf'" :href="attachemnt.url" target="_blank"
                        style="height: 100px; width: 100px; border-radius: 10px; display: flex; justify-content: center; align-items: center; background: #efefef90;">
                        <i class="pi pi-file-pdf" style="font-size: 40px;"></i>
                    </a>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
import type { IEstimate } from '~/types/iEstimate'
import { defineProps, ref } from 'vue'

const props = defineProps<{ estimate: IEstimate }>()

const estimate = ref<IEstimate>(props.estimate)

</script>

