<template>
  <a-card>
    <vuci-named-section :name="'mqtt'" v-slot="{ s }" :card="false">
      <vuci-form-item :uci-section="s" :label="'ACL file: '" name="acl_file_path">
        <div class="flex-container">
          <vuci-form-item-dummy :uci-section="s" :label="'Uploaded file:'" name="acl_file_path"></vuci-form-item-dummy>
            <div>
              <a-button v-if="s.acl_file_path === acl_file_path" type="danger" @click="handleDelete(acl_file_path, 'acl_file_path')" v-text="'Delete'" class="delete"></a-button>
              <a-upload action="/upload" @change="handleUploadAcl" :data="{path: acl_file_path}">
                <div>
                  <a-button type="primary" icon="upload" ghost>Select File</a-button>
                </div>
              </a-upload>
            </div>
        </div>
      </vuci-form-item>
      <vuci-form-item :uci-section="s" :label="'Password file: '" name="password_file">
        <div class="flex-container">
          <vuci-form-item-dummy :uci-section="s" :label="'Uploaded file:'" name="password_file"></vuci-form-item-dummy>
            <div>
              <a-button v-if="s.password_file === password_file_path" type="danger" @click="handleDelete(password_file_path, 'password_file')" v-text="'Delete'" class="delete"></a-button>
              <a-upload action="/upload" @change="handleUploadPassword" :data="{path: password_file_path}">
                <div>
                  <a-button type="primary" icon="upload" ghost>Select File</a-button>
                </div>
              </a-upload>
            </div>
        </div>
      </vuci-form-item>
      <vuci-form-item-switch :uci-section="s" :label="'Persistence: '" name="persistence"/>
      <vuci-form-item-switch :uci-section="s" :label="'Allow Anonymous: '" name="anonymous_access"/>
    </vuci-named-section>
  </a-card>
</template>
<script>
export default {
  data () {
    return {
      acl_file_path: '/etc/vuci-uploads/acl_file.pem',
      password_file_path: '/etc/vuci-uploads/password_file.pem',
    }
  },
  methods: {
    refreshTable () {
      this.$emit('refreshTable')
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
    handleUploadAcl (info) {
      const file = info.file
      const status = file.status
      this.validate(this.acl_file_path, file, status, 'acl_file_path')
    },
    handleUploadPassword (info) {
      const file = info.file
      const status = file.status
      this.validate(this.password_file_path, file, status, 'password_file')
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
