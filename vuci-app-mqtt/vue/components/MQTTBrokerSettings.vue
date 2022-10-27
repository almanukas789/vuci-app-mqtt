<template>
  <a-card>
    <vuci-named-section :name="'mqtt'" v-slot="{ s }" :card="false">
      <vuci-form-item-switch  :uci-section="s" :label="'Use TLS/SSL: '" name="use_tls_ssl" @change='handleToggle'/>
      <vuci-form-item-select @change='handleTlsTypeChange' depend="use_tls_ssl == 1" :uci-section="s" :label="'TLS Type: '" name="tls_type" :options="tlstype" required/>
      <vuci-form-item depend="use_tls_ssl == 1 && tls_type == 'cert'" :uci-section="s" :label="'CA file: '" name="ca_file">
        <div class="flex-container">
          <vuci-form-item-dummy :uci-section="s" :label="'Uploaded file:'" name="ca_file"></vuci-form-item-dummy>
            <div>
              <a-button v-if="s.ca_file === ca_file_path" type="danger" @click="handleDelete(ca_file_path, 'ca_file')" v-text="'Delete'" class="delete"></a-button>
              <a-upload action="/upload" @change="handleUploadCa" :data="{path: ca_file_path}">
                <div>
                  <a-button type="primary" icon="upload" ghost>Select File</a-button>
                </div>
              </a-upload>
            </div>
        </div>
        </vuci-form-item>
        <vuci-form-item depend="use_tls_ssl == 1 && tls_type == 'cert'" :uci-section="s" :label="'Cert file: '" name="cert_file">
          <div class="flex-container">
            <vuci-form-item-dummy :uci-section="s" :label="'Uploaded file:'" name="cert_file"></vuci-form-item-dummy>
              <div>
                <a-button v-if="s.cert_file === cert_file_path" type="danger" @click="handleDelete(cert_file_path, 'cert_file')" v-text="'Delete'"  class="delete"></a-button>
                <a-upload action="/upload" @change="handleUploadCert" :data="{path: cert_file_path}">
                  <div>
                    <a-button type="primary" icon="upload" ghost>Select File</a-button>
                  </div>
                </a-upload>
              </div>
          </div>
        </vuci-form-item>
        <vuci-form-item depend="use_tls_ssl == 1 && tls_type == 'cert'" :uci-section="s" :label="'Key file: '" name="key_file">
          <div class="flex-container">
            <vuci-form-item-dummy :uci-section="s" :label="'Uploaded file:'" name="key_file"></vuci-form-item-dummy>
              <div>
                <a-button v-if="s.key_file === key_file_path" type="danger" @click="handleDelete(key_file_path, 'key_file')" v-text="'Delete'" class="delete"></a-button>
                <a-upload action="/upload" @change="handleUploadKey" :data="{path: key_file_path}">
                  <div>
                    <a-button type="primary" icon="upload" ghost>Select File</a-button>
                  </div>
                 </a-upload>
              </div>
          </div>
        </vuci-form-item>
        <vuci-form-item-select depend="use_tls_ssl == 1 && tls_type == 'cert'" :uci-section="s" :label="'TLS version: '" name="tls_version" :options="tlsversions" required/>
        <vuci-form-item-input depend="use_tls_ssl == 1 && tls_type == 'psk'" :uci-section="s" :label="'Pre-Shared-Key: '" name="psk"/>
        <vuci-form-item-input depend="use_tls_ssl == 1 && tls_type == 'psk'" :uci-section="s" :label="'Identity: '" name="identity"/>
    </vuci-named-section>
  </a-card>
