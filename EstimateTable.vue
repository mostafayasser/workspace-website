<script setup lang="ts">
import type { IEstimate } from "~/types/iEstimate";
import type { IWorkspace } from "~/types/iWorkspace";

const props = defineProps<{ estimate: IEstimate; workspace: IWorkspace }>();


const isLoading = ref(true);

function timestampToData(timestamp: number) {
  const date = new Date(timestamp);
  const year = date.getFullYear();
  const month = date.getMonth() + 1;
  const day = date.getDate();

  return `${month}/${day}/${year}`;
}

nextTick(async () => {
  console.log("nextTICK");
  await new Promise((resolve) => setTimeout(resolve, 1000));
  isLoading.value = false;
});
</script>


<template>
  <div style="width: 100%; position: relative; ">
    <div class="paper ps-1 pr-1 py-1" style="width: 100%;  border-radius: 15px; box-shadow: 0 0 6px #ccc ">
      <div>
        <div class="grid-container pt-5 md:ps-5 ps-1">
          <div class="rounded-lg p-2" :style="{
            backgroundColor: estimate.is_paid ? 'rgba(76, 175, 80, 0.3)' : 'rgba(158, 158, 158, 0.3)', /* change background color if paid */
            color: estimate.is_paid ? '#4CAF50' : 'rgba(158, 158, 158, 1)' /* change text color if paid */
          }" style="width: 100px; border-radius: 8px;">
            {{ estimate.estimate_status }}</div>
          <div class="text-left pt-2">
            <p>{{ estimate.estimate_name }}</p>
          </div>
        </div>
        <div class="grid-container pt-5 md:ps-5 ps-1">
          <div class="text-left rounded-lg" style=" font-size: 40px;">
            #EST-{{ estimate.estimate_number }}</div>
          <div class="text-left">
            <h6>Date Issued</h6>
            <p>{{ timestampToData(estimate._created_at) }}</p>
          </div>
        </div>
        <div class="dotted-line"></div>
        <div class="grid-container items-start  md:ps-5 ps-1">
          <table mb-5 mt-5 text-left>
            <tbody>
              <tr>
                <td>
                  <span font-bold style="font-size: 11px;">FROM</span>
                </td>
              </tr>
              <tr class="text-bolder">
                <td style="font-size: 10.5pt">
                  <span font-bold>{{ workspace.info.name }}</span>
                </td>
              </tr>
              <tr>
                <td style="font-size: 10.5pt">
                  {{ workspace.location_data.address.formatted_address }}
                </td>
              </tr>
              <tr>
                <td style="font-size: 10.5pt">
                  {{ workspace.info.email }}
                </td>
              </tr>
              <tr>
                <td style="font-size: 10.5pt">
                  {{ workspace.phone_model.international_number }}
                </td>
              </tr>
            </tbody>
          </table>
          <table mb-5 mt-5 text-left>
            <tbody>
              <tr>
                <td>
                  <span font-bold style="font-size: 11px;">BILL TO</span>
                </td>
              </tr>
              <tr class="text-bolder">
                <td style="font-size: 10.5pt">
                  <span font-bold>{{ estimate.client_full_name }}</span>
                </td>
              </tr>
              <tr>
                <td style="font-size: 10.5pt">
                  {{ estimate.client_address }}
                </td>
              </tr>
              <tr>
                <td style="font-size: 10.5pt">
                  {{ estimate.client_email }}
                </td>
              </tr>
              <tr>
                <td style="font-size: 10.5pt">
                  {{ estimate.client_phone.international_number }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
        <estimates-estimate-table-items :estimate="estimate" />
        <div style="background: #efefef90;">
          <div class="divider-line"></div>
        </div>
        <table
          style="width: 100%; border-collapse: collapse; background: #efefef90;  border-bottom-left-radius: 15px; border-bottom-right-radius: 15px; ">
          <tbody>
            <tr>
              <td style="width: 54%;"></td>
              <td style="text-align: left; font-size: 10.5pt;">
                Subtotal
              </td>
              <td style="font-size: 10.5pt" class="text-right md:text-left w-1/5 pr-2">
                {{ estimate.currency_data.symbol }}{{ estimate.sub_total.toFixed(2) }}
              </td>
            </tr>
            <tr v-if="estimate.discount_amount !== 0">
              <td style="width: 54%;"></td>
              <td class="text-bolder" style="text-align: left; font-size: 10.5pt">
                Discount ({{ estimate.discount_percentage }}%)
              </td>
              <td style="font-size: 10.5pt" class="text-right md:text-left w-1/5 pr-2">
                {{ estimate.currency_data.symbol }}{{ estimate.discount_amount.toFixed(2) }}
              </td>
            </tr>
            <tr v-if="estimate.tax_amount !== 0">
              <td style="width: 54%;"></td>
              <td class="text-bolder" style="text-align: left; font-size: 10.5pt">Tax ({{ estimate.tax_percentage }}%)
              </td>
              <td style="font-size: 10.5pt" class="text-right md:text-left w-1/5 pr-2">
                {{ estimate.currency_data.symbol }}{{ estimate.tax_amount.toFixed(2) }}
              </td>
            </tr>
            <tr color-black>
              <td style="width: 54%;"></td>
              <td width-2xl class="text-bolder py-1" style="text-align: left;">
                Total
              </td>
              <td class="text-right md:text-left w-1/5 pr-2">
                {{ estimate.currency_data.symbol }}{{ estimate.total.toFixed(2) }}
              </td>
            </tr>
            <tr v-if="estimate.deposit_amount != 0">
              <td style="width: 54%;"></td>
              <td class="text-bolder" style="text-align: left; font-size: 10.5pt">
                Deposit ({{ estimate.deposit_percentage }}%){{ props.estimate.is_deposit_paid ? 'Paid' : '' }}
              </td>
              <td style="font-size: 10.5pt" class="text-right md:text-left w-1/5 pr-2">
                {{ estimate.currency_data.symbol }}{{ estimate.deposit_amount.toFixed(2) }}
              </td>
            </tr>
            <tr v-if="estimate.deposit_amount != 0">
              <td style="width: 54%;"></td>
              <td class="text-bolder" style="text-align: left">Amount</td>
              <td class="text-right md:text-left w-1/5 pr-2">
                {{ estimate.currency_data.symbol }}{{ (estimate.total - estimate.deposit_amount).toFixed(2) }}
              </td>
            </tr>
            <div style=" height: 10px; "></div>
          </tbody>
        </table>

      </div>
    </div>
  </div>
</template>


<style>
.grid-container {
  display: grid;
  grid-template-columns: 53% auto;
  /* 2 columns where one is 53% of width and the other takes remaining width  */
  gap: 10px;
  /* Optional: Add gap between the divs */
}

.dotted-line {
  border-top: 1px dotted rgba(128, 128, 128, 0.5);
  /* 1px dashed line with black color */
  width: 100%;
  /* Set the width as needed */
  margin-top: 5px;
}

.divider-line {
  border-top: 1px solid rgba(128, 128, 128, 0.5);
  /* 1px dashed line with black color */
  /* Set the width as needed */
  padding-bottom: 10px;
  width: 95%;
  /* Set the width as needed */
  margin: 0 auto;
}

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

@media (max-width: 750px) {
  .divider-line {
    width: 97%;
    /* leave space for padding */
  }
}
</style>
