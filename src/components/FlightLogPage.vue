<template>
  <div>
  <h3>Page {{ page_number }}</h3>
  <table>
    <tr>
      <th>Year<br />{{ (new Date(flights[0].date)).getFullYear() }}<br />Date</th>
      <th>Aircraft<br />Make &amp; Model</th>
      <th>Aircraft<br />Ident</th>
      <th>From</th>
      <th><!--Route--></th>
      <th>To</th>
      <th>Airplane<br />SEL</th>
      <th>Airplane<br />MEL</th>
      <th>Ground<br />Trainer</th>
      <th>Dual<br />Received</th>
      <th>Pilot-in-<br />Command</th>
      <th>Day</th>
      <th>Night</th>
      <th>Cross-<br />Country</th>
      <th>Actual<br />Instr.</th>
      <th>Simulated<br />Instr.</th>
      <th>No.<br />Instr.<br />Appr.</th>
      <th>No.<br />Ldg.<br />Day/<br />Night</th>
      <th>Total<br />Duration<br />of Flight</th>
      <th>Comments</th>
    </tr>
    <flight
        v-for="(flight) in flights"
        v-bind:actual_instrument="flight.actual_instrument"
        v-bind:aircraft_id="flight.aircraft_id"
        v-bind:aircraft_model="flight.aircraft_model"
        v-bind:asel="flight.asel"
        v-bind:amel="flight.amel"
        v-bind:comments="flight.comments"
        v-bind:cross_country="flight.cross_country"
        v-bind:day_time="flight.day_time"
        v-bind:day_landings="flight.day_landings"
        v-bind:date="flight.date"
        v-bind:dual="flight.dual"
        v-bind:from="flight.from"
        v-bind:ground_trainer="flight.ground_trainer"
        v-bind:night_time="flight.night_time"
        v-bind:night_landings="flight.night_landings"
        v-bind:number_instrument_approaches="flight.number_instrument_approaches"
        v-bind:pic="flight.pic"
        v-bind:route="flight.route"
        v-bind:simulated_instrument="flight.simulated_instrument"
        v-bind:to="flight.to"
        v-bind:total_time="flight.total_time"
        v-bind:key="flight.id"
    ></flight>
    <flight-summation-row
        v-bind:summary="'Page Total'"
        v-bind:actual_instrument="page_totals.actual_instrument.toFixed(1)"
        v-bind:asel="page_totals.asel.toFixed(1)"
        v-bind:amel="page_totals.amel.toFixed(1)"
        v-bind:cross_country="page_totals.cross_country.toFixed(1)"
        v-bind:day_time="page_totals.day_time.toFixed(1)"
        v-bind:day_landings="page_totals.day_landings"
        v-bind:dual="page_totals.dual.toFixed(1)"
        v-bind:ground_trainer="page_totals.ground_trainer.toFixed(1)"
        v-bind:night_time="page_totals.night_time.toFixed(1)"
        v-bind:night_landings="page_totals.night_landings"
        v-bind:number_instrument_approaches="page_totals.number_instrument_approaches"
        v-bind:pic="page_totals.pic.toFixed(1)"
        v-bind:simulated_instrument="page_totals.simulated_instrument.toFixed(1)"
        v-bind:total_time="page_totals.total_time.toFixed(1)"
        v-bind:key="page_totals.id"
    ></flight-summation-row>
    <flight-summation-row
        v-bind:summary="'Amount Forward'"
        v-bind:actual_instrument="amount_forward.actual_instrument.toFixed(1)"
        v-bind:asel="amount_forward.asel.toFixed(1)"
        v-bind:amel="amount_forward.amel.toFixed(1)"
        v-bind:cross_country="amount_forward.cross_country.toFixed(1)"
        v-bind:day_time="amount_forward.day_time.toFixed(1)"
        v-bind:day_landings="amount_forward.day_landings"
        v-bind:dual="amount_forward.dual.toFixed(1)"
        v-bind:ground_trainer="amount_forward.ground_trainer.toFixed(1)"
        v-bind:night_time="amount_forward.night_time.toFixed(1)"
        v-bind:night_landings="amount_forward.night_landings"
        v-bind:number_instrument_approaches="amount_forward.number_instrument_approaches"
        v-bind:pic="amount_forward.pic.toFixed(1)"
        v-bind:simulated_instrument="amount_forward.simulated_instrument.toFixed(1)"
        v-bind:total_time="amount_forward.total_time.toFixed(1)"
        v-bind:key="amount_forward.id"
    ></flight-summation-row>
    <flight-summation-row
        v-bind:summary="'Total to Date'"
        v-bind:actual_instrument="total_to_date.actual_instrument.toFixed(1)"
        v-bind:asel="total_to_date.asel.toFixed(1)"
        v-bind:amel="total_to_date.amel.toFixed(1)"
        v-bind:cross_country="total_to_date.cross_country.toFixed(1)"
        v-bind:day_time="total_to_date.day_time.toFixed(1)"
        v-bind:day_landings="total_to_date.day_landings"
        v-bind:dual="total_to_date.dual.toFixed(1)"
        v-bind:ground_trainer="total_to_date.ground_trainer.toFixed(1)"
        v-bind:night_time="total_to_date.night_time.toFixed(1)"
        v-bind:night_landings="total_to_date.night_landings"
        v-bind:number_instrument_approaches="total_to_date.number_instrument_approaches"
        v-bind:pic="total_to_date.pic.toFixed(1)"
        v-bind:simulated_instrument="total_to_date.simulated_instrument.toFixed(1)"
        v-bind:total_time="total_to_date.total_time.toFixed(1)"
        v-bind:key="total_to_date.id"
    ></flight-summation-row>
  </table>
  </div>
</template>

<script>
import Flight from "@/components/Flight";
import FlightSummationRow from "@/components/FlightSummationRow"

export default {
  name: "FlightLogPage",
  components: {
    Flight,
    FlightSummationRow
  },
  props: [
    "amount_forward",
    "flights",
    "page_number",
    "page_totals",
    "total_to_date"
  ]
}
</script>

<style scoped>
table {
  border: 1px solid #666;
  border-collapse: collapse;
}
th {
  font-weight: normal;
  font-size: 10pt;
  border: 1px solid #666;
}
</style>
