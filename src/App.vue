<template>
  <div id="viewer-host">
    <JSViewer ref="reportViewer"></JSViewer>
  </div>
</template>

<script lang="ts">
import { ref, onMounted } from "vue";
import { Viewer } from "@mescius/activereportsjs-vue";

export default {
  name: "App",
  components: {
    JSViewer: Viewer,
  },
  setup() {
    const reportViewer = ref(null);
    const loadData = async () => {
      const headers = new Headers();
      const dataRequest = new Request(
        "https://demodata.mescius.io/northwind/api/v1/Customers",
        {
          headers: headers,
        }
      );
      const response = await fetch(dataRequest);
      const data = await response.json();
      return data;
    }
    const loadReport = async () => {
      const reportResponse = await fetch("/CustomersTable.rdlx-json");
      const report = await reportResponse.json();
      return report;
    };
    onMounted(async () => {
      const data = await loadData();
      const report = await loadReport();
      report.DataSources[0].ConnectionProperties.ConnectString = "jsondata=" + JSON.stringify(data);
      reportViewer.value.Viewer().open(report);
    });
    return { reportViewer };
  },
};
</script>

<style src="../node_modules/@mescius/activereportsjs/styles/ar-js-ui.css"></style>
<style src="../node_modules/@mescius/activereportsjs/styles/ar-js-viewer.css"></style>

<style>
body {
  margin: 0;
}
#viewer-host {
  width: 100%;
  height: 100vh;
}
</style>