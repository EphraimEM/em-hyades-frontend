<template>
  <b-card no-body :header="header">
    <b-card-body>
      <c-switch
        id="enabled"
        color="primary"
        v-model="enabled"
        label
        v-bind="labelIcon"
      />{{ $t('admin.integration_gitlab_enable') }}
    </b-card-body>
    <b-card-footer>
      <b-button variant="outline-primary" class="px-4" @click="saveChanges">{{
        $t('message.update')
      }}</b-button>
    </b-card-footer>
  </b-card>
</template>

<script>
import { Switch as cSwitch } from '@coreui/vue';
// eslint-disable-next-line no-unused-vars
import axios from 'axios'; // Import axios
import common from '../../../shared/common';
import configPropertyMixin from '../mixins/configPropertyMixin';

export default {
  mixins: [configPropertyMixin],
  props: {
    header: String,
  },
  components: {
    cSwitch,
  },
  data() {
    return {
      enabled: false,
    };
  },
  methods: {
    saveChanges: function () {
      try {
        this.updateConfigProperties([
          {
            groupName: 'integrations',
            propertyName: 'gitlab_integration.enabled',
            propertyValue: this.enabled,
          },
        ]);
      } catch (error) {
        console.error('Error updating configuration properties:', error);
      }
    },
  },
  created() {
    this.axios
      .get(this.configUrl)
      .then((response) => {
        let configItems = response.data.filter(function (item) {
          return item.groupName === 'integrations';
        });
        for (let i = 0; i < configItems.length; i++) {
          let item = configItems[i];
          switch (item.propertyName) {
            case 'gitlab_integration.enabled':
              this.enabled = common.toBoolean(item.propertyValue);
              break;
          }
        }
      })
      .catch((error) => {
        console.error('Error fetching configuration data:', error);
      });
  },
};
</script>
