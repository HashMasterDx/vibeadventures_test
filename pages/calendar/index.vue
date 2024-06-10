<script setup>
import moment from "moment";

const date = ref(new Date());
const value = ref(null);
const lastDate = ref(null);

//Format the date accord to the specifications
const dateFormatted = computed(() => {
  if (Array.isArray(date.value) && date.value[1]) {
    //Validate if the two dates on the array are the same, format the date to show as one on the InputText
    if (moment(date.value[0]).format('DD/MM/YYYY') === moment(date.value[1]).format('DD/MM/YYYY')) {
      return moment(date.value[0]).format('DD/MM/YYYY');
    }
    return `${moment(date.value[0]).format('DD/MM/YYYY')} -  ${moment(date.value[1]).format('DD/MM/YYYY')}`;
  } else {
    return moment(date.value[0]).format('DD/MM/YYYY');
  }
});

const options = computed(() => [
  {name: '+1 Day', value: 1},
  {name: '+3 Days', value: 3},
  {name: '+5 Days', value: 5},
  {name: '+7 Days', value: 7},
]);

//Add days to a Date
function addDays(date, days) {
  const result = new Date(date);
  result.setDate(result.getDate() + days);
  return result;
}

//Reduce days on a Date
function reduceDays(date, days) {
  const result = new Date(date);
  result.setDate(result.getDate() - days);
  return result;
}

//Update the dates accord to the specify when the SelectButton element is Selected to add or reduce the date days
function updateDates(value) {
  //Validate if the date has a value and its array
  if (date.value && date.value.length === 2) {
    //Validate if both dates in the array are not the same
    if (date.value[0] !== date.value[1]) {
      //Validate if any item is selected on the SelectedButtons
      if (value) {
        //If selected, reduce the dates
        if (lastDate.value !== 0){
          date.value[1] = reduceDays(date.value[1], lastDate.value)
        }
        //Change the lastDate control Value
        lastDate.value = value.value;
        //Add the days to the correct date first selected by the user
        date.value[1] = addDays(date.value[1] ? date.value[1] : date.value[0], value.value);
      } else {
        //This when a item is deselected to reduce the days previously added
        date.value[1] = reduceDays(date.value[1], lastDate.value);
        lastDate.value = 0;
      }
    } else {
      //If the dates are the same and item is Selected
      if (value) {
        //Put the days to add into lastDate to control the correct first selected date
        lastDate.value = value.value;
        //Add Days to the Date
        date.value[1] = addDays(date.value[0], value.value);
      } else {
        //Reduce the days according first selected date
        date.value[0] = reduceDays(date.value[0], lastDate.value);
        //Re-intialize lastDate to 0
        lastDate.value = 0;
      }
    }
  } else {
    //When date not array, like when its initialized
    lastDate.value = value.value;
    date.value = [date.value, addDays(date.value, value.value)];
  }
}

function calendarChange(calendar){
  if (value.value){
    value.value = null
  }
}

</script>
<template>
  <h1 class="text-5xl text-center">Calendar</h1>
  <div class="card flex justify-center items-center">
    <div class="flex flex-col items-center px-6 pt-4 pb-2">
      <h1 class="text-2xl mb-2 text-center">Select a date range</h1>
      <InputText class="mb-2 w-full text-center" v-model="dateFormatted"/>
      <SelectButton ref="selectButton" class="mb-2 w-full" v-model="value" :options="options" optionLabel="name"
                    @update:modelValue="updateDates"/>
      <Calendar v-model="date" selectionMode="range" inline dateFormat="dd/mm/yy" @update:modelValue="calendarChange" />
    </div>
  </div>
</template>