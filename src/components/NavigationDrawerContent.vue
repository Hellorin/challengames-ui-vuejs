<template>
    <div>
        <br/>
        <div class="text-xs-center">
            <v-icon x-large color="green darken-1">face</v-icon>
        </div>
        <template v-if="$store.getters.isConnected">
            <br/>
            <div class="text-xs-center">
                <h2>{{ $t('connectedAs') }} <b class="green--text darken-1--text">{{ $store.getters.username }}  </b><v-icon @click="logoff">exit_to_app</v-icon></h2>
            </div>
            <br/>
        </template>
        <template v-else>
            <v-form v-model="valid">
                <v-container grid-list-md>
                    <v-layout row wrap>
                        <v-flex xs6>
                            <v-text-field
                                label="Username"
                                outline
                                required
                                v-model="loginUsername"
                                @keyup.enter="login"
                            ></v-text-field>
                        </v-flex>

                        <v-flex xs6>
                            <v-text-field
                                :rules="nameRules"
                                label="Password"
                                required
                                outline
                                type="password"
                                @keyup.enter="login"
                            ></v-text-field>
                        </v-flex>

                        <v-flex xs2></v-flex>
                        <v-flex xs8>
                            <v-btn block class="green darken-3"
                                @click="login"
                            >
                                Log-in
                            </v-btn>
                        </v-flex>
                        <v-flex xs2></v-flex>
                        <template v-if="loginLoading == true">
                            <v-flex xs12>
                                <v-progress-linear :indeterminate="true"></v-progress-linear>
                            </v-flex>
                        </template>
                    </v-layout>
                </v-container>
            </v-form>
        </template>

        <v-list>
            <v-divider></v-divider>

            <v-list-tile>
                <v-list-tile-action>
                    <v-icon>home</v-icon>
                </v-list-tile-action>
                <v-list-tile-content>
                    <router-link to='/'>
                        <v-list-tile-title>{{ $t('homeLabel') }}</v-list-tile-title>
                    </router-link>
                </v-list-tile-content>
            </v-list-tile>

            <template v-if="$store.getters.isConnected">
                <v-divider></v-divider>

                <v-list-tile two-line>
                    <v-list-tile-action>
                        <v-icon>add_box</v-icon>
                    </v-list-tile-action>
                    <v-list-tile-content>
                        <v-list-tile-title>
                            <v-dialog
                                v-model="dialogCreateChallenge"
                                width="500">
                                <template v-slot:activator="{ on }">
                                    <button v-on="on">
                                        {{ $t('addNewChallengeLabel') }}
                                    </button>
                                </template>
                                <CreateChallengeDialogContent
                                    @closeCreateChallengeDialog="dialogCreateChallenge = false"
                                    @createChallenge="createChallenge"
                                    :dialog="dialog" />
                            </v-dialog>
                        </v-list-tile-title>
                    </v-list-tile-content>
                </v-list-tile>

                <router-link to="/myChallenges">
                    <v-list-tile>
                        <v-list-tile-action>
                            <v-badge>
                                <template v-slot:badge>
                                    <span>{{ $store.getters.myChallenges.length }}</span>
                                </template>
                            </v-badge>
                        </v-list-tile-action>
                        <v-list-tile-content>
                            <v-list-tile-title>
                                {{ $t('myChallengesLabel') }}
                            </v-list-tile-title>
                        </v-list-tile-content>
                    </v-list-tile>
                </router-link>
            </template>

            <v-divider></v-divider>

            <v-list-tile>
                <v-list-tile-action>
                    <v-icon>group</v-icon>
                </v-list-tile-action>
                <v-list-tile-content>
                    <v-list-tile-title>
                        <router-link to="/ranking">
                            {{ $t('rankingLabel') }}
                        </router-link>
                    </v-list-tile-title>
                </v-list-tile-content>
            </v-list-tile>

        </v-list>
    </div>
</template>

<script>
    import CreateChallengeDialogContent from './core/CreateChallengeDialogContent'

    export default {
        name: "NavigationDrawerContent",
        components: {
            CreateChallengeDialogContent
        },
        props: ['drawer'],
        data () {
            return {
                dialog: false,
                dialogCreateChallenge: false,
                loginUsername: null,
                loginLoading: false
            }
        },
        methods: {
            logoff: function() {
                this.$store.commit('logoff', this.loginUsername);
            },
            login: function() {
                this.$store.commit('login', this.loginUsername);
                this.loginUsername = null;
            },
            createChallenge: function(data) {
                var mappedData = {
                    name: data.challengeName,
                    description: data.description,
                    submitter: this.$store.getters.username,
                    challengee: data.challengee,
                    status: 'OPEN'
                }
                this.$store.commit('createChallenge', mappedData)
                this.$emit('createChallenge')
            }
        },
        beforeMount() {
            this.$store.commit('loadMyChallenges')
        }
    }
</script>

<style scoped>
    a {
        color: white!important;
        text-decoration: none;
    }
</style>
