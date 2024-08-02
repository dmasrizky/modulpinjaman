<template>
  <b-table :items="schedule" :fields="fields">
    <template #cell(date)="data">
      {{ data.value }}
    </template>
    <template #cell(totalPayment)="data">
      {{ data.value }}
    </template>
    <template #cell(principalPayment)="data">
      {{ data.value }}
    </template>
    <template #cell(interestPayment)="data">
      {{ data.value }}
    </template>
    <template #cell(remainingPrincipal)="data">
      {{ data.value }}
    </template>
  </b-table>
</template>

<script>
export default {
  props: {
    schedule: {
      type: Array,
      required: true,
    },
    formatNumber: {
      type: Function,
      required: true,
    },
  },
  data() {
    return {
      fields: [
        { key: "month", label: "Angsuran ke" },
        { key: "date", label: "Tanggal" },
        { key: "totalPayment", label: "Total Angsuran" },
        { key: "principalPayment", label: "Angsuran Pokok" },
        { key: "interestPayment", label: "Angsuran Bunga" },
        { key: "remainingPrincipal", label: "Sisa Angsuran Pokok" },
      ],
    };
  },
};
</script>
