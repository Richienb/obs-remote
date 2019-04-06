<template>
<div class="panel">
    <div class="panel-header">
        <div class="panel-name">
            <slot name="name"></slot>
        </div>
        <button class="mdc-icon-button material-icons-outlined" v-if="hasSettings" @click="settingsOpen = !settingsOpen" data-mdc-auto-init="MDCRipple">
            settings
        </button>
        <button class="mdc-icon-button material-icons-outlined" v-if="depth > 0" @click="remove" data-mdc-auto-init="MDCRipple">
            delete_forever
        </button>
    </div>
    <div v-if="!editing || isGrid" class="panel-content" :class="contentClass">
        <slot></slot>
    </div>
    <overlay v-if="settingsOpen" @overlay-click="settingsOpen = false">
        <div class="modal-header">
            <h2 class="title">Settings</h2>
            <button class="close" @click="settingsOpen = false"></button>
        </div>
        <slot name="settings"></slot>
    </overlay>
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
    mapState
} from 'vuex'

import Overlay from './overlay'

export default {
    components: {
        Overlay
    },
    props: {
        contentClass: null,
        isGrid: {
            type: Boolean,
            default: () => false
        }
    },
    data() {
        return {
            settingsOpen: false
        }
    },
    computed: {
        ...mapState(['editing']),
        depth() {
            return this.$parent.depth
        },
        id() {
            return this.$parent.id
        },
        panel() {
            return this.$parent.panel
        },
        hasSettings() {
            return Boolean(this.$slots.settings)
        }
    },
    watch: {
        editing(newval) {
            if (!newval) {
                this.settingsOpen = false
            }
        }
    },
    methods: {
        remove() {
            this.$store.dispatch('layout/removePanel', {
                id: this.id
            })
        }
    }
}
</script>
