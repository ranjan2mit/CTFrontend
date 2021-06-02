<template>
   <section>
      <div>
         <div class="display-inline">
            <h3>Add/Update Department</h3>
            <table>
               <tbody>
                  <tr>
                     <td>Department Name</td>
                     <td><input type="text" v-model="deptename"></td>
                  </tr>
                  <tr>
                  </tr>
               </tbody>
            </table>
            <button v-if="isupdatedept" type="button" @click="updateDept()">Update Dept</button>
            <button v-else type="button" @click="AddDept()">Add Dept</button>
         </div>
         <div class="display-inline">
            <h3>Add/Update Employee</h3>
            <table>
               <tbody>
                  <tr>
                     <td>Employee Name</td>
                     <td><input type="text" v-model="employeename"></td>
                  </tr>
                  <tr>
                     <td>Employee Department</td>
                     <td>
                        <select v-model="employeedeptid">
                           <option value="">Select</option>
                           <option v-for="row in deptlist" :value="row.DepartmentId"  v-bind:key="'option'+row.DepartmentId">{{row.DeptName || 'NA'}}</option>
                        </select>
                     </td>
                  </tr>
               </tbody>
            </table>
            <button v-if="isupdateemp" type="button" @click="updateEmp()">Update Employee</button>
            <button v-else type="button" @click="AddEmp()">Add Employee</button>
         </div>
      </div>
      <div class="display-inline">
       
         <h3> Department List</h3>
           <tr>
           <td><input type="text" v-model="filterdeptname" placeholder="Enter department name"></td>
           <td> <button @click="searchDept(1)">Search</button> </td>
         </tr>
         <table>
            <thead>
               <tr>
                  <th>Id</th>
                  <th>Department Name</th>
                  <th>Action</th>
               </tr>
            </thead>
            <tbody>
               <tr v-for="row in deptlist" v-bind:key="'dept'+row.DepartmentId">
                  <td>{{row.DepartmentId}}</td>
                  <td>{{row.DeptName || 'NA'}}</td>
                  <td><a @click="deleteDept(row)">Delete</a> <a @click="setdeptupdate(row)">Edit</a> </td>
               </tr>
            </tbody>
         </table>
      </div>
      <div class="display-inline">
         <h3> Employee List</h3>
          <tr>
           <td><input type="text" v-model="filterempname" placeholder="Enter employee name"></td>
           <td> <button @click="searchEmp(1)">Search</button> </td>
         </tr>
         <table>
            <thead>
               <tr>
                  <th>Name</th>
                  <th>Department</th>
                  <th>Action</th>
               </tr>
            </thead>
            <tbody>
               <tr v-for="row in emplist" v-bind:key="'emp'+row.EmployeeId">
                  <td>{{row.Name}}</td>
                  <td>{{row.DeptName || 'NA'}}</td>
                  <td><a @click="deleteEmp(row)">Delete</a>  <a @click="setempupdate(row)">Edit</a></td>
               </tr>
            </tbody>
         </table>
      </div>
   </section>
