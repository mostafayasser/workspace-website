<template>
    <div class="button-container">
        <Button @click="visible = true" class="w-1/2 flex justify-center">Accept</Button>
        <Button @click="decline" severity="danger" class="w-1/2 flex justify-center">Decline</Button>
    </div>
    <Dialog v-model:visible="visible" modal header="Accept Estimate" :style="{ width: '25rem' }">
        <span class="p-text-secondary block mb-5 mt-5">Please write your full name to accept this estimate</span>
        <div class="flex items-center gap-3 mb-5">
            <label for="fullname" class="font-semibold w-6rem">Full name</label>
            <div>
                <InputText id="fullname" v-model="fullname" class="flex-auto" autocomplete="off" />
                <div v-if="errorMessage" class="text-red-500">{{ errorMessage }}</div>
            </div>

        </div>
        <div v-if="!loading" class="flex justify-end gap-2">
            <Button type="button" label="Cancel" severity="secondary" @click="cancel"></Button>
            <Button type="button" label="Save" @click="save"></Button>
        </div>
        <div v-if="loading" class="card flex justify-content-center">
            <ProgressSpinner style="width: 50px; height: 50px" strokeWidth="8" fill="var(--surface-ground)"
                animationDuration=".5s" aria-label="Custom ProgressSpinner" />
        </div>
    </Dialog>
</template>


<script setup lang="ts">

import { useConfirm } from "primevue/useConfirm";
import { useToast } from "primevue/useToast";
import type { IEstimate } from "~/types/iEstimate";
import { EstimateApi } from "~/composables/estimates/estimateApi";

const props = defineProps<{ estimate: IEstimate, id: string }>()
const estimate = ref<IEstimate>(props.estimate);
const id = ref<string>(props.id);

const confirm = useConfirm();
const toast = useToast();

const loading = ref(false);
const visible = ref(false);

let fullname = ref('');
let errorMessage = ref('');

// Update errorMessage based on your validation logic
// For example, if the input value should not be empty:
watch(fullname, (newVal) => {
    if (!newVal.trim()) {
        errorMessage.value = 'This field is required';
    } else {
        errorMessage.value = '';
    }
});

const save = async () => { // Add this function
    if (fullname.value.trim() === '') {
        // Show a message to the user, or handle the empty input case as you see fit
        errorMessage.value = 'Full name is required';
        toast.add({ severity: 'error', summary: 'Error', detail: 'Full name is required', life: 3000 });
    } else {
        loading.value = true; // start loading
        try {
            var updated = await EstimateApi.updateEstimateStatus({ id: id.value, status: 'Accepted' }); // This is an api call to update the status of estimate.
            if (updated) {
                var now = Date.now();
                estimate.value.estimate_status = 'Accepted';
                estimate.value._accepted_at = now;
                estimate.value._updated_at = now;
                toast.add({ severity: 'info', summary: 'Confirmed', detail: 'You have accepted the estimate', life: 3000 }); // This is a global component to show toast after accepting or declining.
            }
        } catch (error) {
            loading.value = false; // stop loading
        }
        visible.value = false;
    }
}

const cancel = () => {
    toast.add({ severity: 'error', summary: 'Rejected', detail: 'You have canceled', life: 3000 }); // This is a global component to show toast if cancel accepting or declining.
    visible.value = false;
}


const decline = () => {
    confirm.require({
        message: 'Do you want to delete this record?',
        header: 'Delete Confirmation',
        icon: 'pi pi-exclamation-triangle',
        rejectClass: 'p-button-text p-button-text',
        acceptClass: 'p-button-danger p-button-text',
        accept: async () => {
            loading.value = true;
            try {
                var updated = await EstimateApi.updateEstimateStatus({ id: id.value, status: 'Declined' });
                if (updated) {
                    var now = Date.now();
                    estimate.value.estimate_status = 'Declined';
                    estimate.value._declined_at = now;
                    estimate.value._updated_at = now;
                    toast.add({ severity: 'info', summary: 'Confirmed', detail: 'You have declined the estimate', life: 3000 });
                }
            } finally {
                loading.value = false;
            }
        },
        reject: () => {
            toast.add({ severity: 'error', summary: 'Rejected', detail: 'You have canceled', life: 3000 });
        }
    });
};

</script>

<style scoped>
.button-container {
    display: flex;
    /* to make the buttons in one line */
    justify-content: space-between;
    /* space between buttons */
    gap: 10px;
    /* gap between buttons in row and column */
    height: 40px;
    /* height of container */
    width: 100%;
    /* width of container */
}
</style>