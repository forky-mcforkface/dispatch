<template>
  <ValidationObserver v-slot="{ invalid, validated }">
    <v-navigation-drawer app clipped right width="800">
      <template v-slot:prepend>
        <v-list-item two-line>
          <v-list-item-content>
            <v-list-item-title class="title">
              {{ name }}
            </v-list-item-title>
            <v-list-item-subtitle>
              Reported - {{ reported_at | formatRelativeDate }}
            </v-list-item-subtitle>
          </v-list-item-content>
          <v-spacer />
          <v-btn
            icon
            color="info"
            :loading="loading"
            :disabled="invalid || !validated"
            @click="save()"
          >
            <v-icon>save</v-icon>
          </v-btn>
          <v-btn icon color="secondary" @click="closeEditSheet">
            <v-icon>close</v-icon>
          </v-btn>
        </v-list-item>
      </template>
      <v-tabs color="primary" fixed-tabs v-model="tab">
        <v-tab key="details"> Details </v-tab>
        <v-tab key="resources"> Resources </v-tab>
        <v-tab key="timeline"> Timeline </v-tab>
      </v-tabs>
      <v-tabs-items v-model="tab">
        <v-tab-item key="details">
          <case-details-tab />
        </v-tab-item>
        <v-tab-item key="resources">
          <case-resources-tab />
        </v-tab-item>
        <v-tab-item key="timeline">
          <case-timeline-tab />
        </v-tab-item>
      </v-tabs-items>
    </v-navigation-drawer>
  </ValidationObserver>
</template>

<script>
import { mapFields } from "vuex-map-fields"
import { mapActions } from "vuex"
import { ValidationObserver } from "vee-validate"

import CaseDetailsTab from "@/case/DetailsTab.vue"
import CaseResourcesTab from "@/case/ResourcesTab.vue"
import CaseTimelineTab from "@/case/TimelineTab.vue"

export default {
  name: "CaseEditSheet",

  components: {
    CaseDetailsTab,
    CaseResourcesTab,
    CaseTimelineTab,
    ValidationObserver,
  },

  data() {
    return {
      tab: null,
    }
  },

  computed: {
    ...mapFields("case_management", [
      "selected.id",
      "selected.name",
      "selected.project",
      "selected.reported_at",
      "selected.loading",
      "dialogs.showEditSheet",
    ]),
  },

  created() {
    this.fetchDetails()
  },

  watch: {
    "$route.params.name": function () {
      this.fetchDetails()
    },
  },

  methods: {
    fetchDetails() {
      this.getDetails({ name: this.$route.params.name })
    },
    ...mapActions("case_management", ["save", "getDetails", "closeEditSheet"]),
  },
}
</script>
