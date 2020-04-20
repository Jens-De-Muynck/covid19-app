<template>
    <Page actionBarHidden="true">
        <RadSideDrawer ref="drawer">
            <StackLayout ~drawerContent backgroundColor="#ffffff">
                <FlexboxLayout marginTop="4%" width="100%">
                    <Image class="drawer-menu-icon" src="~/assets/images/menu-open.png" stretch="aspectFit" @tap="$refs.drawer.nativeView.closeDrawer()" />
                </FlexboxLayout>

                <FlexboxLayout class="drawer-screens" flexDirection="column">
                    <Label class="drawer-item" text="Overview" @tap="goToOverview()" />
                    <Label class="drawer-item" text="Timeline" @tap="$refs.drawer.nativeView.closeDrawer()" />
                    <Label class="drawer-item" text="Watchlist" @tap="goToWatchlist()" />
                </FlexboxLayout>

                <FlexboxLayout class="drawer-other" flexDirection="column">
                    <Label class="drawer-item" text="Settings" @tap="goToSettings()" />
                    <Label class="drawer-item" text="About" @tap="goToAbout()" />
                    <Label class="drawer-item" text="Log Out" @tap="logOut()" />
                </FlexboxLayout>
            </StackLayout>

            <FlexboxLayout backgroundColor="#1A223F" flexDirection="column" ~mainContent>
                <GridLayout rows="*, auto, auto">
                    <FlexboxLayout justifyContent="space-between" alignItems="center" flexDirection="column" height="100%" >
                        <Image src="~/assets/images/bg-shape.png" iosOverflowSafeArea="true" stretch="aspectFit" width="101%" rowSpan="3" ></Image>
                        <Label class="WHO" text="World Health Organisation" @tap="gotoWebWHO()" />
                    </FlexboxLayout>

                    <FlexboxLayout class="content">
                        <FlexboxLayout class="menu" flexDirection="column">
                            <FlexboxLayout class="menu" flexDirection="row" justifyContent="space-between" >
                                <Image class="menu-icon" src="~/assets/images/menu.png" stretch="aspectFit" @tap="$refs.drawer.nativeView.showDrawer()" />
                                <Image class="menu-icon" src="~/assets/images/search.png" stretch="aspectFit" @tap="toggleSearchbar()" />
                                <Image class="menu-icon" src="~/assets/images/settings.png" stretch="aspectFit" @tap="goToSettings()"/>
                            </FlexboxLayout>

                            <FlexboxLayout v-if="searchbarIsShown" class="searchbar_wrap">
                                <SearchBar ref="searchBar" hint="Search hint" v-model="searchPhrase" @submit="onSubmit()" color="#1a223f" backgroundColor="white"/>
                            </FlexboxLayout>
                        </FlexboxLayout>

                        <FlexboxLayout class="title">
                            <Label text="Timeline" />
                            <Image src="~/assets/images/heart-true.png" stretch="aspectFit" width="100px"></Image>
                        </FlexboxLayout>

                        <FlexboxLayout class="data-list">
                            <FlexboxLayout class="data-item">
                                <Label text="Cases"></Label>
                                <Image :src="timeline_cases" stretch="aspectFit"/>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Label text="Deaths"></Label>
                                <Image :src="timeline_deaths" stretch="aspectFit"/>
                            </FlexboxLayout>

                            <FlexboxLayout class="data-item">
                                <Label text="Recovered"></Label>
                                <Image :src="timeline_recovered" stretch="aspectFit"/>
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

import App from "~/components/App.vue";
import Signup from "~/screens/Signup.vue";
import Settings from "~/screens/Settings.vue";
import Overview from "~/screens/Overview.vue";
import Timeline from "~/screens/Timeline.vue";
import Watchlist from "~/screens/Watchlist.vue";
import About from "~/screens/About.vue";

