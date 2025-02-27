<template>
  <FilingTemplate
    class="incorporation-application"
    :filing="filing"
    :index="index"
  >
    <template #subtitle>
      <FiledAndPendingPaid
        v-if="isFutureEffectivePending"
        class="item-header-subtitle"
        :filing="filing"
        :index="index"
      />

      <FutureEffectivePaid
        v-else-if="isFutureEffective"
        class="item-header-subtitle"
        :filing="filing"
        :index="index"
      />
    </template>

    <template #body>
      <FutureEffectivePending
        v-if="isFutureEffectivePending"
        :filing="filing"
      />

      <FutureEffective
        v-else-if="isFutureEffective"
        :filing="filing"
      />

      <div
        v-else-if="isStatusCompleted"
        class="completed-ia-details"
      >
        <h4>Incorporation Complete</h4>

        <p>
          {{ getLegalName || 'A Numbered Benefit Company' }} has been successfully incorporated.
        </p>

        <p>
          Return to My Business Registry to access your business and file changes.
        </p>

        <div class="to-dashboard-container text-center mt-6">
          <v-btn
            color="primary"
            @click.stop="returnToMyBusinessRegistry()"
          >
            <span>Return to My Business Registry</span>
          </v-btn>
        </div>
      </div>
    </template>
  </FilingTemplate>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator'
import { Getter } from 'pinia-class'
import { ApiFilingIF } from '@/interfaces'
import { DateUtilities, EnumUtilities } from '@/services'
import { navigate } from '@/utils'
import FiledAndPendingPaid from '../subtitles/FiledAndPendingPaid.vue'
import FilingTemplate from '../FilingTemplate.vue'
import FutureEffective from '../bodies/FutureEffective.vue'
import FutureEffectivePaid from '../subtitles/FutureEffectivePaid.vue'
import FutureEffectivePending from '../bodies/FutureEffectivePending.vue'
import { useBusinessStore, useConfigurationStore } from '@/stores'

@Component({
  components: {
    FiledAndPendingPaid,
    FilingTemplate,
    FutureEffective,
    FutureEffectivePaid,
    FutureEffectivePending
  }
})
export default class IncorporationApplication extends Vue {
  @Prop({ required: true }) readonly filing!: ApiFilingIF
  @Prop({ required: true }) readonly index!: number

  @Getter(useBusinessStore) getLegalName!: string
  @Getter(useConfigurationStore) getMyBusinessRegistryUrl!: string

  /** Whether this filing is in Complete status. */
  get isStatusCompleted (): boolean {
    return EnumUtilities.isStatusCompleted(this.filing)
  }

  /** Whether this filing is Future Effective Pending (overdue). */
  get isFutureEffectivePending (): boolean {
    return (
      EnumUtilities.isStatusPaid(this.filing) &&
      this.filing.isFutureEffective &&
      DateUtilities.isDatePast(this.filing.effectiveDate)
    )
  }

  /** Whether this filing is Future Effective (not yet completed). */
  get isFutureEffective (): boolean {
    return (
      EnumUtilities.isStatusPaid(this.filing) &&
      this.filing.isFutureEffective &&
      DateUtilities.isDateFuture(this.filing.effectiveDate)
    )
  }

  returnToMyBusinessRegistry (): void {
    navigate(this.getMyBusinessRegistryUrl)
  }
}
</script>

<style lang="scss" scoped>
@import "@/assets/styles/theme.scss";

.item-header-subtitle {
  color: $gray7;
  margin-top: 0.5rem;
}

p {
  color: $gray7;
  font-size: $px-15;
  margin-top: 1rem !important;
}
</style>
