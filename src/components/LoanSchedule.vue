<template>
  <b-container>
    <h2>Amortization Schedule</h2>
    <b-table :items="schedule" :fields="fields" striped hover></b-table>
  </b-container>
</template>

<script>
export default {
  props: {
    schedule: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      fields: [
        { key: "installment", label: "Angsuran ke" },
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
