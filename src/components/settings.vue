<template>
<div class="settings--global mdc-dialog" role="alertdialog" aria-modal="true" aria-labelledby="my-dialog-title" aria-describedby="my-dialog-content">
    <div class="mdc-dialog__container">
        <div class="mdc-dialog__surface">
            <h2 class="mdc-dialog__title" id="my-dialog-title">Global Settings</h2>
            <div class="mdc-dialog__content" id="my-dialog-content">
                <h2 class="mdc-typography--headline6">Danger zone</h2>
                <button class="mdc-button mdc-button--outlined" @click="askForReset"><span class="mdc-button__label">Reset Layout</span></button>
            </div>
            <footer class="mdc-dialog__actions">
                <button type="button" class="mdc-button mdc-dialog__button">
                    <span class="mdc-button__label">Close</span>
                </button>
            </footer>
        </div>
    </div>
    <div class="mdc-dialog__scrim"></div>
</div>
</template>

<script>
import * as mdc from 'material-components-web'

mdc.autoInit()

import Overlay from './overlay'

import {
    mapMutations
} from 'vuex'

export default {
    components: {
        Overlay
    },
    methods: {
        askForReset() {
            // eslint-disable-next-line no-alert
            if (confirm('Are you sure you want to reset the layout?')) {
                this.resetLayout()
            }
        },
        ...mapMutations(['showSettings']),
        ...mapMutations('layout', ['resetLayout'])
    }
}
</script>
