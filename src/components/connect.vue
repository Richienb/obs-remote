<template>
<div>
    <div class="alert error" v-if="error">{{ error.message }}</div>
    <form @submit.prevent="connect" v-if="!needAuth">
        <h2 class="mdc-typography--headline2">Connect</h2>
        <div class="mdc-text-field mdc-text-field--outlined full-width" data-mdc-auto-init="MDCTextField">
            <input type="text" id="e1" v-model="host" class="mdc-text-field__input">
            <div class="mdc-notched-outline">
                <div class="mdc-notched-outline__leading"></div>
                <div class="mdc-notched-outline__notch">
                    <label for="e1" class="mdc-floating-label">Host (not localhost)</label>
                </div>
                <div class="mdc-notched-outline__trailing"></div>
            </div>
        </div>
        <br><br>
        <div class="mdc-text-field mdc-text-field--outlined full-width" data-mdc-auto-init="MDCTextField">
            <input type="number" id="e2" v-model="port" class="mdc-text-field__input" value="4444">
            <div class="mdc-notched-outline">
                <div class="mdc-notched-outline__leading"></div>
                <div class="mdc-notched-outline__notch">
                    <label for="e2" class="mdc-floating-label mdc-floating-label--float-above">Port</label>
                </div>
                <div class="mdc-notched-outline__trailing"></div>
            </div>
        </div>
        <br><br>
        <button class="connect--go mdc-button mdc-button--raised" data-mdc-auto-init="MDCRipple" :disabled="connecting" v-text="connecting ? 'Connecting...' : 'Connect'"></button>
    </form>
    <form @submit.prevent="login" v-if="needAuth">
        <h2 class="mdc-typography--headline2">Password required</h2>
        <div class="mdc-text-field mdc-text-field--outlined full-width" data-mdc-auto-init="MDCTextField">
            <input type="password" id="e3" v-model="password" class="mdc-text-field__input">
            <div class="mdc-notched-outline">
                <div class="mdc-notched-outline__leading"></div>
                <div class="mdc-notched-outline__notch">
                    <label for="e3" class="mdc-floating-label">Password</label>
                </div>
                <div class="mdc-notched-outline__trailing"></div>
            </div>
        </div>
        <br>
        <br>
        <button class="mdc-button mdc-button--raised" :disabled="authenticating" v-text="authenticating ? 'Checking...' : 'Log In'" data-mdc-auto-init="MDCRipple"></button>
    </form>
</div>
</template>

<script>
import * as mdc from 'material-components-web'

mdc.autoInit()

import {
    mapState
} from 'vuex'

import {
    parseHashBang
} from '../util'

export default {
    data() {
        const data = {
            authenticating: false,
            connecting: false,
            error: null,
            host: '',
            password: '',
            port: 4444
        }

        const vars = parseHashBang()
        if (vars.host) {
            data.host = vars.host
        } else {
            data.host = this.$store.state.settings.defaultHost
        }
        if (vars.port) {
            data.port = parseInt(vars.port, 10)
        } else {
            data.port = this.$store.state.settings.defaultPort
        }
        if (vars.password) {
            data.password = vars.password
        }
        return data
    },
    computed: {
        ...mapState('obs', {
            needAuth: state => state.connection === 'auth'
        }),
        urlified() {
            let url = ''
            if (this.host) {
                url += '&host=' + this.host
            }
            if (this.port && this.port !== 4444) {
                url += '&port=' + this.port
            }
            return url
        }
    },
    watch: {
        urlified: {
            handler(newValue) {
                this.$emit('update:query', newValue)
            },
            immediate: true
        }
    },
    methods: {
        async connect() {
            this.connecting = true
            this.error = null
            try {
                await this.$store.dispatch('obs/connect', {
                    host: this.host.length ? this.host : undefined,
                    port: this.port
                })
                if (this.host.length > 0) {
                    this.$store.commit('settings/set', {
                        key: 'defaultHost',
                        value: this.host
                    })
                }
                if (this.port) {
                    this.$store.commit('settings/set', {
                        key: 'defaultPort',
                        value: this.port
                    })
                }
            } catch (err) {
                this.error = err
            }
            this.connecting = false
        },
        async login() {
            if (this.password.length === 0) {
                return
            }

            this.authenticating = true
            this.error = null
            try {
                await this.$store.dispatch('obs/authenticate', {
                    password: this.password
                })
            } catch (err) {
                this.error = err
            }
            this.authenticating = false
        }
    }
}
</script>
