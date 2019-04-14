<template>
<div style="display: contents">
    <header class="mdc-top-app-bar mdc-top-app-bar--short mdc-top-app-bar--fixed">
        <div class="mdc-top-app-bar__row">
            <section class="mdc-top-app-bar__section mdc-top-app-bar__section--align-start">
                <span class="mdc-top-app-bar__title">OBS Remote</span>
            </section>
            <section class="mdc-top-app-bar__section mdc-top-app-bar__section--align-end">
                <div v-if="connectionReady" key="editing-button" style="display: contents">
                    <button @click="toggleEditing" :class="{'is-highlighted': !hasEdited}" class="material-icons-outlined mdc-icon-button mdc-top-app-bar__action-item--unbounded" aria-label="Edit panels"
                        data-mdc-auto-init="MDCRipple">{{ editing ? 'done' : 'mode_edit' }}</button>
                </div>
                <button @click="modSettings(!showingSettings)" class="material-icons-outlined mdc-icon-button mdc-top-app-bar__action-item--unbounded" aria-label="Settings" data-mdc-auto-init="MDCRipple">settings</button>
                <button @click="toggleFullscreen" class="material-icons-outlined mdc-icon-button mdc-top-app-bar__action-item--unbounded" aria-label="Full screen" data-mdc-auto-init="MDCRipple">fullscreen</button>
                <div v-if="connectionReady" key="disconnect-button" style="display: contents">
                    <button @click="disconnect" class="material-icons-outlined mdc-icon-button mdc-top-app-bar__action-item--unbounded" aria-label="Disconnect" data-mdc-auto-init="MDCRipple">power_settings_new</button>
                </div>
            </section>
        </div>
    </header>
    <div class="mdc-top-app-bar--short-fixed-adjust"></div>
</div>
</template>

<script>
import * as mdc from 'material-components-web'

function documentReady(fn) {
    if (document.attachEvent ? document.readyState === "complete" : document.readyState !== "loading") {
        fn();
    } else {
        document.addEventListener('DOMContentLoaded', fn);
    }
}

documentReady(() => {
    mdc.autoInit()

    Array.prototype.forEach.call(document.querySelectorAll(".mdc-icon-button[data-mdc-auto-init='MDCRipple']"), ({
        MDCRipple
    }, _) => {
        // Fix ripples
        MDCRipple.unbounded = true
    });
})

import {
    mapActions,
    mapGetters,
    mapMutations,
    mapState
} from 'vuex'

import fullscreen from '../mixins/fullscreen'

export default {
    mixins: [fullscreen],
    computed: {
        ...mapState(['editing', 'showingSettings']),
        ...mapState('settings', ['hasEdited']),
        ...mapGetters('obs', ['connectionReady'])
    },
    methods: {
        toggleEditing() {
            if (!this.hasEdited && !this.editing) {
                this.setSetting({
                    key: 'hasEdited',
                    value: true
                })
            }
            this.setEditing(!this.editing)
        },
        modSettings(show) {
            this.showSettings(show)
            if (!dialog) var dialog = new mdc.dialog.MDCDialog(document.querySelector('.settings--global'))
            if (show) dialog.open()
            else dialog.close()
        },
        ...mapActions({
            setEditing: 'editing'
        }),
        ...mapActions('obs', [
            'disconnect'
        ]),
        ...mapMutations(['showSettings']),
        ...mapMutations('settings', {
            setSetting: 'set'
        })
    }
}
</script>
