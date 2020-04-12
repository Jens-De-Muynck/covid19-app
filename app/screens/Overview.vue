<template>
    <Page actionBarHidden="true">

        <RadSideDrawer ref="drawer">
            <StackLayout ~drawerContent backgroundColor="#ffffff">
                <FlexboxLayout marginTop="4%" width="100%">
                    <Image class="drawer-menu-icon" src="~/assets/images/menu-open.png" stretch="aspectFit" @tap="$refs.drawer.nativeView.closeDrawer()" />
                </FlexboxLayout>

                <FlexboxLayout class="drawer-screens" flexDirection="column">
                    <Label class="drawer-item" text="Overview" @tap="goToOverview()"/>
                    <Label class="drawer-item" text="Timeline" @tap="goToTimeline()"/>
                    <Label class="drawer-item" text="Watchlist" @tap="goToWatchlist()"/>
                </FlexboxLayout>

                <FlexboxLayout class="drawer-other" flexDirection="column">
                    <Label class="drawer-item" text="Settings" @tap="goToSettings()"/>
                    <Label class="drawer-item" text="About" @tap="goToAbout()"/>
                    <Label class="drawer-item" text="Log Out" @tap="logOut()"/>
                </FlexboxLayout>
            </StackLayout>

            <FlexboxLayout backgroundColor="#1A223F" flexDirection="column" ~mainContent>
                
                <GridLayout rows="*, auto, auto">
                    <FlexboxLayout justifyContent="space-between" alignItems="center" flexDirection="column" height="100%">
                        <Image src="~/assets/images/bg-shape.png" iosOverflowSafeArea="true" stretch="aspectFit" width="101%" rowSpan="3"></Image>
                        <Label class="WHO" text="World Health Organisation" @tap="gotoWebWHO()" />
                    </FlexboxLayout>

                    <FlexboxLayout class="content" flexDirection="column" justifyContent="space-between" alignItems="center">
                        <FlexboxLayout class="menu" flexDirection="column">
                            <FlexboxLayout class="menu" flexDirection="row" justifyContent="space-between">                            
                                <Image class="menu-icon" src="~/assets/images/menu.png" stretch="aspectFit" @tap="$refs.drawer.nativeView.showDrawer()" />
                                <Image class="menu-icon" src="~/assets/images/search.png" stretch="aspectFit" @tap="toggleSearchbar()"/>
                                <Image class="menu-icon" src="~/assets/images/settings.png" stretch="aspectFit" @tap="goToSettings()"/>
                            </FlexboxLayout>

                            <FlexboxLayout v-if='searchbarIsShown' class="searchbar_wrap">
                                <SearchBar ref="searchBar" hint="Search hint" v-model="searchPhrase" @submit="onSubmit()" color="#1a223f" backgroundColor="white"/>
                            </FlexboxLayout>
                        </FlexboxLayout>
                        
                        <FlexboxLayout class="title" justifyContent="space-between" width="100%" marginBottom="50px">
                            <Label class="current_country" v-model="current_country" @tap="makeApiCall('belgium')">{{current_country}}</Label>
                            <Image src="~/assets/images/heart-true.png" stretch="aspectFit" width="100px"></Image>
                        </FlexboxLayout>

                        <FlexboxLayout class="data-list" flexDirection="column" justifyContent="space-between">
                            <FlexboxLayout class="data-item">
                                <Label text="Cases"></Label>
                                <Label class="dashed-line"></Label>
                                <Label v-model="data_cases">{{data_cases}}</Label>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Label text="Cases Today"></Label>
                                <Label class="dashed-line"></Label>
                                <Label v-model="data_cases_today">{{data_cases_today}}</Label>
                            </FlexboxLayout>
                            
                            <FlexboxLayout class="data-item">
                                <Label text="Deaths"></Label>
                                <Label class="dashed-line"></Label>
                                <Label v-model="data_deaths">{{data_deaths}}</Label>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Label text="Deaths Today"></Label>
                                <Label class="dashed-line"></Label>
                                <Label v-model="data_deaths_today">{{data_deaths_today}}</Label>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Label text="Recovered"></Label>
                                <Label class="dashed-line"></Label>
                                <Label v-model="data_recovered">{{data_recovered}}</Label>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Label text="Active"></Label>
                                <Label class="dashed-line"></Label>
                                <Label v-model="data_active">{{data_active}}</Label>
                            </FlexboxLayout>
                            
                            <FlexboxLayout class="data-item">
                                <Label text="Critical"></Label>
                                <Label class="dashed-line"></Label>
                                <Label v-model="data_critical">{{data_critical}}</Label>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Label text="Cases / million"></Label>
                                <Label class="dashed-line"></Label>
                                <Label v-model="data_cases_per_million">{{data_cases_per_million}}</Label>
                            </FlexboxLayout>
                        </FlexboxLayout>
                    </FlexboxLayout>
                </GridLayout>
            </FlexboxLayout>
        </RadSideDrawer>
    </Page>
