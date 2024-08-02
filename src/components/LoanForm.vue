<template>
  <b-container>
    <b-row>
      <b-col>
        <h1>Modul Pinjaman</h1>
        <b-form @submit.prevent="calculateAnnuity">
          <b-form-group label="Plafond:" label-for="principal">
            <b-form-input
              id="principal"
              v-model.number="principal"
              type="number"
              required
            ></b-form-input>
          </b-form-group>
          <b-form-group
            label="Suku Bunga %(per tahun):"
            label-for="annualInterestRate"
          >
            <b-form-input
              id="annualInterestRate"
              v-model.number="annualInterestRate"
              type="number"
              required
            ></b-form-input>
          </b-form-group>
          <b-form-group label="Lama Pinjaman (bulan):" label-for="loanTerm">
            <b-form-input
              id="loanTerm"
              v-model.number="loanTerm"
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
          <b-button type="submit" variant="primary">Hitung</b-button>
        </b-form>
        <loan-schedule
          v-if="monthlyPayment"
          :schedule="loanSchedule"
          :formatNumber="formatNumber"
        ></loan-schedule>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import LoanSchedule from "./LoanSchedule.vue";

export default {
  components: {
    LoanSchedule,
  },
  data() {
    return {
      principal: 0, // Default principal amount
      annualInterestRate: 0, // Default annual interest rate in percentage
      loanTerm: 0, // Default loan term in years
      startDate: "", // Start date for the loan payments
      monthlyPayment: null, // Monthly payment to be calculated
      loanSchedule: [], // Loan schedule array
    };
  },
  methods: {
    calculateAnnuity() {
      if (
        this.principal <= 0 ||
        this.annualInterestRate <= 0 ||
        this.loanTerm <= 0
      ) {
        alert("Please enter valid values for all fields.");
        return;
      }

      const P = this.principal;
      const r = this.annualInterestRate / 100 / 12; // Convert annual interest rate to monthly
      const n = this.loanTerm;

      // Calculate the monthly annuity payment using the formula
      const A = (P * r) / (1 - Math.pow(1 + r, -n));
      this.monthlyPayment = A.toFixed(2);

      this.calculateSchedule(P, r, n, this.startDate);
    },
    roundToNearestInteger(amount) {
      return Math.round(amount);
    },
    formatNumber(number) {
      return number.toLocaleString("en-US");
    },
    calculateSchedule(principal, monthlyRate, totalMonths, startDate) {
      const schedule = [];
      let remainingPrincipal = principal;
      let currentDate = new Date(startDate);
      currentDate.setMonth(currentDate.getMonth() + 1);

      for (let i = 1; i <= totalMonths; i++) {
        const interestPayment = remainingPrincipal * monthlyRate;
        const principalPayment = this.monthlyPayment - interestPayment;
        remainingPrincipal -= principalPayment;

        schedule.push({
          month: i,
          date: currentDate.toISOString().split("T")[0],
          totalPayment: this.formatNumber(
            parseFloat(this.monthlyPayment).toFixed(2)
          ),
          principalPayment: this.formatNumber(
            this.roundToNearestInteger(principalPayment)
          ),
          interestPayment: this.formatNumber(
            this.roundToNearestInteger(interestPayment)
          ),
          remainingPrincipal: this.formatNumber(
            this.roundToNearestInteger(remainingPrincipal)
          ),
        });

        currentDate.setMonth(currentDate.getMonth() + 1);
      }

      this.loanSchedule = schedule;
    },
  },
};
</script>
