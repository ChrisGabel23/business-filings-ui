<template>
  <div id="agm-extension">
    <ConfirmDialog
      ref="confirm"
      attach="#agm-extension"
    />

    <PaymentErrorDialog
      attach="#agm-extension"
      filingName="AGM Extension"
      :dialog="paymentErrorDialog"
      :errors="saveErrors"
      :warnings="saveWarnings"
      @exit="onPaymentErrorDialogExit()"
    />

    <ResumeErrorDialog
      attach="#agm-extension"
      :dialog="resumeErrorDialog"
      @exit="goToDashboard(true)"
    />

    <SaveErrorDialog
      attach="#agm-extension"
      filingName="AGM Extension"
      :dialog="!!saveErrorReason"
      :disableRetry="busySaving"
      :errors="saveErrors"
      :warnings="saveWarnings"
      @exit="saveErrorReason=null"
      @retry="onSaveErrorDialogRetry()"
      @okay="onSaveErrorDialogOkay()"
    />

    <StaffPaymentDialog
      :staffPaymentData.sync="staffPaymentData"
      attach="#agm-extension"
      :dialog="staffPaymentDialog"
      :loading="filingPaying"
      @exit="staffPaymentDialog=false"
      @submit="onClickFilePay(true)"
    />

    <!-- Initial Page Load Transition -->
    <v-fade-transition>
      <div
        v-show="showLoadingContainer"
        class="loading-container"
      >
        <div class="loading__content">
          <v-progress-circular
            color="primary"
            :size="50"
            indeterminate
          />
          <div class="loading-msg">
            {{ loadingMessage }}
          </div>
        </div>
      </div>
    </v-fade-transition>

    <!-- Main Body -->
    <v-container
      v-if="dataLoaded"
      class="view-container"
    >
      <v-row>
        <v-col
          cols="12"
          lg="9"
        >
          <article id="main-article">
            <!-- Page Title -->
            <header>
              <h1>
                AGM Extension
              </h1>
            </header>

            <ExpandableHelp helpLabel="Help with Annual General Meeting Extension">
              <template #content>
                Help text, or sub-component, goes here.
              </template>
            </ExpandableHelp>

            <!-- Extension Eligibility -->
            <section class="mt-8">
              <header>
                <h2>Extension Eligibility</h2>
                <p>
                  Fill out the following section to evaluate the eligibility for extension.
                </p>
              </header>

              <div
                id="extension-eligibility-section"
                :class="{ 'invalid-extension-eligibility': !extensionEligibilityValid && showErrors }"
              >
                <v-card
                  flat
                  class="py-4 px-5"
                >
                  <v-row
                    no-gutters
                    class="my-4"
                  >
                    <v-col
                      cols="12"
                      sm="3"
                      class="pr-4"
                    >
                      <strong :class="{ 'app-red': !extensionEligibilityValid && showErrors }">
                        Business in Good Standing
                      </strong>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="9"
                    >
                      Yes
                    </v-col>
                  </v-row>

                  <v-divider />

                  <v-row
                    no-gutters
                    class="my-4"
                  >
                    <v-col
                      cols="12"
                      sm="3"
                      class="pr-4"
                    >
                      <strong :class="{ 'app-red': !extensionEligibilityValid && showErrors }">
                        Date of Incorporation
                      </strong>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="9"
                    >
                      (date goes here)
                    </v-col>
                  </v-row>

                  <v-divider />

                  <v-row
                    no-gutters
                    class="my-4"
                  >
                    <v-col
                      cols="12"
                      sm="3"
                      class="pr-4"
                    >
                      <strong :class="{ 'app-red': !extensionEligibilityValid && showErrors }">
                        Date of the Last Extension Grant on Record
                      </strong>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="9"
                    >
                      (date goes here)
                    </v-col>
                  </v-row>

                  <v-divider />

                  <v-row
                    no-gutters
                    class="my-4"
                  >
                    <v-col
                      cols="12"
                      sm="3"
                      class="pr-4"
                    >
                      <strong :class="{ 'app-red': !extensionEligibilityValid && showErrors }">
                        Date the Annual General Meeting is intended to be held
                      </strong>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="9"
                    >
                      (date picker goes here)
                    </v-col>
                  </v-row>

                  <v-divider />

                  <v-row
                    no-gutters
                    class="my-4"
                  >
                    <v-col
                      cols="12"
                      sm="3"
                      class="pr-4"
                    >
                      <strong :class="{ 'app-red': !extensionEligibilityValid && showErrors }">
                        Date of Previous Annual General Meeting
                      </strong>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="9"
                    >
                      (radio buttons go here)
                    </v-col>
                  </v-row>

                  <v-divider />

                  <v-row
                    no-gutters
                    class="my-4"
                  >
                    <v-col
                      cols="12"
                      sm="3"
                      class="pr-4"
                    >
                      <strong :class="{ 'app-red': !extensionEligibilityValid && showErrors }">
                        Was the above extension grant for the current AGM?
                      </strong>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="9"
                    >
                      (radio buttons go here)
                    </v-col>
                  </v-row>
                </v-card>
              </div>
            </section>

            <!-- Certify -->
            <section>
              <header>
                <h2>Certify</h2>
                <p class="grey-text">
                  Enter the legal name of the person authorized to complete and submit this filing.
                </p>
              </header>
              <div
                id="certify-form-section"
                :class="{ 'invalid-section': !certifyFormValid && showErrors }"
              >
                <Certify
                  ref="certifyRef"
                  :isCertified.sync="isCertified"
                  :certifiedBy.sync="certifiedBy"
                  :class="{ 'invalid-certify': !certifyFormValid && showErrors }"
                  :entityDisplay="displayName()"
                  :message="certifyText(FilingCodes.AGM_EXTENSION)"
                  @valid="certifyFormValid=$event"
                />
              </div>
            </section>
          </article>
        </v-col>

        <v-col
          cols="12"
          lg="3"
          style="position: relative"
        >
          <aside>
            <affix
              relative-element-selector="#main-article"
              :offset="{ top: 120, bottom: 40 }"
            >
              <SbcFeeSummary
                :filingData="filingData"
                :payURL="getPayApiUrl"
                @total-fee="totalFee=$event"
              />
            </affix>
          </aside>
        </v-col>
      </v-row>
    </v-container>

    <!-- Buttons -->
    <v-container
      id="buttons-container"
      class="list-item"
    >
      <div class="buttons-left">
        <v-btn
          id="consent-save-btn"
          large
          :disabled="busySaving"
          :loading="saving"
          @click="onClickSave()"
        >
          <span>Save</span>
        </v-btn>
        <v-btn
          id="consent-save-resume-btn"
          large
          :disabled="busySaving"
          :loading="savingResuming"
          @click="onClickSaveResume()"
        >
          <span>Save and Resume Later</span>
        </v-btn>
      </div>

      <div class="buttons-right">
        <v-tooltip
          top
          color="tooltipColor"
          content-class="top-tooltip"
        >
          <template #activator="{ on }">
            <div
              class="d-inline"
              v-on="on"
            >
              <v-btn
                id="consent-file-pay-btn"
                color="primary"
                large
                :disabled="busySaving"
                :loading="filingPaying"
                @click="onClickFilePay()"
              >
                <span>{{ isPayRequired ? "File and Pay" : "File Now (no fee)" }}</span>
              </v-btn>
            </div>
          </template>
          Ensure all of your information is entered correctly before you File.<br>
          There is no opportunity to change information beyond this point.
        </v-tooltip>

        <v-btn
          id="consent-cancel-btn"
          large
          :disabled="busySaving"
          @click="goToDashboard()"
        >
          <span>Cancel</span>
        </v-btn>
      </div>
    </v-container>
  </div>
