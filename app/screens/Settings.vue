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
                            <Label text="Settings" />
                        </StackLayout>

                        <FlexboxLayout class="settings_wrap">
                            <FlexboxLayout class="setting">
                                <Label text="Name" class="settings-label"></Label>
                                <Label :text="this.current_user.displayName || 'No display name found'" class="settings-value"></Label>
                            </FlexboxLayout>

                            <FlexboxLayout class="setting">
                                <Label text="Email" class="settings-label"></Label>
                                <Label :text="this.current_user.email || 'No email found'" class="settings-value"></Label>
                            </FlexboxLayout>

                            <FlexboxLayout class="setting">
                                <Label text="Night Mode" class="settings-label"></Label>
                                <Switch checked="true" class="settings-value" />
                            </FlexboxLayout>

                            <FlexboxLayout class="reset_password_wrap">
                                <Label class="reset_password" text="Reset Password" @tap="resetMyPassword()"></Label>
                                <Label :text="this.resetPasswordText"></Label>
                            </FlexboxLayout>

                            <Label class="log_out" text="Log Out" @tap="logOut()"></Label>
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
        props: ['user','country'],
        data() {
            return {
                current_user: this.user,
                current_country: this.country,
                resetPasswordText: ""
            }
        },
        methods:{
            resetMyPassword() {                
                const firebase = require("nativescript-plugin-firebase");
                console.log('RESET PASSWORD')
                this.resetPasswordText = "Coming soon"
                setTimeout(() => this.resetPasswordText = "", 3500)
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
        color: white;
    }

    .title{
        display: flex;
        align-self: flex-start;
        font-family: "Serenity", "Serenity-Medium";
        font-weight: bold;
        font-size: 45rem;
        margin-bottom: 50px;
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

    .settings_wrap{
        flex-direction: column;
        width: 100%;
    }

    .setting{
        justify-content: space-between;
        flex-direction: row;
        align-items: center;
        margin-bottom: 10rem;
        padding: 0;
        font-size: 16rem;
    }

    .settings-label{
        color: #929292;
    }

    .reset_password_wrap{
        align-items: flex-start;
        margin-bottom: 20px;
    }

    .reset_password{
        text-decoration: underline;
        font-size: 16rem;
        margin-right: 15px;
    }

    .log_out{
        text-decoration: underline;
        font-size: 16rem;
        margin-bottom: 20px;
    }
</style>