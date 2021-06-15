<template>
  <tr>
    <td>{{ (new Date(date).getMonth() + 1) + "/" + (new Date(date).getDate() + 1) }}</td>
    <td>{{ aircraft_model }}</td>
    <td>{{ aircraft_id }}</td>
    <td>{{ from }}</td>
    <td>{{ route }}</td>
    <td>{{ to }}</td>
    <td class="gray">{{ asel }}</td>
    <td>{{ amel }}</td>
    <td>{{ ground_trainer }}</td>
    <td class="gray">{{ dual }}</td>
    <td>{{ pic }}</td>
    <td class="gray">{{ day_time }}</td>
    <td>{{ night_time }}</td>
    <td class="gray">{{ cross_country }}</td>
    <td>{{ actual_instrument }}</td>
    <td class="gray">{{ simulated_instrument }}</td>
    <td>{{ number_instrument_approaches }}</td>
    <td>{{ day_landings }}/{{ night_landings }}</td>
    <td>{{ total_time }}</td>
    <td class="left">{{ comments }}</td>
  </tr>
</template>

<script>

export default {
  name: "Flight",
  props: [
      "actual_instrument",
      "aircraft_id",
      "aircraft_model",
      "asel",
      "amel",
      "comments",
      "cross_country",
      "day_time",
      "day_landings",
      "date",
      "dual",
      "from",
      "ground_trainer",
      "night_time",
      "night_landings",
      "number_instrument_approaches",
      "pic",
      "route",
      "simulated_instrument",
      "to",
      "total_time"
  ]
}
</script>

<style scoped>
td {
  font-size: 10pt;
  text-align: right;
  border: 1px solid #666;
}
td.left {
  text-align: left;
}
td.gray {
  background-color: #f0f0f0;
}
</style>