</template>

<script lang="ts">
import { Component, Mixins, Watch } from 'vue-property-decorator'
import { Getter } from 'pinia-class'
import { StatusCodes } from 'http-status-codes'
import { navigate } from '@/utils'
import SbcFeeSummary from 'sbc-common-components/src/components/SbcFeeSummary.vue'
import { ExpandableHelp } from '@bcrs-shared-components/expandable-help'
import { Certify } from '@/components/common'
import { ConfirmDialog, PaymentErrorDialog, ResumeErrorDialog, SaveErrorDialog, StaffPaymentDialog }
  from '@/components/dialogs'
import { CommonMixin, DateMixin, EnumMixin, FilingMixin, ResourceLookupMixin } from '@/mixins'
import { LegalServices } from '@/services/'
import { FilingCodes, FilingStatus, FilingTypes, Routes, SaveErrorReasons, StaffPaymentOptions }
  from '@/enums'
import { ConfirmDialogType, StaffPaymentIF } from '@/interfaces'
import { useBusinessStore, useConfigurationStore, useRootStore } from '@/stores'

@Component({
  components: {
    Certify,
    ConfirmDialog,
    ExpandableHelp,
    PaymentErrorDialog,
    ResumeErrorDialog,
    SaveErrorDialog,
    SbcFeeSummary,
    StaffPaymentDialog
  }
})
export default class AgmExtension extends Mixins(CommonMixin, DateMixin,
  EnumMixin, FilingMixin, ResourceLookupMixin) {
  // Refs
  $refs!: {
    confirm: ConfirmDialogType,
    certifyRef: Certify
  }

  @Getter(useConfigurationStore) getAuthWebUrl!: string
  @Getter(useBusinessStore) getLegalName!: string
  @Getter(useConfigurationStore) getPayApiUrl!: string
  @Getter(useRootStore) isRoleStaff!: boolean

  // enum for template
  readonly FilingCodes = FilingCodes

  // variables for Certify component
  certifiedBy = ''
  isCertified = false
  certifyFormValid = false

  // variables for Extension Eligiblity section
  extensionEligibilityValid = false

  // variables for staff payment
  staffPaymentData = { option: StaffPaymentOptions.NONE } as StaffPaymentIF
  staffPaymentDialog = false

  // variables for displaying dialogs
  resumeErrorDialog = false
  saveErrorReason: SaveErrorReasons = null
  paymentErrorDialog = false

  // other variables
  totalFee = 0
  dataLoaded = false
  loadingMessage = ''
  filingId = 0 // id of this consent to continuation out filing
  savedFiling: any = null // filing during save
  saving = false // true only when saving
  savingResuming = false // true only when saving and resuming
  showErrors = false // true when we press on File and Pay (trigger validation)
  filingPaying = false // true only when filing and paying
  haveChanges = false
  saveErrors = []
  saveWarnings = []

  /** True if loading container should be shown, else False. */
  get showLoadingContainer (): boolean {
    // show loading container when data isn't yet loaded and when
    // no dialogs are displayed (otherwise dialogs may be hidden)
    return (!this.dataLoaded && !this.saveErrorReason && !this.paymentErrorDialog)
  }

  /** The Base URL string. */
  get baseUrl (): string {
    return sessionStorage.getItem('BASE_URL')
  }

  /** True if page is valid, else False. */
  get isPageValid (): boolean {
    return (this.certifyFormValid && this.extensionEligibilityValid)
  }

  /** True when saving, saving and resuming, or filing and paying. */
  get busySaving (): boolean {
    return (this.saving || this.savingResuming || this.filingPaying)
  }

  /** True if payment is required, else False. */
  get isPayRequired (): boolean {
    // FUTURE: modify rule here as needed
    return (this.totalFee > 0)
  }

  /** Called when component is created. */
  created (): void {
    // init
    this.setFilingData([])

    // before unloading this page, if there are changes then prompt user
    window.onbeforeunload = (event) => {
      if (this.haveChanges) {
        event.preventDefault()
        // NB: custom text is not supported in all browsers
        event.returnValue = 'You have unsaved changes. Are you sure you want to leave?'
      }
    }

    // this is the id of THIS filing
    // if 0, this is a new filing
    // otherwise it's a draft filing
    this.filingId = +this.$route.params.filingId // number or NaN

    // if required data isn't set, go back to dashboard
    if (!this.getIdentifier || isNaN(this.filingId)) {
      this.$router.push({ name: Routes.DASHBOARD })
    }
  }

  /** Called when component is mounted. */
  async mounted (): Promise<void> {
    // wait until entire view is rendered (including all child components)
    // see https://v3.vuejs.org/api/options-lifecycle-hooks.html#mounted
    await this.$nextTick()

    if (this.filingId > 0) {
      this.loadingMessage = `Resuming Your Request for AGM Extension`
    } else {
      this.loadingMessage = `Preparing Your Request for AGM Extension`
    }

    // fetch draft (which may overwrite some properties)
    if (this.filingId > 0) {
      await this.fetchDraftFiling()
    }

    this.dataLoaded = true

    // always include consent continue out code
    // use existing Priority and Waive Fees flags
    this.updateFilingData('add', FilingCodes.AGM_EXTENSION, this.staffPaymentData.isPriority,
      (this.staffPaymentData.option === StaffPaymentOptions.NO_FEE))
  }

  /** Fetches the draft consent filing. */
  async fetchDraftFiling (): Promise<void> {
    const url = `businesses/${this.getIdentifier}/filings/${this.filingId}`
    await LegalServices.fetchFiling(url).then(filing => {
      // verify data
      if (!filing) throw new Error('Missing filing')
      if (!filing.header) throw new Error('Missing header')
      if (!filing.business) throw new Error('Missing business')
      if (!filing.agmExtension) throw new Error('Missing agm extension object')
      if (filing.header.name !== FilingTypes.AGM_EXTENSION) throw new Error('Invalid filing type')
      if (filing.header.status !== FilingStatus.DRAFT) throw new Error('Invalid filing status')
      if (filing.business.identifier !== this.getIdentifier) throw new Error('Invalid business identifier')
      if (filing.business.legalName !== this.getLegalName) throw new Error('Invalid business legal name')

      // load Certified By (but not Date)
      this.certifiedBy = filing.header.certifiedBy

      // load Staff Payment properties
      if (filing.header.routingSlipNumber) {
        this.staffPaymentData = {
          option: StaffPaymentOptions.FAS,
          routingSlipNumber: filing.header.routingSlipNumber,
          isPriority: filing.header.priority
        } as StaffPaymentIF
      } else if (filing.header.bcolAccountNumber) {
        this.staffPaymentData = {
          option: StaffPaymentOptions.BCOL,
          bcolAccountNumber: filing.header.bcolAccountNumber,
          datNumber: filing.header.datNumber,
          folioNumber: filing.header.folioNumber,
          isPriority: filing.header.priority
        } as StaffPaymentIF
      } else if (filing.header.waiveFees) {
        this.staffPaymentData = {
          option: StaffPaymentOptions.NO_FEE
        } as StaffPaymentIF
      } else {
        this.staffPaymentData = {
          option: StaffPaymentOptions.NONE
        } as StaffPaymentIF
      }
    }).catch(error => {
      // eslint-disable-next-line no-console
      console.log('fetchDraftFiling() error =', error)
      this.resumeErrorDialog = true
    })
  }

  /**
   * Called when user clicks Save button
   * or when user retries from Save Error dialog.
   */
  async onClickSave (): Promise<void> {
    // prevent double saving
    if (this.busySaving) return

    this.saving = true

    // save draft filing
    this.savedFiling = await this.saveFiling(true).catch(error => {
      this.saveErrorReason = SaveErrorReasons.SAVE
      // try to return filing (which may exist depending on save error)
      return error?.response?.data?.filing || null
    })

    const filingId = +this.savedFiling?.header?.filingId || 0
    if (filingId > 0) {
      // save filing ID for possible future updates
      this.filingId = filingId
    }

    // if there was no error, finish save process now
    // otherwise, dialog may finish this later
    if (!this.saveErrorReason) this.onClickSaveFinish()

    this.saving = false
  }

  onClickSaveFinish (): void {
    // safety check
    if (this.filingId > 0) {
      // changes were saved, so clear flag
      this.haveChanges = false
    } else {
      // eslint-disable-next-line no-console
      console.log('onClickSaveFinish(): invalid filing ID, filing =', null)
    }
  }

  /**
   * Called when user clicks Save and Resume later button
   * or when user retries from Save Error dialog.
   */
  async onClickSaveResume (): Promise<void> {
    // prevent double saving
    if (this.busySaving) return

    this.savingResuming = true

    // save draft filing
    this.savedFiling = await this.saveFiling(true).catch(error => {
      this.saveErrorReason = SaveErrorReasons.SAVE_RESUME
      // try to return filing (which may exist depending on save error)
      return error?.response?.data?.filing || null
    })

    const filingId = +this.savedFiling?.header?.filingId || 0
    if (filingId > 0) {
      // save filing ID for possible future updates
      this.filingId = filingId
    }

    // if there was no error, finish save-resume process now
    // otherwise, dialog may finish this later
    if (!this.saveErrorReason) this.onClickSaveResumeFinish()

    this.savingResuming = false
  }

  onClickSaveResumeFinish (): void {
    // safety check
    if (this.filingId > 0) {
      // changes were saved, so go to dashboard
      this.goToDashboard(true)
    } else {
      // eslint-disable-next-line no-console
      console.log('onClickSaveResumeFinish(): invalid filing ID, filing =', null)
    }
  }

  /**
   * Called when user clicks File and Pay button
   * or when user retries from Save Error dialog
   * or when user submits from Staff Payment dialog.
   */
  async onClickFilePay (fromStaffPayment = false): Promise<void> {
    // if there is an invalid component, scroll to it
    if (!this.isPageValid) {
      this.showErrors = true
      // *** TODO: check for section errors here
      if (!this.extensionEligibilityValid) {
        // Show error message of detail comment text area if invalid
        // this.$refs.detailCommentRef.$refs.textarea.error = true
      }
      if (!this.certifyFormValid) {
        // Show error message of legal name text field if invalid
        this.$refs.certifyRef.$refs.certifyTextfieldRef.error = true
      }
      await this.validateAndScroll(this.validFlags, this.validComponents)
      return
    }

    // prevent double saving
    if (this.busySaving) return

    // if this is a staff user clicking File and Pay (not Submit)
    // then detour via Staff Payment dialog
    if (this.isRoleStaff && !fromStaffPayment) {
      this.staffPaymentDialog = true
      return
    }

    this.filingPaying = true

    // save final filing (not draft)
    this.savedFiling = await this.saveFiling(false).catch(error => {
      if (error?.response?.status === StatusCodes.PAYMENT_REQUIRED) {
        // changes were saved if a 402 is received, so clear flag
        this.haveChanges = false
        // display payment error dialog
        this.paymentErrorDialog = true
        // try to return filing (which should exist in this case)
        return error?.response?.data?.filing || null
      } else {
        this.saveErrorReason = SaveErrorReasons.FILE_PAY
        // try to return filing (which may exist depending on save error)
        return error?.response?.data?.filing || null
      }
    })

    const filingId = +this.savedFiling?.header?.filingId || 0
    if (filingId > 0) {
      // save filing ID for possible future updates
      this.filingId = filingId
    }

    // if there were no errors, finish file-pay process now
    // otherwise, dialogs may finish this later
    if (!this.paymentErrorDialog && !this.saveErrorReason) this.onClickFilePayFinish()

    this.filingPaying = false
  }

  onClickFilePayFinish (): void {
    // safety check
    if (this.filingId > 0) {
      // changes were saved, so clear flag
      this.haveChanges = false

      const isPaymentActionRequired = Boolean(this.savedFiling.header.isPaymentActionRequired)

      // if payment action is required, navigate to Pay URL
      if (isPaymentActionRequired) {
        const paymentToken = this.savedFiling.header.paymentToken
        const returnUrl = encodeURIComponent(this.baseUrl + '?filing_id=' + this.filingId)
        const payUrl = this.getAuthWebUrl + 'makepayment/' + paymentToken + '/' + returnUrl
        // assume Pay URL is always reachable
        // otherwise, user will have to retry payment later
        navigate(payUrl)
      } else {
        // route to dashboard with filing id parameter
        this.$router.push({ name: Routes.DASHBOARD, query: { filing_id: this.filingId.toString() } })
      }
    } else {
      // eslint-disable-next-line no-console
      console.log('onClickFilePayFinish(): invalid filing ID, filing =', null)
    }
  }

  /** Builds and saves the filing. NB: Caller needs to catch exceptions. */
  async saveFiling (isDraft): Promise<any> {
    this.saveErrors = []
    this.saveWarnings = []

    // if this is a new filing, ensure there are no pending tasks
    if (this.filingId === 0) {
      const hasPendingTasks = await LegalServices.hasPendingTasks(this.getIdentifier)
        .catch(() => {
          this.saveErrors = [{ error: 'Unable to check server for pending tasks.' }]
          throw new Error()
        })

      if (hasPendingTasks) {
        this.saveErrors = [
          { error: 'Another draft filing already exists. Please complete it before creating a new filing.' }
        ]
        throw new Error()
      }
    }

    const header: any = {
      header: {
        name: FilingTypes.CONSENT_CONTINUATION_OUT,
        certifiedBy: this.certifiedBy || '',
        date: this.getCurrentDate // NB: API will reassign this date according to its clock
      }
    }

    switch (this.staffPaymentData.option) {
      case StaffPaymentOptions.FAS:
        header.header['routingSlipNumber'] = this.staffPaymentData.routingSlipNumber
        header.header['priority'] = this.staffPaymentData.isPriority
        break

      case StaffPaymentOptions.BCOL:
        header.header['bcolAccountNumber'] = this.staffPaymentData.bcolAccountNumber
        header.header['datNumber'] = this.staffPaymentData.datNumber
        header.header['folioNumber'] = this.staffPaymentData.folioNumber
        header.header['priority'] = this.staffPaymentData.isPriority
        break

      case StaffPaymentOptions.NO_FEE:
        header.header['waiveFees'] = true
        break

      case StaffPaymentOptions.NONE: // should never happen
        break
    }

    const business: any = {
      business: {
        foundingDate: this.dateToApi(this.getFoundingDate),
        identifier: this.getIdentifier,
        legalName: this.getLegalName,
        legalType: this.getLegalType
      }
    }

    const data: any = {
      [FilingTypes.AGM_EXTENSION]: {
        // properties go here
      }
    }

    // build filing
    const filing = Object.assign({}, header, business, data)
    try {
      let ret
      if (this.filingId > 0) {
        // we have a filing id, so update an existing filing
        ret = await LegalServices.updateFiling(this.getIdentifier, filing, this.filingId, isDraft)
      } else {
        // filing id is 0, so create a new filing
        ret = await LegalServices.createFiling(this.getIdentifier, filing, isDraft)
      }
      return ret
    } catch (error) {
      // save errors or warnings, if any
      this.saveErrors = error?.response?.data?.errors || []
      this.saveWarnings = error?.response?.data?.warnings || []
      throw error
    }
  }

  /**
   * Routes to dashboard if there are no outstanding changes,
   * else prompts user before routing.
   */
  goToDashboard (force = false): void {
    // check if there are no data changes
    if (!this.haveChanges || force) {
      // route to dashboard
      this.$router.push({ name: Routes.DASHBOARD })
        .catch(() => {}) // ignore error in case navigation was aborted
      return
    }

    // open confirmation dialog and wait for response
    this.$refs.confirm.open(
      'Unsaved Changes',
      'You have unsaved changes in your AGM Extension. Do you want to exit your filing?',
      {
        width: '45rem',
        persistent: true,
        yes: 'Return to my filing',
        no: null,
        cancel: 'Exit without saving'
      }
    ).then(() => {
      // if we get here, Yes was clicked
      // nothing to do
    }).catch(() => {
      // if we get here, Cancel was clicked
      // ignore changes
      this.haveChanges = false
      // route to dashboard
      this.$router.push({ name: Routes.DASHBOARD })
        .catch(() => {}) // ignore error in case navigation was aborted
    })
  }

  /** Handles Exit event from Payment Error dialog. */
  onPaymentErrorDialogExit (): void {
    if (this.isRoleStaff) {
      // close Payment Error dialog -- this
      // leaves user on Staff Payment dialog
      this.paymentErrorDialog = false
    } else {
      // close the dialog and go to dashboard --
      // user will have to retry payment from there
      this.paymentErrorDialog = false
      this.goToDashboard(true)
    }
  }

  /** Handles Retry events from Save Error dialog. */
  async onSaveErrorDialogRetry (): Promise<void> {
    switch (this.saveErrorReason) {
      case SaveErrorReasons.SAVE:
        // close the dialog and retry save
        this.saveErrorReason = null
        await this.onClickSave()
        break
      case SaveErrorReasons.SAVE_RESUME:
        // close the dialog and retry save-resume
        this.saveErrorReason = null
        await this.onClickSaveResume()
        break
      case SaveErrorReasons.FILE_PAY:
        // close the dialog and retry file-pay
        this.saveErrorReason = null
        if (this.isRoleStaff) await this.onClickFilePay(true)
        else await this.onClickFilePay()
        break
    }
  }

  /** Handles Okay events from Save Error dialog. */
  onSaveErrorDialogOkay (): void {
    switch (this.saveErrorReason) {
      case SaveErrorReasons.SAVE:
        // close the dialog and finish save process
        this.saveErrorReason = null
        this.onClickSaveFinish()
        break
      case SaveErrorReasons.SAVE_RESUME:
        // close the dialog and finish save-resume process
        this.saveErrorReason = null
        this.onClickSaveResumeFinish()
        break
      case SaveErrorReasons.FILE_PAY:
        // close the dialog and finish file-pay process
        this.saveErrorReason = null
        this.onClickFilePayFinish()
        break
    }
  }

  /** Array of valid components. Must match validFlags. */
  readonly validComponents = [
    'extension-eligibility-section',
    'certify-form-section'
  ]

  /** Object of valid flags. Must match validComponents. */
  get validFlags (): object {
    return {
      extensionEligibility: this.extensionEligibilityValid,
      certifyForm: this.certifyFormValid
    }
  }

  @Watch('extensionEligibilityValid')
  @Watch('certifyFormValid')
  onHaveChanges (): void {
    this.haveChanges = true
  }

  @Watch('staffPaymentData')
  onStaffPaymentDataChanged (val: StaffPaymentIF): void {
    const waiveFees = (val.option === StaffPaymentOptions.NO_FEE)

    // add Waive Fees flag to all filing codes
    this.updateFilingData('add', FilingCodes.CONSENT_CONTINUATION_OUT, val.isPriority, waiveFees)

    this.haveChanges = true
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/styles/theme.scss';

#agm-extension {
  /* Set "header-counter" to 0 */
  counter-reset: header-counter;
}

h2::before {
  /* Increment "header-counter" by 1 */
  counter-increment: header-counter;
  content: counter(header-counter) '. ';
}

article {
  .v-card {
    line-height: 1.2rem;
    font-size: $px-14;
  }
}

section + section {
  margin-top: 3rem;
}

h1 {
  margin-bottom: 1.25rem;
  line-height: 2rem;
  letter-spacing: -0.01rem;
}

h2 {
  margin-bottom: 0.25rem;
  margin-top: 3rem;
  font-size: 1.125rem;
}

strong {
  color: $gray9;
}

// Save & Filing Buttons
#buttons-container {
  padding-top: 2rem;
  border-top: 1px solid $gray5;

  .buttons-left {
    width: 50%;
  }

  .buttons-right {
    margin-left: auto;
  }

  .v-btn + .v-btn {
    margin-left: 0.5rem;
  }

  #consent-cancel-btn {
    margin-left: 0.5rem;
  }
}

// Fix font size and color to stay consistent.
:deep() {
  .invalid-extension-eligibility {
    .title-label {
      color: $app-red;
    }
  }

  .certify-clause, .certify-stmt, .grey-text {
    color: $gray7;
  }

  .invalid-certify {
    .certify-stmt, .title-label {
      color: $app-red;
    }
  }
}
</style>
