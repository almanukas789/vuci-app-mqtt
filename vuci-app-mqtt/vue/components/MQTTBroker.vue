<template>
  <div>
    <a-card :title="'MQTT'" hoverable>
      <vuci-named-section :name="'mqtt'" v-slot="{ s }" :card="false">
        <vuci-form-item-switch :uci-section="s" :label="'Enable: '" name="enabled"/>
        <vuci-form-item-input :uci-section="s" :label="'Local port: '" :initial="'1883'" name="local_port" required/>
        <vuci-form-item-switch :uci-section="s" :label="'Enable remote access: '" name="enable_ra"/>
      </vuci-named-section>
    </a-card>
  </div>
</template>
<script>
export default {
  data () {
    return {
    }
  }
}
</script>
