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
                    <Label class="drawer-item" text="About" @tap="About()"/>
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
                                <SearchBar ref="searchBar" hint="Search hint" :text="searchPhrase" @submit="onSubmit()" color="#1a223f" backgroundColor="white"/>
                            </FlexboxLayout>
                        </FlexboxLayout>
                        
                        <FlexboxLayout class="title" justifyContent="space-between" width="100%" marginBottom="50px">
                            <Label text="Watchlist" />
                            <Image src="~/assets/images/heart-true.png" stretch="aspectFit" width="100px"></Image>
                        </FlexboxLayout>

                        <FlexboxLayout class="data-list" flexDirection="column">
                            <FlexboxLayout class="data-item">
                                <Image src="~/assets/images/menu.png" width="80px" class="country-flag"></Image>
                                <Label text="Belgium" class="country-name"></Label>
                                <Image src="~/assets/images/back-arrow.png" width="30px" class="country-arrow"></Image>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Image src="~/assets/images/menu.png" width="80px" class="country-flag"></Image>
                                <Label text="Belgium" class="country-name"></Label>
                                <Image src="~/assets/images/back-arrow.png" width="30px" class="country-arrow"></Image>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Image src="~/assets/images/menu.png" width="80px" class="country-flag"></Image>
                                <Label text="Belgium" class="country-name"></Label>
                                <Image src="~/assets/images/back-arrow.png" width="30px" class="country-arrow"></Image>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Image src="~/assets/images/menu.png" width="80px" class="country-flag"></Image>
                                <Label text="Belgium" class="country-name"></Label>
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
    import App from '~/components/App.vue'
    import Signup from '~/screens/Signup.vue'
    import Settings from '~/screens/Settings.vue'
    import Overview from '~/screens/Overview.vue'
    import Timeline from '~/screens/Timeline.vue'
    import Watchlist from '~/screens/Watchlist.vue'

    export default {
        data: function() {
            return {
                searchbarIsShown: false
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
            About(){
                console.log("ABOUT")
            },
            logOut(){
                this.$navigateTo(App);
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
                console.log('sumbitted search')
                this.searchbarIsShown = false
                this.$refs.searchBar.nativeView.dismissSoftInput()
                this.$refs.searchBar.nativeView.android.clearFocus()
            }
        },
        components: {
            App,
            Signup,
            Settings,
            Overview,
            Timeline,
            Watchlist
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
    }

    .country-name{
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