</template>
<script>
export default {
  data () {
    return {
      key_file_path: '/etc/vuci-uploads/key_file.key',
      cert_file_path: '/etc/vuci-uploads/cert_file.pem',
      ca_file_path: '/etc/vuci-uploads/ca_file.pem'
      headers: {
        authorization: 'authorization'
      },
      tlsversions: [
        ['all', 'Support all'],
        ['tlsv1', 'TLSV1'],
        ['tlsv1.1', 'TLSV1.1'],
        ['tlsv1.2', 'TLSV1.2']
      ],
      tlstype: [
        ['cert', 'Certificate based'],
        ['psk', 'Shared-Key base']
      ]
    }
  },
  methods: {
    async handleTlsTypeChange (self) {
      if (self.model === 'psk') {
        this.$uci.reset()
        await this.$uci.set('mosquitto', 'mqtt', 'ca_file', undefined)
        await this.$uci.set('mosquitto', 'mqtt', 'key_file', undefined)
        await this.$uci.set('mosquitto', 'mqtt', 'cert_file', undefined)
        await this.$uci.set('mosquitto', 'mqtt', 'tls_version', undefined)
        await this.randomFixForSaveAndApply()
      } else if (self.model === 'cert') {
        this.$uci.reset()
        await this.$uci.set('mosquitto', 'mqtt', 'psk', undefined)
        await this.$uci.set('mosquitto', 'mqtt', 'identity', undefined)
        await this.randomFixForSaveAndApply()
      }
    },
    randomFixForSaveAndApply () {
    //  random fix
      this.$uci.set('mosquitto', 'mqtt', 'random', undefined)
      this.$uci.set('mosquitto', 'mqtt', 'random2', undefined)
      this.$uci.set('mosquitto', 'mqtt', 'random3', undefined)
      this.$uci.set('mosquitto', 'mqtt', 'random4', undefined)
      this.$uci.set('mosquitto', 'mqtt', 'random5', undefined)
      this.$uci.set('mosquitto', 'mqtt', 'random6', undefined)
    },
    handleToggle (self) {
      if (self.model === false) {
        this.$uci.set('mosquitto', 'mqtt', 'ca_file', undefined)
        this.$uci.set('mosquitto', 'mqtt', 'key_file', undefined)
        this.$uci.set('mosquitto', 'mqtt', 'cert_file', undefined)
        this.$uci.set('mosquitto', 'mqtt', 'tls_type', undefined)
        this.$uci.set('mosquitto', 'mqtt', 'psk', undefined)
        this.$uci.set('mosquitto', 'mqtt', 'tls_type', undefined)
        this.$uci.set('mosquitto', 'mqtt', 'identity', undefined)
        this.$uci.set('mosquitto', 'mqtt', 'tls_version', undefined)
        //  random fix
        this.$uci.set('mosquitto', 'mqtt', 'random', undefined)
      } else {
        this.$uci.reset()
      }
    },
    async handleDelete (path, optionName) {
      await this.$uci.set('mosquitto', 'mqtt', optionName, undefined)
      await this.$uci.save()
      await this.$uci.apply()
      await this.removeFile(path)
      this.refreshTable()
      this.$message.success('Removed file.')
    },
    removeFile (filepath) {
      return this.$rpc.call('file', 'remove', { path: filepath })
    },
    async validate (path, file, status, optionName) {
      await this.$uci.load('mosquitto')
      if (status === 'uploading') return
      if (status !== 'done') {
        this.$message.error(`upload '${file.name}' failed.`)
      } else if (status === 'done') {
        this.$message.success('File added!')
        await this.$uci.set('mosquitto', 'mqtt', optionName, path)
      }
    },
    handleUploadCa (info) {
      const file = info.file
      const status = file.status
      this.validate(this.ca_file_path, file, status, 'ca_file')
    },
    handleUploadCert (info) {
      const file = info.file
      const status = file.status
      this.validate(this.cert_file_path, file, status, 'cert_file')
    },
    handleUploadKey (info) {
      const file = info.file
      const status = file.status
      this.validate(this.key_file_path, file, status, 'key_file')
    },
    refreshTable () {
      this.$emit('refreshTable')
    }
  }
}
</script>
<style scoped>
.flex-container {
  display: flex;
  flex-wrap: nowrap;
}
.flex-container > div {
  width: 100%;
  text-align: center;
}

</style>
