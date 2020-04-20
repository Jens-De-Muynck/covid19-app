<template>
    <Page actionBarHidden="true">

        <RadSideDrawer ref="drawer">
            <StackLayout ~drawerContent backgroundColor="#ffffff">
                <FlexboxLayout marginTop="4%" width="100%">
                    <Image class="drawer-menu-icon" src="~/assets/images/menu-open.png" stretch="aspectFit" @tap="$refs.drawer.nativeView.closeDrawer()" />
                </FlexboxLayout>

                <FlexboxLayout class="drawer-screens" flexDirection="column">
                    <Label class="drawer-item" text="Overview" @tap="$refs.drawer.nativeView.closeDrawer()"/>
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

                    <FlexboxLayout class="content">
                        <FlexboxLayout class="menu" flexDirection="column">
                            <FlexboxLayout class="menu">                            
                                <Image class="menu-icon" src="~/assets/images/menu.png" stretch="aspectFit" @tap="$refs.drawer.nativeView.showDrawer()" />
                                <Image class="menu-icon" src="~/assets/images/search.png" stretch="aspectFit" @tap="toggleSearchbar()"/>
                                <Image class="menu-icon" src="~/assets/images/settings.png" stretch="aspectFit" @tap="goToSettings()"/>
                            </FlexboxLayout>

                            <FlexboxLayout v-if='searchbarIsShown' class="searchbar_wrap">
                                <SearchBar ref="searchBar" hint="Search country" v-model="searchPhrase" @submit="onSubmit()"/>
                            </FlexboxLayout>
                        </FlexboxLayout>
                        
                        <FlexboxLayout class="title">
                            <Label class="current_country" v-model="current_country" @tap="makeApiCall('belgium')">{{current_country}}</Label>
                            <Image :src="this.isInWatchlist ? '~/assets/images/eye-remove.png' : '~/assets/images/eye-add.png'" stretch="aspectFit" width="100px" @tap="handleWatchlist(false)"></Image>
                        </FlexboxLayout>

                        <FlexboxLayout class="data-list">
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
    import * as http from "http"; /* Used for api calls */
    
    import App from '~/components/App.vue'
    import Signup from '~/screens/Signup.vue'
    import Settings from '~/screens/Settings.vue'
    import Overview from '~/screens/Overview.vue'
    import Timeline from '~/screens/Timeline.vue'
    import Watchlist from '~/screens/Watchlist.vue'
    import About from '~/screens/About.vue'

    export default {
        props: ['user', 'country'],
        data: function() {
            return {
                searchbarIsShown: false,
                searchPhrase: "",
                data_cases: 0,
                data_cases_today: 0,
                data_deaths: 0,
                data_deaths_today: 0,
                data_recovered: 0,
                data_active: 0,
                data_critical: 0,
                data_cases_per_million: 0,
                current_user: this.user,
                current_country: this.country || 'belgium',
                isInWatchlist: false,
                current_watchlist: []
            }
        },
        methods:{
            goToTimeline() {
                this.$navigateTo(
                    Timeline,
                    {
                        props: {
                            user: this.current_user,
                            country: this.current_country
                        }
                    }
                );
            },
            goToWatchlist() {
                this.$navigateTo(
                    Watchlist, 
                    {
                        props: {
                            user: this.current_user,
                            country: this.current_country
                        }
                    }
                );
            },
            goToSettings() {
                this.$navigateTo(
                    Settings,
                    {
                        props: {
                            user: this.current_user,
                            country: this.current_country
                        }
                    }
                );
            },
            goToAbout(){
                this.$navigateTo(About);
            },
            logOut(){
                const firebase = require('nativescript-plugin-firebase')
                this.current_user = null

                firebase.logout()
                .then(() => this.$navigateTo(App) )
                .catch(error => console.log("couldn't logout: " + error));
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
            },
            onSubmit(){
                this.searchbarIsShown = false
                this.$refs.searchBar.nativeView.dismissSoftInput()
                this.$refs.searchBar.nativeView.android.clearFocus()
                this.makeApiCall(this.searchPhrase)
                this.current_country = this.searchPhrase
                this.searchPhrase = ""

                this.handleWatchlist(true)
            },
            makeApiCall(country) {
                // console.log("Start Fetching Api...");

                http.getJSON(`https://corona.lmao.ninja/v2/countries/${country}`)
                .then(result => {
                    // console.log("Filling API data...");

                    // console.log(`(with =>`);
                    // console.log(result);
                    // console.log(`)`);
                    
                    this.data_cases = this.numberWithCommas(result.cases) 
                    this.data_cases_today = this.numberWithCommas(result.todayCases) 
                    this.data_deaths = this.numberWithCommas(result.deaths) 
                    this.data_deaths_today = this.numberWithCommas(result.todayDeaths)
                    this.data_recovered = this.numberWithCommas(result.recovered)
                    this.data_active = this.numberWithCommas(result.active)
                    this.data_critical = this.numberWithCommas(result.critical)
                    this.data_cases_per_million = this.numberWithCommas(result.casesPerOneMillion)
                })
                .catch(error => console.log("API Error: " + error));
            },
            numberWithCommas(x) { // add dividers to large numbers for better readability
                return x.toString().replace(/\B(?<!\.\d*)(?=(\d{3})+(?!\d))/g, ".");
            },
            handleWatchlist(clientOnly){
                // console.log('-- START HANDLING WATCHLIST --')
                const firebase = require("nativescript-plugin-firebase");
                let functionIsDone = false

                firebase.init();

                firebase.getValue(`/users/${this.current_user.uid}/watchlist`)
                .then(result => {
                    this.current_watchlist = result.value || {}
                })
                .then(() => {

                    let temp_watchlist = Object.entries(this.current_watchlist)
                    
                    for (let i = temp_watchlist.length-1; i >= 0; i--) {
                        if(temp_watchlist[i][1] == this.current_country){
                            this.isInWatchlist = true
                    
                            if(!clientOnly){
                                // Remove from db watchlist
                                firebase.remove(`/users/${this.current_user.uid}/watchlist/${temp_watchlist[i][0]}`);

                                // Remove from local watchlist
                                temp_watchlist.splice[i, 1]

                                this.isInWatchlist = false
                                functionIsDone = true
                            }
                        }
                    }

                    if(!(this.isInWatchlist) && !functionIsDone){
                        this.isInWatchlist = false

                        if(!clientOnly){
                            // Add to db watchlist
                            firebase.push(
                                `/users/${this.current_user.uid}/watchlist`,
                                this.current_country
                            ).then(
                                function (result) {
                                    // console.log("created key: " + result.key);
                                }
                            );

                            // Add to local watchlist
                            temp_watchlist.push(this.current_country)

                            this.isInWatchlist = true
                        }
                    }
                })
                .catch(error => {
                    console.log("Error: " + error)
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
            this.makeApiCall(this.current_country)
            this.handleWatchlist(true)
        }
    };
</script>

<style scoped>
    .content{
        margin: 0% 5% 18% 5%;
        flex-direction: column; 
        justify-content: space-between;
        align-items: center;
        color: white;
    }

    .searchbar_wrap{
        flex: 1;
        justify-content: center;
        align-items: center;
        width: 100%;
    }

    .searchbar_wrap *{
        font-size: 15rem;
        color: #1a223f;
        background-color: white;
    }

    .title{
        display: flex;
        justify-content: space-between;
        align-self: flex-start;
        font-family: "Serenity", "Serenity-Medium";
        font-weight: bold;
        font-size: 45rem;
        width: 100%;
        margin-bottom: 50px;
    }

    .menu{
        width: 100%;
        height: 20%;
        margin-top: 5%;
        flex-direction: row;
        justify-content: space-between;
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
        flex-direction: column;
        justify-content: space-between;
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