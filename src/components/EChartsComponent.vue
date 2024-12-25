<template>
  <div>
    <h2>Biểu đồ dữ liệu lỗi</h2>
    <div id="stationChart" style="width: 100%; height: 400px;"></div>
    <div id="errorCodeChart" style="width: 100%; height: 400px;"></div>
    <div id="rootCauseChart" style="width: 100%; height: 400px;"></div>
  </div>
</template>

<script>
import * as echarts from "echarts";

export default {
  data() {
    return {
      socket: null, // Kết nối WebSocket
    };
  },
  methods: {
    connectWebSocket() {
      // Tạo kết nối WebSocket
      this.socket = new WebSocket("ws://localhost:5000/ws/failure-records");

      this.socket.onopen = () => {
        console.log("WebSocket connected");
      };

      // Xử lý dữ liệu khi nhận được từ server
      this.socket.onmessage = (event) => {
        const data = JSON.parse(event.data); // Parse dữ liệu JSON từ server
        console.log("Received data:", data);

        // Vẽ biểu đồ dựa trên dữ liệu nhận được
        this.renderStationChart(data.station_count);
        this.renderErrorCodeChart(data.error_code_count);
        this.renderRootCauseChart(data.root_cause_count);
      };

      this.socket.onerror = (error) => {
        console.error("WebSocket error:", error);
      };

      this.socket.onclose = () => {
        console.log("WebSocket disconnected");
      };
    },
    renderStationChart(data) {
      console.log("Data for station chart:", data);
      const stationChart = echarts.init(document.getElementById("stationChart"));
      stationChart.setOption({
        title: {
          text: "Số lượng lỗi theo công đoạn",
        },
        tooltip: {},
        xAxis: {
          type: "category",
          data: Object.keys(data),
          axisLabel: {
          interval: 0, // Hiển thị tất cả các nhãn (không bỏ qua nhãn nào)
          rotate: 45, // Xoay nhãn 45 độ để tránh chồng chéo (hoặc 90 nếu cần)
          formatter: (value) => value, // Đảm bảo giá trị là chuỗi
        },
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            data: Object.values(data),
            type: "bar",
          },
        ],
      });
    },
    renderErrorCodeChart(data) {
      const errorCodeChart = echarts.init(document.getElementById("errorCodeChart"));
      errorCodeChart.setOption({
        title: {
          text: "Tỉ lệ các loại lỗi",
        },
        tooltip: {},
        series: [
          {
            type: "pie",
            data: Object.entries(data).map(([key, value]) => ({
              name: key,
              value: value,
            })),
          },
        ],
      });
    },
    renderRootCauseChart(data) {
      const rootCauseChart = echarts.init(document.getElementById("rootCauseChart"));
      rootCauseChart.setOption({
        title: {
          text: "Tỉ lệ nguyên nhân lỗi",
        },
        tooltip: {},
        series: [
          {
            type: "pie",
            data: Object.entries(data).map(([key, value]) => ({
              name: key,
              value: value,
            })),
          },
        ],
      });
    },
  },
  mounted() {
    // Kết nối WebSocket khi component được mount
    this.connectWebSocket();
  },
  beforeUnmount() {
    // Đóng kết nối WebSocket khi component bị hủy
    if (this.socket) {
      this.socket.close();
    }
  },
};
</script>
