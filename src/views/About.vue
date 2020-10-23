<template>
  <div class="about h-screen overflow-y-scroll px-40 text-left">
    <h1 class="text-lg font-bold my-3">Students</h1>
    <!-- {{studentFields}} -->
    <list-filter :fields="studentFields" @filter-applied="applyFilter" class="my-5" />
    <table class="table-auto w-full">
  <thead>
    <tr>
      <th class="px-4 py-2">Firstname</th>
      <th class="px-4 py-2">Lastname</th>
      <th class="px-4 py-2 text-right">Age</th>
      <th class="px-4 py-2 text-right">Balance</th>
      <th class="px-4 py-2">Date of Birth</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="(student, index) in students" :key="index">
      <td class="border px-4 py-2">{{student.firstName}}</td>
      <td class="border px-4 py-2">{{student.lastName}}</td>
      <td class="border px-4 py-2 text-right">{{student.age}}</td>
      <td class="border px-4 py-2 text-right">{{parseFloat(student.balance).toFixed(2).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}}</td>
      <td class="border px-4 py-2">{{student.dateOfBirth}}</td>
    </tr>
  </tbody>
</table>
  </div>
</template>
<script>
import { ref, onMounted } from 'vue'
import ListFilter from '@/components/ListFilter'
export default {
  components: {
    ListFilter
  },
  setup(){

    const students = ref([]);
    const listFilters = ref([])
    const studentFields = ref([])

    onMounted(() => {
      getColumns();
      getStudents();
      console.log('Component is mounted!')
    })



    function getColumns(){
      fetch("http://localhost:5000/api/students/fields")
            .then(response => response.json())
            .then(data => studentFields.value = data);
    }

    function getStudents(){
      fetch("http://localhost:5000/api/students", {
        method: 'POST', // *GET, POST, PUT, DELETE, etc.
        headers: {
          'Content-Type': 'application/json'
          // 'Content-Type': 'applicat ion/x-www-form-urlencoded',
        },
        body: JSON.stringify({filters: listFilters.value}) // body data type must match "Content-Type" header
      })
      .then(response => response.json())
      .then(data => students.value = data);
    }

    const applyFilter = (query) => {
      listFilters.value = query;
      getStudents()
    }

    return {students, listFilters, studentFields, applyFilter}
  }
}
</script>
