<template>
  <div id="app">
    <input
      class="form-control"
      type="file"
      id="input"
      @change="excelFile($event)"
      accept=".xls,.xlsx"
    />
    <button class="btn btn-primary" @click="cleare" id="button">Cleare</button>
    <div id="result">
      <table class="table table-bordered">
        <thead>
          <tr>
            <th v-for="(header, j) in tableHeader" :key="j">
              {{ header }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in tableElements" :key="index">
            <td v-for="(key, i) in tableHeader" :key="i">
              <!-- {{ item[key] }}-----{{ item[key] == null }} -->
              <!-- :class="[item[key] < 0 ? 'errorClass' : '']" -->
              <div :class="[valiedData(item[key]) ? 'errorClass' : '']">
                {{ item[key] === undefined ? "undefined" : item[key] }}
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { read, utils, writeFile } from "xlsx";
export default {
  name: "App",
  data() {
    return {
      tableElements: [],
      tableHeader: [],
    };
  },
  methods: {
    // method for convert excel to json
    excelFile(e) {
      const files = e.target.files;
      const f = files[0];
      const reader = new FileReader();
      reader.onload = (e) => {
        const data = e.target.result;
        const workbook = read(data, { type: "binary" });
        console.log(workbook);
        const worksheet = workbook.Sheets[workbook.SheetNames[0]];
        let tableData = utils.sheet_to_json(worksheet);
        this.tableElements = [...tableData];
        // store object key name in Array from data
        const keys = Object.keys(tableData[0]);
        this.tableHeader = [...keys];
        console.log(this.tableHeader);
        console.log(this.tableElements);
      };
      reader.readAsBinaryString(f);
    },
    // cleare input file method
    cleare() {
      document.getElementById("input").value = "";
      this.tableElements = [];
      this.tableHeader = [];
    },
    // method for check data valied or not if data is null or undefined or < 0 return true else return false
    valiedData(data) {
      if (data == null || data == undefined || data < 0) {
        console.log("data is not valied ===>", data);
        return true;
      } else {
        return false;
      }
    },
  },
};
</script>

<style>
body {
  background-color: #474141;
  color: #f5f5f5;
}
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  width: 100%;
  border-collapse: collapse;
  border-spacing: 0;

  border: 1px solid #ddd;
}

td,
th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #dddddd;
}
.errorClass {
  color: rgb(255, 255, 255);
  font-weight: 800;
  height: 100%;
  width: 100%;
  background-color: #ff0000;
}
</style>
