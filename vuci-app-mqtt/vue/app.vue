<template>
<vuci-form uci-config="mosquitto" :key="tableRefresh">
  <MQTTBroker @refreshTable="refreshTable"></MQTTBroker>
  <a-card :title="'Broker Settings'">
    <a-tabs>
      <a-tab-pane key="security" :tab="'Security'">
        <MQTTBrokerSettings @refreshTable="refreshTable"></MQTTBrokerSettings>
      </a-tab-pane>
      <a-tab-pane key="miscellaneous" :tab="' Miscellaneous'">
        <MQTTBrokerMiscellaneous @refreshTable="refreshTable"></MQTTBrokerMiscellaneous>
      </a-tab-pane>
    </a-tabs>
  </a-card>
</vuci-form>
</template>
<script>
import MQTTBroker from './components/MQTTBroker.vue'
import MQTTBrokerSettings from './components/MQTTBrokerSettings.vue'
import MQTTBrokerMiscellaneous from './components/MQTTBrokerMiscellaneous.vue'
export default {
  components: {
    MQTTBroker,
    MQTTBrokerSettings,
    MQTTBrokerMiscellaneous
  },
  data () {
    return {
      tableRefresh: 0
    }
  },
  methods: {
    refreshTable () {
      this.tableRefresh += 1
    }
  }
}
</script>
