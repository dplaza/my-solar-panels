<template>
  <div class="home">
    <input
      type="date"
      placeholder="Ingrese fecha de inicio"
      v-model="startDate"
    >
    <input
      type="date"
      placeholder="Ingrese fecha final"
      v-model="endDate"
    >
    <input
      type="text"
      placeholder="Ingrese cantidad de KwH de boleta"
      v-model="kwh"
    >
    <input
      type="text"
      placeholder="¿Dónde vives?"
      v-model="location"
    >

    <h2>Days: {{ calcDaysBetweenDates }}</h2>
    <h2>KwH per day: {{ calcKwHPerDay }} KwH</h2>
    <h2>Watts per day: {{ calcKwHToWatts }} W</h2>
    <h2>Daylight duration: {{ calcDaylightDuration }} hours</h2>
    <h2>
      Watts needed to generate during daylight per hour:
      {{ calcWattsNeededDuringDaylight }} W
    </h2>
    <h2>Watts generated by regular solar panel per hour: {{ solarPanelWatts }} W</h2>
    <h2>Solar panels: {{ calcSolarPanels }}</h2>
  </div>
</template>

<script>
import moment from 'moment';
import sunCalc from 'suncalc';

export default {
  name: 'Home',
  data: () => ({
    startDate: undefined,
    endDate: undefined,
    kwh: undefined,
    location: 'Reñaca, Chile',
    solarPanelWatts: 60,
  }),
  computed: {
    calcWattsNeededDuringDaylight() {
      if (!this.calcKwHToWatts || !this.calcDaylightDuration) return 0;
      return this.calcKwHToWatts / this.calcDaylightDuration;
    },
    calcDaylightDuration() {
      const { sunrise, sunset } = sunCalc.getTimes(new Date(), -32.999282199999996, -71.4952256);
      return moment(sunset).diff(moment(sunrise), 'hours');
    },
    calcKwHToWatts() {
      if (!this.calcKwHPerDay) return 0;
      return this.calcKwHPerDay * 1000;
    },
    calcSolarPanels() {
      if (!this.calcWattsNeededDuringDaylight) return 0;
      return Math.round(this.calcWattsNeededDuringDaylight / this.solarPanelWatts);
    },
    calcKwHPerDay() {
      if (!this.calcDaysBetweenDates || !this.kwh) return 0;
      return this.kwh / this.calcDaysBetweenDates;
    },
    calcDaysBetweenDates() {
      if (!this.startDate || !this.endDate) return 0;

      const startDate = moment(this.startDate);
      const endDate = moment(this.endDate);
      const diffInDays = endDate.diff(startDate, 'days');

      return diffInDays;
    },
  },
};
</script>
