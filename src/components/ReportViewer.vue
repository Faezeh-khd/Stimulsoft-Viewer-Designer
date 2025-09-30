<template>
  <div>
    <h2>Stimulsoft Viewer + Designer</h2>

    <!-- Toggle buttons -->
    <div style="margin-bottom: 10px">
      <button @click="showViewer">Show Viewer</button>
      <button @click="showDesigner">Edit in Designer</button>
    </div>

    <!-- Containers -->
    <div
      v-show="mode === 'viewer'"
      id="viewer"
      style="width: 100%; height: 80vh"
    ></div>
    <div
      v-show="mode === 'designer'"
      id="designer"
      style="width: 100%; height: 80vh"
    ></div>
  </div>
</template>

<script>
export default {
  name: "ReportEditor",
  data() {
    return {
      report: null,
      viewer: null,
      designer: null,
      mode: "viewer", // viewer | designer
    };
  },
  mounted() {
    // Create report
    this.report = new window.Stimulsoft.Report.StiReport();
    this.report.loadFile("reports/SimpleList.mrt");
    // Init Viewer
    this.viewer = new window.Stimulsoft.Viewer.StiViewer(
      null,
      "StiViewer",
      false
    );
    this.viewer.report = this.report;
    this.viewer.renderHtml("viewer");

    // Init Designer
    const options = new window.Stimulsoft.Designer.StiDesignerOptions();
    this.designer = new window.Stimulsoft.Designer.StiDesigner(
      options,
      "StiDesigner",
      false
    );
    this.designer.report = this.report;

    // Handle "Save" in Designer
    this.designer.onSaveReport = (e) => {
      // Get modified report as JSON string
      const jsonStr = e.report.saveToJsonString();

      // Reload into same report object
      this.report.load(jsonStr);

      // Refresh viewer (if user switches back)
      this.viewer.report = this.report;

      alert("Report saved back to Viewer!");
    };

    this.designer.renderHtml("designer");

    
  },
  methods: {
    showViewer() {
      this.mode = "viewer";
      // Re-render Viewer with latest report
      this.viewer.report = this.report;
    },
    showDesigner() {
      this.mode = "designer";
    },
  },
};
</script>