</template>

<script>
    import App from '~/components/App.vue'
    import Signup from '~/screens/Signup.vue'
    import Settings from '~/screens/Settings.vue'
    import Overview from '~/screens/Overview.vue'
    import Timeline from '~/screens/Timeline.vue'
    import Watchlist from '~/screens/Watchlist.vue'
    import About from '~/screens/About.vue'

    import * as http from "http"; /* Used for api calls */
    import * as application from 'application'; /* Used to get IP address */ 

    export default {
        props: ['user'],
        data: function() {
            return {
                searchbarIsShown: false,
                searchPhrase: "",
                current_country: 'Belgium',
                data_cases: 0,
                data_cases_today: 0,
                data_deaths: 0,
                data_deaths_today: 0,
                data_recovered: 0,
                data_active: 0,
                data_critical: 0,
                data_cases_per_million: 0
            }
        },
        methods:{
            goToOverview() {
                this.$navigateTo(Overview);
            },
            goToTimeline() {
                this.$navigateTo(Timeline);
            },
            goToWatchlist() {
                this.$navigateTo(Watchlist);
            },
            goToSettings() {
                this.$navigateTo(Settings);
            },
            goToAbout(){
                this.$navigateTo(About);
            },
            logOut(){
                const firebase = require('nativescript-plugin-firebase')
                firebase.logout()
                .then(() => this.$navigateTo(App) )
            },
            gotoWebWHO() {
                const utilsModule = require("tns-core-modules/utils/utils");
                utilsModule.openUrl("https://www.who.int/")
            },
            toggleSearchbar(){
                this.searchbarIsShown = !this.searchbarIsShown
                if(!this.searchbarIsShown){
                    this.$refs.searchBar.nativeView.dismissSoftInput()
                    this.$refs.searchBar.nativeView.android.clearFocus()
                }
                console.log(this.searchbarIsShown)
            },
            onSubmit(){
                this.searchbarIsShown = false
                this.$refs.searchBar.nativeView.dismissSoftInput()
                this.$refs.searchBar.nativeView.android.clearFocus()
                this.makeApiCall(this.searchPhrase)
                this.current_country = this.searchPhrase
                this.searchPhrase = ""
            },
            logUserProp(){
                console.log(this.user)
            },
            makeApiCall(country) {
                http.getJSON(`https://corona.lmao.ninja/countries/${country}`)
                .then(result => {
                    this.data_cases = result.cases 
                    this.data_cases_today = result.todayCases 
                    this.data_deaths = result.deaths 
                    this.data_deaths_today = result.todayDeaths
                    this.data_recovered = result.recovered
                    this.data_active = result.active
                    this.data_critical = result.critical
                    this.data_cases_per_million = result.casesPerOneMillion
                }, error => {
                    console.log(error);
                });
            }
        },
        components: {
            App,
            Signup,
            Settings,
            Timeline,
            Watchlist,
            About
        },
        beforeMount(){
            this.makeApiCall('belgium')
        }
    };
</script>

<style scoped>
    .content{
        margin: 0% 5% 18% 5%;
    }

    .searchbar_wrap{
        flex: 1;
        justify-content: center;
        align-items: center;
        width: 100%;
    }

    .searchbar_wrap *{
        font-size: 15rem;
    }

    .title{
        display: flex;
        align-self: flex-start;
        font-family: "Serenity", "Serenity-Medium";
        font-weight: bold;
        font-size: 45rem;
    }

    .menu{
        width: 100%;
        height: 20%;
        margin-top: 5%;
        align-items: flex-start;
    }

    .menu-icon{
        width: 70px;
        height: 70px;
    }

    .current_country{
        text-transform: capitalize;
    }

    .data-list{
        flex: 1;
        font-family: 'Serenity', 'Serenity-ExtraLight';
        font-weight: 200;
        width: 100%;
    }

    .data-item{
        font-size: 30rem;
    }

    .dashed-line{
        flex: 1;
        border-bottom-color: rgba(100, 100, 100, 0.25);
        border-bottom-width: 5px;
    }

    .WHO{
        margin-bottom: 50px;
        text-decoration: underline;
    }

    .drawer-menu-icon{
        width: 75px;
        margin-left: 16;
        margin-bottom: 30;
    }

    .drawer-item {
        padding: 8 16;
        color: #1a223f;
        font-size: 26;
        font-family: 'Serenity', 'Serenity-DemiBold';
        font-weight: 600;
    }
    
    .drawer-screens,.drawer-other{
        margin-top: 35;
    }

    #active{
        background-color: red;
    }
</style>