<template>
  <b-container>
    <b-row>
      <b-col>
        <h1>Loan Calculator</h1>
        <b-form @submit.prevent="calculateSchedule">
          <b-form-group label="Plafond:" label-for="plafond">
            <b-form-input
              id="plafond"
              v-model="plafond"
              type="number"
              required
            ></b-form-input>
          </b-form-group>
          <b-form-group label="Lama Pinjaman (bulan):" label-for="loanTerm">
            <b-form-input
              id="loanTerm"
              v-model="loanTerm"
              type="number"
              required
            ></b-form-input>
          </b-form-group>
          <b-form-group
            label="Suku Bunga (% per tahun):"
            label-for="interestRate"
          >
            <b-form-input
              id="interestRate"
              v-model="interestRate"
              type="number"
              required
            ></b-form-input>
          </b-form-group>
          <b-form-group label="Tanggal Mulai Angsuran:" label-for="startDate">
            <b-form-input
              id="startDate"
              v-model="startDate"
              type="date"
              required
            ></b-form-input>
          </b-form-group>
          <b-button type="submit" variant="primary">Calculate</b-button>
        </b-form>
        <LoanSchedule v-if="schedule.length" :schedule="schedule" />
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import LoanSchedule from "./LoanSchedule.vue";
import logger from "@/services/logger";

export default {
  components: {
    LoanSchedule,
  },
  data() {
    return {
      plafond: 0,
      loanTerm: 0,
      interestRate: 0,
      startDate: "",
      schedule: [],
    };
  },
  methods: {
    calculateSchedule() {
      logger.log("Calculating loan schedule");
      const I = this.interestRate / 100;
      const P = this.plafond;
      const H = 30;
      const schedule = [];
      let remainingPrincipal = P;
      let currentDate = new Date(this.startDate);

      for (let i = 1; i <= this.loanTerm; i++) {
        const interestPayment = (I / 360) * H * remainingPrincipal;
        const principalPayment = P / this.loanTerm;
        const totalPayment = interestPayment + principalPayment;
        remainingPrincipal -= principalPayment;

        // Move to the next month and format the date to yyyy-mm-dd
        currentDate.setMonth(currentDate.getMonth() + 1);
        const formattedDate = currentDate.toISOString().split("T")[0];

        schedule.push({
          installment: i,
          date: formattedDate,
          totalPayment: totalPayment.toFixed(2),
          principalPayment: principalPayment.toFixed(2),
          interestPayment: interestPayment.toFixed(2),
          remainingPrincipal: remainingPrincipal.toFixed(2),
        });
      }

      this.schedule = schedule;
      logger.log("Loan schedule calculated successfully");
    },
  },
};
</script>
