<template>
  <div class="w-full text-left">
    <div class="">
      <!-- {{query}}
      {{columns}} -->
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
                  v-model="query[index].selection"
                  @change="resetState($event, index)"
                  :name="`query${index}.selection`"
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
                  v-model="query[index].element"
                  :name="`query${index}.selection`"
                >
                  <option v-for="(option, index) in getComparisonOptions(index)" :value="option.element" :key="index">{{option.text}}</option>
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
                  <component :is="getValueComponent(index)[0]" v-model="query[index].value" :name="`query${index}.value`"/>
                <!-- <datepicker
                  v-model="selected"
                  :locale="locale"
                  :upperLimit="to"
                  :lowerLimit="from"
                /> -->
                </div>
                <div>
                  <component :is="getValueComponent(index)[1]" v-model="query[index].value2" :name="`query${index}.value2`"/>
                </div>
              </div>
              <!-- <single-text-field v-model="test" placeholder="value" v-if="{"sw", }.includes(query[0].element)" /> -->
            </div>
          </div>
          <div class="mt-2">
            <button type="button" class="px-2 py-1 mr-1 border" @click="addRow">Add filter</button>
            <button type="button" class="px-2 py-1 border" @click="removeRow(index)">delete</button>
          </div>
        </div>
        <div>
          <button class="px-4 py-2 bg-indigo-700 text-white text-base" type="button" @click="submit">Apply filter</button>
          <button class="px-4 py-2 bg-gray-200 text-black text-base" type="button" @click="clearFilter">Clear filter</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-debugger */
/* eslint-disable vue/no-unused-components */
/* eslint-disable no-unused-vars */
import { ref, computed, onMounted } from 'vue';
import DatePicker from 'vue3-datepicker'

import SingleTextField from "@/components/SingleTextField"
// @ is an alias to /src

export default {
  name: "Home",
  components: {
    SingleTextField, DatePicker
  },
  props: {
    fields: {
      type: Array,
      default: () => []
    }
  },
  setup(props, context){
    // let columns = [
    //   {name: "firstName", label: "Firstname", dataType: "text" },
    //   {name: "lastName", label: "Lastname", dataType: "text" },
    //   {name: "age", label: "Age", dataType: "number" },
    //   {name: "dateOfBirth", label: "Date of Birth", dataType: "date" }
    // ];
    const columns = computed(() => props.fields);
    const query = ref([
      {selection: "", element: "", value: "", value2: ""}
    ]);


    const dataTypes = {
      text: {
        id: 1,
        options: [
          {element : "EQUALS", text: "Equals", elementCode: "==", valueComponent:[SingleTextField]},
          {element : "STARTS_WITH", text: "Starts With", elementCode: "", valueComponent: [SingleTextField]},
          {element : "ENDS_WITH", text: "Ends With", elementCode: "", valueComponent: [SingleTextField]},
          {element : "CONTAINS", text: "Contains", elementCode: "", valueComponent: [SingleTextField]},
          {element : "EMPTY", text: "Empty", elementCode: "", valueComponent: []},
          {element : "NOT_EMPTY", text: "Not Empty", elementCode: "", valueComponent: []},
        ]
      },
      date: {
        id: 2,
        options: [
          {element : "ON", text: "On", elementCode: "==", valueComponent: [SingleTextField]},
          {element : "TODAY", text: "Today", elementCode: "", valueComponent: [SingleTextField]},
          {element : "BETWEEN", text: "Between", elementCode: "", valueComponent: [SingleTextField, SingleTextField]},
          {element : "EMPTY", text: "Empty", elementCode: "", valueComponent: []},
          {element : "NOT_EMPTY", text: "Not Empty", elementCode: "", valueComponent: []},
        ]
      },
      selection: {
        id: 3,
        options: [
          {element : "IN", text: "In", elementCode: "==", valueComponent: [SingleTextField]},
          {element : "EQUALS", text: "Equals", elementCode: "==", valueComponent: [SingleTextField]},
          {element : "EMPTY", text: "Empty", elementCode: "", valueComponent: []},
          {element : "NOT_EMPTY", text: "Not Empty", elementCode: "", valueComponent: []},
        ]
      },
      number: {
        id: 4,
        options: [
          {element : "GREATER_THAN", text: "More Than", elementCode: "==", valueComponent: [SingleTextField]},
          {element : "LESS_THAN", text: "Less Than", elementCode: "==", valueComponent: [SingleTextField]},
          {element : "BETWEEN", text: "Between", elementCode: "==", valueComponent: [SingleTextField, SingleTextField]},
          {element : "EQUALS", text: "Equals", elementCode: "==", valueComponent: [SingleTextField]},
          {element : "EMPTY", text: "Empty", elementCode: "", valueComponent: []},
          {element : "NOT_EMPTY", text: "Not Empty", elementCode: "", valueComponent: []},
        ]
      }
    }



    const getComparisonOptions = (index) => {
      if(query.value[index].selection){
        const columnSelected = columns.value.find(column => column.name === query.value[index].selection);
        let options = dataTypes[columnSelected.dataType].options;
        return options;
      }
      return []
    }

    const getValueComponent = (index) => {
      let {selection, element} = query.value[index];
      if(element){
        const columnSelected = columns.value.find(column => column.name === selection);
        let component = dataTypes[columnSelected.dataType].options.find(option => option.element === element).valueComponent;
        return component;
      }
      return []
    }

    const addRow = () => {
      query.value.push({selection: "", element: "", value: "", value2: ""})
    }
    const removeRow = (index) => {
      query.value.splice(index, 1)
    }
    const submit = () => {
      context.emit("filter-applied", query.value);
    }

    const clearFilter = () => {
      query.value = [
      {selection: "", element: "", value: "", value2: ""}
    ]
      context.emit("filter-applied", []);
    }

    const resetState = (event, index) => {
      let selected = query.value[index].selection;
      var found = query.value.filter(item => item.selection === selected);
      if(found.length > 1){
        query.value[index].selection = ''
      }
      else query.value[index].element = ''
    }

    return {columns, query, getComparisonOptions, getValueComponent, addRow, removeRow, submit,clearFilter, resetState}
  }
};
</script>

<style scoped>
@import "https://unpkg.com/tailwindcss@^1.0/dist/tailwind.min.css";
</style>