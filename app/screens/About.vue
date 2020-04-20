<template>
    <Page actionBarHidden="true">
        <RadSideDrawer ref="drawer">

            <StackLayout ~drawerContent backgroundColor="#ffffff">
                <Label class="drawer-header" text="Drawer"/>

                <Label class="drawer-item" text="Item 1"/>
                <Label class="drawer-item" text="Item 2"/>
                <Label class="drawer-item" text="Item 3"/>
            </StackLayout>

            <FlexboxLayout backgroundColor="#1A223F" flexDirection="column" ~mainContent>
                
                <GridLayout rows="*, auto, auto">
                    <FlexboxLayout alignItems="flex-start">
                        <Image src="~/assets/images/bg-shape.png" iosOverflowSafeArea="true" stretch="aspectFit" width="100%" rowSpan="3"></Image>
                    </FlexboxLayout>

                    <FlexboxLayout class="content">
                        <FlexboxLayout class="menu">
                            <Image class="menu-icon" src="~/assets/images/back-arrow.png" stretch="aspectFit" @tap="$navigateBack()"/>
                        </FlexboxLayout>

                        <StackLayout class="title">
                            <Label text="About" />
                        </StackLayout>

                        <FlexboxLayout class="about_wrap">
                            <Label text="This is the about"/>
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
    import Overview from '~/screens/Overview.vue'
    import Timeline from '~/screens/Timeline.vue'
    import Watchlist from '~/screens/Watchlist.vue'

    export default {
        data() {
            return {
                
            }
        },
        methods:{
            resetPassword() {
                console.log("RESET PASSWORD")
            },
            logOut(){
                const firebase = require('nativescript-plugin-firebase')
                firebase.logout()
                .then(() => this.$navigateTo(App))
            }
        },
        components: {
            App,
            Signup,
            Overview,
            Timeline,
            Watchlist
        }
    };
</script>

<style scoped>
    .content{
        margin: 0 5% 18% 5%;
        flex-direction: column;
        justify-content: flex-start;
        align-items: center;
    }

    .title{
        display: flex;
        align-self: flex-start;
        font-family: "Serenity", "Serenity-Medium";
        font-weight: bold;
        font-size: 45rem;
        margin-bottom: 50px;
    }

    .about_wrap{
        flex-direction: column;
        width: 100%;
    }

    .menu{
        width: 100%;
        height: 20%;
        margin-top: 5%;
        flex-direction: row;
        justify-content: flex-start;
        align-items: flex-start;
    }

    .menu-icon{
        width: 40px;
    }

    .setting{
        justify-content: space-between;
        align-items: center;
        margin-bottom: 10rem;
        padding: 0;
        font-size: 16rem;
    }

    .settings-label{
        color: #929292;
    }

    .link{
        text-decoration: underline;
        font-size: 16rem;
        margin-bottom: 20px;
    }
</style>