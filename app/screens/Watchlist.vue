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
                    <Label class="drawer-item" text="Watchlist" @tap="$refs.drawer.nativeView.closeDrawer()"/>
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
                            <Label text="Watchlist" />
                            <Image src="~/assets/images/heart-true.png" stretch="aspectFit" width="100px"></Image>
                        </FlexboxLayout>

                        <FlexboxLayout class="data-list" flexDirection="column">
                            
                            <FlexboxLayout class="data-item" v-for='country in this.current_watchlist' :key="country" @tap="tappedWatchlistItem(country)">
                                <Image :src="country.flag" width="80px" class="country-flag"></Image>
                                <Label class="country-name">{{country.name}}</Label>
                                <Image src="~/assets/images/back-arrow.png" width="30px" class="country-arrow"></Image>
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
        props: ['user','country'],
        data: function() {
            return {
                searchbarIsShown: false,
                searchPhrase: '',
                current_user: this.user,
                current_country: this.country,
                current_watchlist: []
            }
        },
        methods:{
            goToOverview() {
                this.$navigateTo(
                    Overview,
                    {
                        props: {
                            user: this.current_user,
                            country: this.current_country
                        }
                    }
                );
            },
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
            goToSettings() {
                this.$navigateTo(Settings);
            },
            goToAbout(){
                this.$navigateTo(About);
            },
            logOut() {
                const firebase = require("nativescript-plugin-firebase");
                this.current_user = null
                    
                firebase.logout().then(() => this.$navigateTo(App))
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
                console.log(this.searchbarIsShown)
            },
            onSubmit(){
                this.searchbarIsShown = false;
                this.$refs.searchBar.nativeView.dismissSoftInput();
                this.$refs.searchBar.nativeView.android.clearFocus();
                this.current_country = this.searchPhrase
                this.goToOverview()
            },
            getWatchlist(){
                console.log("CURRENT USER")
                console.log(this.current_user.uid)
                const firebase = require('nativescript-plugin-firebase')

                firebase.init({
                    persist: true
                });

                firebase.query(
                    function() {}, // Mandatory for signature
                    `/users`,
                    {
                        singleEvent: true,
                        orderBy: {
                            type: firebase.QueryOrderByType.KEY
                        },
                        range: {
                            type: firebase.QueryRangeType.EQUAL_TO,
                            value: this.current_user.uid
                        },
                        limit: {
                            type: firebase.QueryLimitType.FIRST,
                            value: 1
                        }   
                    }
                )
                .then(result => {
                    if(Object.keys(result.value).length !== 0){ // User bestaat
                    
                        console.log("Query Succeeded:")

                        // Loop through all existing users
                        for (let i = 0; i < Object.keys(result.value).length; i++){
                            // DATABASE user id ==? CURRENT user id
                            if(Object.keys(result.value)[0] == this.current_user.uid){
                                // Put all items in watchlist in a variable array
                                var country_arr = Object.values(result.value[this.current_user.uid].watchlist)
                                country_arr.forEach(country_item => {

                                    // Get flag src
                                    console.log("Searching Flag...");

                                    http.getJSON(`https://corona.lmao.ninja/v2/countries/${country_item}`)
                                    .then(result => {
                                        let flag_item = result.countryInfo.flag

                                        let current_country_data = {
                                            "name": country_item,
                                            "flag": flag_item
                                        }

                                        this.current_watchlist.push(current_country_data)

                                        console.log(current_country_data);
                                    })
                                    .catch(error => console.log("API Error: " + error));
                                });
                            }
                        }

                    }
                    else{ // User bestaat nog niet
                        console.log("Query Failed")
                    }
                })
                .catch(error => console.log("UserID Error: " + error));
            },
            tappedWatchlistItem(selected_country){
                this.current_country = selected_country.name
                this.goToOverview()
            }
        },
        components: {
            App,
            Signup,
            Settings,
            Overview,
            Timeline,
            Watchlist
        },
        beforeMount(){
            this.getWatchlist()
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

    .data-list{
        flex: 1;
        font-family: 'Serenity', 'Serenity-ExtraLight';
        font-weight: 200;
        width: 100%;
    }

    .data-item{
        background-color: rgba(0, 0, 0, 0.25);
        display: flex;
        align-items: center;
        font-size: 24rem;
        padding: 20px 35px;
        margin-bottom: 30px;
    }

    .country-flag{
        border-width: 1px;
        border-color: rgba(255, 255, 255, 0.55);
        border-style: solid;
    }

    .country-name{
        text-transform: capitalize;
        flex: 1;
        padding-left: 30px;
    }

    .country-arrow{
        transform: rotate(180deg);
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