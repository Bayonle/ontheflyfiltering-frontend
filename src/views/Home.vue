<template>
  <div class="w-full h-screen px-40 text-left">
    <div class="">
      {{query}}
      <form action="">
        <div class="flex items-center mb-2 w-full" v-for="(queryItem, index) in query" :key="index">
          <div class="flex  flex-wrap -mx-3 mb-2 w-2/3">
            <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
              <label
                class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
                for="grid-state"
              >
                Where
              </label>
              <div class="relative">
                <select
                  class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-3 px-4 pr-8 rounded leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
                  id="grid-state"
                  v-model="query[index].selectedColumn"
                  @change="resetState($event, index)"
                  :name="`query${index}.selectedColumn`"
                >
                  <option>Select column</option>
                  <option v-for="(column, index) in columns" :key="index" :value="column.name">{{column.label}}</option>
                </select>
                <div
                  class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700"
                >
                  <svg
                    class="fill-current h-4 w-4"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 20 20"
                  >
                    <path
                      d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                    />
                  </svg>
                </div>
              </div>
            </div>
            <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
              <label
                class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
                for="grid-state"
              >
              comparator
              </label>
              <div class="relative">
                <select
                  class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-3 px-4 pr-8 rounded leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
                  id="grid-state"
                  v-model="query[index].operator"
                  :name="`query${index}.selectedColumn`"
                >
                  <option v-for="(option, index) in getComparisonOptions(index)" :value="option.operator" :key="index">{{option.text}}</option>
                </select>
                <div
                  class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700"
                >
                  <svg
                    class="fill-current h-4 w-4"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 20 20"
                  >
                    <path
                      d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                    />
                  </svg>
                </div>
              </div>
            </div>
            <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
              <label
                class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2"
                for="grid-zip"
              >
                value
              </label>
              <!-- <input
                class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
                id="grid-zip"
                type="text"
                placeholder="90210"
              /> -->
              <div class="flex items-center">
                <div class="mr-2">
                  <component :is="getValueComponent(index)[0]" v-model="query[index].assertValue1" :name="`query${index}.assertValue1`"/>
                <!-- <datepicker
                  v-model="selected"
                  :locale="locale"
                  :upperLimit="to"
                  :lowerLimit="from"
                /> -->
                </div>
                <div>
                  <component :is="getValueComponent(index)[1]" v-model="query[index].assertValue2" :name="`query${index}.assertValue2`"/>
                </div>
              </div>
              <!-- <single-text-field v-model="test" placeholder="value" v-if="{"sw", }.includes(query[0].operator)" /> -->
            </div>
          </div>
          <div class="mt-2">
            <button type="button" class="px-2 py-1 mr-1 border" @click="addRow">Add filter</button>
            <button type="button" class="px-2 py-1 border" @click="removeRow(index)">delete</button>
          </div>
        </div>
        <div>
          <button class="px-4 py-2 bg-indigo-700 text-white text-base" type="button" @click="submit">Apply filter</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-debugger */
/* eslint-disable vue/no-unused-components */
/* eslint-disable no-unused-vars */
import { ref, computed } from 'vue';
import DatePicker from 'vue3-datepicker'

import SingleTextField from "@/components/SingleTextField"
// @ is an alias to /src

export default {
  name: "Home",
  components: {
    SingleTextField, DatePicker
  },
  setup({props}){
    let columns = [
      {name: "firstName", label: "Firstname", dataType: "text" },
      {name: "lastName", label: "Lastname", dataType: "text" },
      {name: "age", label: "Age", dataType: "number" },
      {name: "dateOfBirth", label: "Date of Birth", dataType: "date" }
    ];

    const query = ref([
      {selectedColumn: "", operator: "", assertValue1: "", assertValue2: ""}
    ]);


    const dataTypes = {
      text: {
        id: 1,
        options: [
          {operator : "EQUALS", text: "Equals", operatorCode: "==", valueComponent:[SingleTextField]},
          {operator : "STARTS_WITH", text: "Starts With", operatorCode: "", valueComponent: [SingleTextField]},
          {operator : "ENDS_WITH", text: "Ends With", operatorCode: "", valueComponent: [SingleTextField]},
          {operator : "CONTAINS", text: "Contains", operatorCode: "", valueComponent: [SingleTextField]},
          {operator : "EMPTY", text: "Empty", operatorCode: "", valueComponent: []},
          {operator : "NOT_EMPTY", text: "Not Empty", operatorCode: "", valueComponent: []},
        ]
      },
      date: {
        id: 2,
        options: [
          {operator : "ON", text: "On", operatorCode: "==", valueComponent: [SingleTextField]},
          {operator : "TODAY", text: "Today", operatorCode: "", valueComponent: [SingleTextField]},
          {operator : "BETWEEN", text: "Between", operatorCode: "", valueComponent: [SingleTextField, SingleTextField]},
          {operator : "EMPTY", text: "Empty", operatorCode: "", valueComponent: []},
          {operator : "NOT_EMPTY", text: "Not Empty", operatorCode: "", valueComponent: []},
        ]
      },
      selection: {
        id: 3,
        options: [
          {operator : "IN", text: "In", operatorCode: "==", valueComponent: [SingleTextField]},
          {operator : "EQUALS", text: "Equals", operatorCode: "==", valueComponent: [SingleTextField]},
          {operator : "EMPTY", text: "Empty", operatorCode: "", valueComponent: []},
          {operator : "NOT_EMPTY", text: "Not Empty", operatorCode: "", valueComponent: []},
        ]
      },
      number: {
        id: 4,
        options: [
          {operator : "GREATER_THAN", text: "More Than", operatorCode: "==", valueComponent: [SingleTextField]},
          {operator : "LESS_THAN", text: "Less Than", operatorCode: "==", valueComponent: [SingleTextField]},
          {operator : "BETWEEN", text: "Between", operatorCode: "==", valueComponent: [SingleTextField, SingleTextField]},
          {operator : "EQUALS", text: "Equals", operatorCode: "==", valueComponent: [SingleTextField]},
          {operator : "EMPTY", text: "Empty", operatorCode: "", valueComponent: []},
          {operator : "NOT_EMPTY", text: "Not Empty", operatorCode: "", valueComponent: []},
        ]
      }
    }



    const getComparisonOptions = (index) => {
      if(query.value[index].selectedColumn){
        const columnSelected = columns.find(column => column.name === query.value[index].selectedColumn);
        let options = dataTypes[columnSelected.dataType].options;
        return options;
      }
      return []
    }

    const getValueComponent = (index) => {
      let {selectedColumn, operator} = query.value[index];
      if(operator){
        const columnSelected = columns.find(column => column.name === selectedColumn);
        let component = dataTypes[columnSelected.dataType].options.find(option => option.operator === operator).valueComponent;
        return component;
      }
      return []
    }

    const addRow = () => {
      query.value.push({selectedColumn: "", operator: "", assertValue1: "", assertValue2: ""})
    }
    const removeRow = (index) => {
      query.value.splice(index, 1)
    }
    const submit = () => {
      console.log(query.value)
    }

    const resetState = (event, index) => {
      debugger
      let selected = query.value[index].selectedColumn;
      var found = query.value.filter(item => item.selectedColumn === selected);
      if(found.length > 1){
        query.value[index].selectedColumn = ''
      }
      else query.value[index].operator = ''
    }

    return {columns, query, getComparisonOptions, getValueComponent, addRow, removeRow, submit, resetState}
  }
};
</script>

<style scoped>
@import "https://unpkg.com/tailwindcss@^1.0/dist/tailwind.min.css";
</style>