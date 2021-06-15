<template>
  <div>
    <div id="header">
      <h1>Aviation Logbook Printer</h1>
      <div id="right-pane">
        <div class="file_container" v-on:drop.prevent="importFile" v-on:dragover.prevent v-if="flights.length === 0">
          <p>Drag your logbook export here.</p>
        </div>
      </div>
      <p></p>

      <flight-log-page
          v-for="(flights_page, page_index) in flight_pages"
          v-bind:page_number="page_index + 1"
          v-bind:flights="flights_page"
          v-bind:page_totals="flights_page_total(page_index)"
          v-bind:amount_forward="flights_page_amount_forward(page_index)"
          v-bind:total_to_date="flights_page_amount_forward(page_index + 1)"
          v-bind:key="flights_page.id"
      ></flight-log-page>
    </div>
  </div>
</template>

<script>
import FlightLogPage from "@/components/FlightLogPage";

import papa from "papaparse";


export default {
  name: 'App',
  components: {
    FlightLogPage
  },
  data() {
    return {
      flights: [],
      flight_pages: [],
      user_raw_data: '',
    }
  },
  methods: {
    formatted_date: function() {
      var d = new Date();
      return d.toDateString()
    },
    importFile: function(event) {
      let csv_file = event.dataTransfer.files[0];
      var reader = new FileReader();
      reader.readAsText(csv_file,"UTF-8");
      reader.onload = readerEvent => {
        var content = readerEvent.target.result;
        this.loadCSVFromRaw(content);
      }
    },
    loadCSVFromRaw: function(data) {
      this.user_raw_data = data;
    },
    getValueByHeader(headers, row, header) {
      for (let i = 0; i < headers.length; i++) {
        if (headers[i] === header)
          return row[i]
      }
      return -1;
    },
    getAircraftModelByID(aircraft_table, id) {
      for (let i = 0; i < aircraft_table.length; i++) {
        if (aircraft_table[i][0] === id)
          return this.getValueByHeader(aircraft_table[0], aircraft_table[i], "Model");
      }
      return -1;
    },
    get_number_instrument_approaches: function(header_row, data_row) {
      var count = 0;
      while (count < 10 && this.getValueByHeader(header_row, data_row,"Approach" + (count + 1).toString()) !== "") {
        count++;
      }
      return count;
    },
    handle_update_csv: function(results) {
      var csv_data = results.data
      var aircraft_first_row = -1;
      var aircraft_last_row = -1;
      var flights_first_row = -1;
      var flights_last_row = -1;
      for (let i = 0; i < csv_data.length; i++) {
        if (csv_data[i][0] === "Aircraft Table")
          aircraft_first_row = i + 1;
        else if (aircraft_first_row >= 0 && aircraft_last_row === -1 && csv_data[i][0] === "")
          aircraft_last_row = i;
        else if (csv_data[i][0] === "Flights Table")
          flights_first_row = i + 1;
        else if (flights_first_row >= 0 && flights_last_row === -1 && csv_data[i][0] === "")
          flights_last_row = i;
      }
      var raw_aircraft = csv_data.slice(aircraft_first_row, aircraft_last_row);
      var raw_flights = csv_data.slice(flights_first_row, flights_last_row);

      for (let i = 0; i < raw_flights.length; i++) {
        if (i === 0)
          continue; // Skip header row

        var aircraft_id = this.getValueByHeader(raw_flights[0], raw_flights[i], "AircraftID");
        var night_time = this.getValueByHeader(raw_flights[0], raw_flights[i], "Night");
        var total_time = this.getValueByHeader(raw_flights[0], raw_flights[i], "TotalTime");
        var day_time = (total_time - night_time).toFixed(1);
        this.flights.push({
          actual_instrument: this.getValueByHeader(raw_flights[0], raw_flights[i], "ActualInstrument"),
          aircraft_id: aircraft_id,
          aircraft_model: this.getAircraftModelByID(raw_aircraft, aircraft_id),
          amel: 0,
          asel: this.getValueByHeader(raw_flights[0], raw_flights[i], "TotalTime"),
          comments: this.getValueByHeader(raw_flights[0], raw_flights[i], "PilotComments").replaceAll("  ", ", "),
          cross_country: this.getValueByHeader(raw_flights[0], raw_flights[i], "CrossCountry"),
          date: Date.parse(this.getValueByHeader(raw_flights[0], raw_flights[i], "Date")),
          day_landings: this.getValueByHeader(raw_flights[0], raw_flights[i], "DayLandingsFullStop"),
          day_time: day_time,
          dual: this.getValueByHeader(raw_flights[0], raw_flights[i], "DualReceived"),
          from: this.getValueByHeader(raw_flights[0], raw_flights[i], "From"),
          ground_trainer: this.getValueByHeader(raw_flights[0], raw_flights[i], "GroundTraining"),
          night_landings: this.getValueByHeader(raw_flights[0], raw_flights[i], "NightLandingsFullStop"),
          night_time: night_time,
          number_instrument_approaches: this.get_number_instrument_approaches(raw_flights[0], raw_flights[i]),
          pic: this.getValueByHeader(raw_flights[0], raw_flights[i], "PIC"),
          route: this.getValueByHeader(raw_flights[0], raw_flights[i], "Route"),
          simulated_instrument: this.getValueByHeader(raw_flights[0], raw_flights[i], "SimulatedInstrument"),
          to: this.getValueByHeader(raw_flights[0], raw_flights[i], "To"),
          total_time: total_time,
        });
      }
      this.flights.reverse();

      for (let i = 0; i < this.flights.length; i++) {
        if (i % 7 === 0)
          this.flight_pages.push([this.flights[i]]);
        else
          this.flight_pages[this.flight_pages.length - 1].push(this.flights[i]);
      }
    },
    add_totals: function(t1, t2) {
      return {
        actual_instrument: t1.actual_instrument + t2.actual_instrument,
        asel: t1.asel + t2.asel,
        amel: t1.amel + t2.amel,
        cross_country: t1.cross_country + t2.cross_country,
        day_time: t1.day_time + t2.day_time,
        day_landings: t1.day_landings + t2.day_landings,
        dual: t1.dual + t2.dual,
        ground_trainer: t1.ground_trainer + t2.ground_trainer,
        night_time: t1.night_time + t2.night_time,
        night_landings: t1.night_landings + t2.night_landings,
        number_instrument_approaches: t1.number_instrument_approaches + t2.number_instrument_approaches,
        pic: t1.pic + t2.pic,
        simulated_instrument: t1.simulated_instrument + t2.simulated_instrument,
        total_time: t1.total_time + t2.total_time
      }
    },
    flights_page_amount_forward: function(flights_page_index) {
      if (flights_page_index === 0) {
        return {
          actual_instrument: 0,
          asel: 0,
          amel: 0,
          cross_country: 0,
          day_time: 0,
          day_landings: 0,
          dual: 0,
          ground_trainer: 0,
          night_time: 0,
          night_landings: 0,
          number_instrument_approaches: 0,
          pic: 0,
          simulated_instrument: 0,
          total_time: 0
        };
      }
      else if (flights_page_index === 1) {
        return this.flights_page_total(0);
      }
      else {
        return this.add_totals(
            this.flights_page_total(flights_page_index - 1),
            this.flights_page_amount_forward(flights_page_index - 1)
        );
      }
    },
    flights_page_total: function(flights_page_index) {
      var totals = {
        actual_instrument: 0,
        asel: 0,
        amel: 0,
        cross_country: 0,
        day_time: 0,
        day_landings: 0,
        dual: 0,
        ground_trainer: 0,
        night_time: 0,
        night_landings: 0,
        number_instrument_approaches: 0,
        pic: 0,
        simulated_instrument: 0,
        total_time: 0
      };

      for (let i = 0; i < this.flight_pages[flights_page_index].length; i++) {
        totals.actual_instrument += (parseFloat(this.flight_pages[flights_page_index][i].actual_instrument) || 0.0);
        totals.asel += (parseFloat(this.flight_pages[flights_page_index][i].asel) || 0.0);
        totals.amel += (parseFloat(this.flight_pages[flights_page_index][i].amel) || 0.0);
        totals.cross_country += (parseFloat(this.flight_pages[flights_page_index][i].cross_country) || 0.0);
        totals.day_time += (parseFloat(this.flight_pages[flights_page_index][i].day_time) || 0.0);
        totals.day_landings += (parseInt(this.flight_pages[flights_page_index][i].day_landings) || 0);
        totals.dual += (parseFloat(this.flight_pages[flights_page_index][i].dual) || 0.0);
        totals.ground_trainer += (parseFloat(this.flight_pages[flights_page_index][i].ground_trainer) || 0.0);
        totals.night_time += (parseFloat(this.flight_pages[flights_page_index][i].night_time) || 0.0);
        totals.night_landings += (parseInt(this.flight_pages[flights_page_index][i].night_landings) || 0);
        totals.number_instrument_approaches += (parseInt(this.flight_pages[flights_page_index][i].number_instrument_approaches) || 0);
        totals.pic += (parseFloat(this.flight_pages[flights_page_index][i].pic) || 0.0);
        totals.simulated_instrument += (parseFloat(this.flight_pages[flights_page_index][i].simulated_instrument) || 0.0);
        totals.total_time += (parseFloat(this.flight_pages[flights_page_index][i].total_time) || 0.0);
      }

      return totals;
    },
    parse: function(raw) {
      this.parse_csv(raw);
    },
    parse_csv: function(raw) {
      var handle = this;
      papa.parse(raw, {
        complete: function(results) {
          handle.handle_update_csv(results)
        }
      });
    },
  },
  watch: {
    user_raw_data: function(newVal) {
      this.parse(newVal);
    }
  },
}
</script>

<style>
div {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

@media screen {
  div#header {
    display: block;
    clear: both;
    float: left;
    height: auto;
    width: 100%;
    margin-bottom: 8px;
  }
  div#right-pane {
    float: left;
    display: block;
    width: 100%;
  }
  div#right-pane .file_container {
    width: 90%;
    height: 50px;
    border: 2px dotted gray;
    text-align: center;
  }
}
</style>
