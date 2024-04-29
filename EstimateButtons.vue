<template>
  <div class="button-container" :class="showPayDeposit() ? 'md:h-10 h-25' : 'h-10'">
    <button class="action-button" :class="showPayDeposit() ? 'md:w-auto w-33' : 'w-auto'" @click="openPdf">
      .PDF
    </button>
    <div v-if="showActionButtons()" class="hidden md:block">
      <EstimatesEstimateActionsButtons :estimate="estimate" :id="id" />
    </div>
    <!-- max-width is 140px here to match pdf button width on mobile and height is 40 to match height on all platforms.-->
    <Button v-if="showPayDeposit()" @click="createDepositCheckout" style="max-width: 140px; height: 40px;">Pay
      Deposit</Button>
    <div v-if="loading" class="loading-page">
      <p>Loading...</p>
    </div>
  </div>
</template>

<script setup lang="ts">

import type { IEstimate } from "~/types/iEstimate";
import { EstimateApi } from "~/composables/estimates/estimateApi";

const props = defineProps<{ estimate: IEstimate, id: string }>()
const estimate = ref<IEstimate>(props.estimate);
const id = ref<string>(props.id);
const loading = ref(false);
const route = useRoute();
let currentUrl = "https://369.dev" + route.fullPath;


async function createDepositCheckout() {
  loading.value = true; // start loading
  try {
    var stripeAccountId = await EstimateApi.getStripeAccountId({ workspace_id: estimate.value.workspace_id }); // This is an api call to get stripe account id.
    console.log("stripeAccountId: " + stripeAccountId);
    if (stripeAccountId && stripeAccountId !== "") { // If stripe account id is not empty.
      // This is an api call to create stripe checkout.
      var checkout = await EstimateApi.createStripeCheckout({
        amount: estimate.value.deposit_amount,
        client_full_name: estimate.value.client_full_name,
        client_email: estimate.value.client_email,
        currency_code: estimate.value.currency_data.code,
        stripe_account_id: stripeAccountId,
        user_id: "", // this isn't used right now.
        workspace_id: estimate.value.workspace_id,
        invoice_id: estimate.value.invoice_id,
        estimate_id: estimate.value.id,
        is_deposit: "true",
        redirect_url: currentUrl,
      });
      if (checkout && checkout.url !== "") {
        window.open(checkout.url, "_self"); // Open stripe checkout page.
      }
    }
  } finally {
    loading.value = false; // stop loading
  }
}


function showPayDeposit() {
  return estimate.value.is_deposit_paid === false && estimate.value.deposit_amount !== 0 && estimate.value._accepted_at !== 0;
}

function showActionButtons() {
  return estimate.value._accepted_at === 0 && estimate.value._declined_at === 0 && !estimate.value.is_paid;
}

function openPdf() {
  window.open(props.estimate.estimate_pdf_url, '_blank')
}
</script>

<style scoped>
.button-container {
  display: flex;
  /* make the buttons in one line */
  justify-content: flex-end;
  /* align the buttons to the right */
  gap: 10px;
  /* Space between buttons */
  width: 50%;
  /* width of the buttons container */
  /* Space between buttons */
}

/* show loading on top of the buttons so user can't click on them */
.loading-page {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.8);
  text-align: center;
  padding-top: 200px;
  font-size: 30px;
  font-family: sans-serif;
  z-index: 1;
}

.action-button {
  background: #efefef90;
  color: black;
  box-shadow: 0 0 3px #ccc;
  border: none;
  padding: 10px 15px;
  border-radius: 8px;
  cursor: pointer;
  align-items: center;
  /* align the text and icon in the center */
  gap: 5px;
  height: 40px;
}

.action-button:hover {
  box-shadow: 0 0 10px #ccc;
}

@media (max-width: 750px) {
  .button-container {
    flex-direction: column;
    justify-content: flex-end;
    align-items: flex-end;
    /* Change the direction to column on mobile */
  }
}
</style>