</template>
<script>
   import axios from "axios";
   export default {
     name: 'Opration',
     data: () => ({
       HOST_URL: 'http://localhost:8081', //docker port
       emplist: [],
       deptlist: [],
       deptename: '',
       employeename: '',
       employeedeptid: '',
       employeeid: '',
       isupdatedept: false,
       isupdateemp: false,
       updatedeptid:'',
       filterdeptname: '',
       filterempname: ''
     }),
    async created () { 
      await this.searchDept()
      await this.searchEmp()
     },
     methods: {
        searchEmp: async function (issearch) {
           try {
              let getObj = {}
              if(issearch) {
                getObj.epmloyeename = this.filterempname
              }
              let empresult = await axios.post(`${this.HOST_URL}/api/listemp`, getObj)
              empresult = empresult.data
              if(empresult.status) {
                this.emplist = empresult.data
              }
           } catch (e) {
             console.log(e)
           }
        },
        searchDept: async function (issearch) {
          try {
              let getObj = {}
              if(issearch) {
                getObj.departmentname = this.filterdeptname
              }
              let deptresult = await axios.post(`${this.HOST_URL}/api/listdept`, getObj)
              deptresult = deptresult.data
              if(deptresult.status) {
                this.deptlist = deptresult.data
                console.log(this.deptlist)
              }
           } catch (e) {
             console.log(e)
           }
        },
        deleteEmp: async function (data) {
          if(confirm("Sure you want to delete!")){
             let reqobj = {
               employeeid: data.EmployeeId
             }
             let empresult = await axios.post(`${this.HOST_URL}/api/removeemp`, reqobj)
              empresult = empresult.data
              if(empresult.status) {
                alert("Employee data deleted")
                this.searchEmp()
              }
          } 
        },
        deleteDept: async function (data) {
          if(confirm("Sure you want to delete!")){
             let reqobj = {
               departmentid: data.DepartmentId
             }
             let deptresult = await axios.post(`${this.HOST_URL}/api/removedept`, reqobj)
              deptresult = deptresult.data
              if(deptresult.status) {
                alert("Department data deleted")
                this.searchDept()
              }
          }
        },
        AddEmp: async function () {
          if(!this.employeename) {
            return alert("Please enter employee name")
          }
           if(!this.employeedeptid) {
            return alert("Please select department")
          }
         try {
           let reqobj = {
               employeename: this.employeename,
               departmentid: this.employeedeptid
             }
              let deptresult = await axios.post(`${this.HOST_URL}/api/addemp`, reqobj)
              deptresult = deptresult.data
              if(deptresult.status) {
                alert("Employee added")
                this.searchEmp()
              }
           } catch (e) {
             console.log(e)
           }
        },
        AddDept: async function () {
          if(!this.deptename) {
            return alert("Please enter department name")
          }
         try {
           let reqobj = {
               departmentname: this.deptename
             }
              let deptresult = await axios.post(`${this.HOST_URL}/api/adddept`, reqobj)
              deptresult = deptresult.data
              if(deptresult.status) {
                alert("Department added")
                this.searchDept()
              }
           } catch (e) {
             console.log(e)
           }
        },
        setdeptupdate: async function(data) {
          this.deptename = data.DeptName
          this.updatedeptid = data.DepartmentId
          this.isupdatedept = true
        },
        setempupdate: async function (data) {
           this.employeename = data.Name
           this.employeedeptid = data.DepartmentId
           this.employeeid = data.EmployeeId
           this.isupdateemp = true
        },
        updateDept: async function() {
   try {
     if(!this.deptename) {
            return alert("Please enter department name")
          }
           let reqobj = {
               departmentname: this.deptename,
               departmentid: this.updatedeptid
             }
              let deptresult = await axios.post(`${this.HOST_URL}/api/updatedept`, reqobj)
              deptresult = deptresult.data
              if(deptresult.status) {
                alert("Department update")
                this.searchDept()
              }
           } catch (e) {
             console.log(e)
           }
        },
        updateEmp: async function() {
   try {
     if(!this.employeename) {
            return alert("Please enter employee name")
          }
          if(!this.employeedeptid) {
            return alert("Please select department")
          }
           let reqobj = {
               employeename: this.employeename,
               departmentid: this.employeedeptid,
               employeeid: this.employeeid
             }
              let deptresult = await axios.post(`${this.HOST_URL}/api/updateemp`, reqobj)
              deptresult = deptresult.data
              if(deptresult.status) {
                alert("Employee update")
                this.searchEmp()
              }
           } catch (e) {
             console.log(e)
           }
        }
     }
   }
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
   h3 {
   margin: 40px 0 0;
   }
   ul {
   list-style-type: none;
   padding: 0;
   }
   li {
   display: inline-block;
   margin: 0 10px;
   }
   a {
   color: #42b983;
   }
   .display-inline {
   display: inline-block;
   margin: 4% 7%
   }
   table {
   font-family: arial, sans-serif;
   border-collapse: collapse;
   width: 100%;
   }
   td, th {
   border: 1px solid #dddddd;
   text-align: left;
   padding: 8px;
   }
   tr:nth-child(even) {
   background-color: #dddddd;
   }
</style>