export default {
    props: ["user", "country"],
    data: function() {
        return {
            searchbarIsShown: false,
            searchPhrase: '',
            current_user: this.user,
            current_country: this.country,
            timeline_cases: "",
            timeline_deaths: "",
            timeline_recovered: ""
        };
    },
    methods: {
        goToOverview() {
            this.$navigateTo(Overview, {
                props: {
                    user: this.current_user,
                    country: this.current_country,
                },
            });
        },
        goToWatchlist() {
            this.$navigateTo(Watchlist, {
                props: {
                    user: this.current_user,
                    country: this.current_country,
                },
            });
        },
        goToSettings() {
            this.$navigateTo(Settings);
        },
        goToAbout() {
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
            utilsModule.openUrl("https://www.who.int/");
        },
        toggleSearchbar() {
            this.searchbarIsShown = !this.searchbarIsShown;
            if (!this.searchbarIsShown) {
                this.$refs.searchBar.nativeView.dismissSoftInput();
                this.$refs.searchBar.nativeView.android.clearFocus();
            }
            console.log(this.searchbarIsShown);
        },
        onSubmit() {
            this.searchbarIsShown = false;
            this.$refs.searchBar.nativeView.dismissSoftInput();
            this.$refs.searchBar.nativeView.android.clearFocus();
            console.log(this.searchPhrase)
            this.current_country = this.searchPhrase
            this.goToOverview()
        },
        getTimeline(req) {
            let data_string =
                "https://image-charts.com/chart?chco=FFFFFF&chd=t:";
            let data_points = [];

            http.getJSON(
                `https://corona.lmao.ninja/v2/historical/${this.current_country}`
            )
            .then((result) => {
                console.log("IMAGE REQUEST");
                // console.log(req)
                // console.log(result.timeline[req])
                let current_timeline = result.timeline[req];

                // Put all points in array
                for (var key in current_timeline) {
                    if (current_timeline.hasOwnProperty(key)) {
                        // console.log(current_timeline[key]);
                        data_points.push(current_timeline[key]);
                    }
                }

                // Add points from array to the data string
                let highest_number = Math.max(...data_points);
                for (var point in data_points) {
                    data_string += Math.round(
                        (100 / highest_number) * data_points[point]
                    );
                    data_string += ",";
                }

                // Remove last comma
                data_string = data_string.slice(0, -1);
                // Add ending of data string
                data_string += "&chs=700x200&cht=ls&chf=bg,s,00000000"

                console.log(data_string);

                if(req == "cases") this.timeline_cases = data_string
                if(req == "deaths") this.timeline_deaths = data_string
                if(req == "recovered") this.timeline_recovered = data_string
            })
            .then(() => {return data_string})
            .catch((error) => console.log(error));
        },
    },
    components: {
        App,
        Signup,
        Settings,
        Overview,
        Timeline,
        Watchlist,
    },
    beforeMount() {
        // Fetch image charts
        this.getTimeline('cases')
        this.getTimeline('deaths')
        this.getTimeline('recovered')
    },
};
</script>

<style scoped>
.content {
    margin: 0% 5% 18% 5%;
    flex-direction: column; 
    justify-content: space-between;
    align-items: center;
}

.searchbar_wrap {
    flex: 1;
    justify-content: center;
    align-items: center;
    width: 100%;
}

.searchbar_wrap * {
    font-size: 15rem;
}

.title {
    display: flex;
    justify-content: space-between;
    align-self: flex-start;
    font-family: "Serenity", "Serenity-Medium";
    font-weight: bold;
    font-size: 45rem;
    width: 100%;
    margin-bottom: 50px;
}

.menu {
    width: 100%;
    height: 20%;
    margin-top: 5%;
    align-items: flex-start;
}

.menu-icon {
    width: 70px;
    height: 70px;
}

.data-list {
    flex: 1;
    flex-direction: column;
    justify-content: space-between;
    font-family: "Serenity", "Serenity-ExtraLight";
    font-weight: 200;
    width: 100%;
}

.data-item {
    font-size: 30rem;
    flex-direction: column;
}

.dashed-line {
    flex: 1;
    border-bottom-color: rgba(100, 100, 100, 0.25);
    border-bottom-width: 5px;
}

.WHO {
    margin-bottom: 50px;
    text-decoration: underline;
}

.drawer-menu-icon {
    width: 75px;
    margin-left: 16;
    margin-bottom: 30;
}

.drawer-item {
    padding: 8 16;
    color: #1a223f;
    font-size: 26;
    font-family: "Serenity", "Serenity-DemiBold";
    font-weight: 600;
}

.drawer-screens,
.drawer-other {
    margin-top: 35;
}

#active {
    background-color: red;
}
</style>
