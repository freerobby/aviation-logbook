<template>
  <tr>
    <td colspan="6" align="right">{{ summary }}</td>
    <td class="bordered gray">{{ asel }}</td>
    <td class="bordered">{{ amel }}</td>
    <td class="bordered">{{ ground_trainer }}</td>
    <td class="bordered gray">{{ dual }}</td>
    <td class="bordered">{{ pic }}</td>
    <td class="bordered gray">{{ day_time }}</td>
    <td class="bordered">{{ night_time }}</td>
    <td class="bordered gray">{{ cross_country }}</td>
    <td class="bordered">{{ actual_instrument }}</td>
    <td class="bordered gray">{{ simulated_instrument }}</td>
    <td class="bordered">{{ number_instrument_approaches }}</td>
    <td class="bordered">{{ day_landings }}/{{ night_landings }}</td>
    <td class="bordered">{{ total_time }}</td>
    <td>{{ comments }}</td>
  </tr>
</template>

<script>

export default {
  name: "FlightSummationRow",
  props: [
    "actual_instrument",
    "asel",
    "amel",
    "comments",
    "cross_country",
    "day_time",
    "day_landings",
    "dual",
    "ground_trainer",
    "night_time",
    "night_landings",
    "number_instrument_approaches",
    "pic",
    "simulated_instrument",
    "summary",
    "total_time"
  ]
}
</script>

<style scoped>
td {
  font-size: 10pt;
  text-align: right;
}
td.bordered {
  border: 1px solid #666;
}
td.gray {
  background-color: #f0f0f0;
}
</style>