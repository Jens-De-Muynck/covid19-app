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
                        <StackLayout class="title">
                            <Label text="Hello there," />
                            <Label text="welcome back" />
                        </StackLayout>

                        <FlexboxLayout class="login-form" flexDirection="column">
                            <Label v-model="login_error" :text="login_error" class="login_error" textWrap="true"/>
                            <TextField class="login-email" v-model="login_email" hint="E-mail" autocorrect="false" keyboardType="email" />
                            <TextField class="login-password" v-model="login_password" hint="Password" autocorrect="false" secure='true'/>
                            
                            <Label class="forgot-password" text="Forgot your password?" @tap="forgotPassword()"/>
                        </FlexboxLayout>

                        <Button class="login-button" text="Sign In" @tap="signIn()"></Button>
                            
                        <Label class="has-account" @tap="goToSignup()">
                            <FormattedString>
                                <Span text="New here? " />
                                <Span text="Sign up instead." class="sign-up-instead" />
                            </FormattedString>
                        </Label>
                    </FlexboxLayout>
                </GridLayout>
            </FlexboxLayout>
        </RadSideDrawer>
    </Page>
</template>

<script >

    import Signup from '~/screens/Signup.vue'
    import Settings from '~/screens/Settings.vue'
    import Overview from '~/screens/Overview.vue'
    import Timeline from '~/screens/Timeline.vue'
    import Watchlist from '~/screens/Watchlist.vue'
    
    export default {
        data() {
            return {
                login_email: "",
                login_password: "",
                login_error: ""
            }
        },
        methods:{
            goToSignup() {
                this.$navigateTo(Signup);
            },
            forgotPassword() {
                const utilsModule = require("tns-core-modules/utils/utils");
                utilsModule.openUrl("https://docs.nativescript.org/core-concepts/utils")
            },
            signIn(){
                const firebase = require('nativescript-plugin-firebase')
                
                if(this.login_email != "" && this.login_password != ""){
                    return firebase.login({
                        type: firebase.LoginType.PASSWORD,
                        passwordOptions: {
                            email: this.login_email,
                            password: this.login_password
                        }
                    })
                    .then(
                        result => {
                            console.log("SUCCES:")
                            console.log(result)
                            this.login_error = ""

                            this.$navigateTo(Overview, {
                                    props: {
                                        user: result
                                    }
                                }
                            )
                        }
                    )
                    .catch(
                        error => {
                            console.log("ERROR: " + error)
                            this.login_error = error
                        }
                    );
                }
            }
            
        },
        components: {
            Signup,
            Settings,
            Overview,
            Timeline,
            Watchlist
        }
    }
</script>

<style scoped>
    .content{
        margin: 25% 5% 18% 5%;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;
    }

    .title{
        display: flex;
        align-self: flex-start;
        font-family: "Serenity", "Serenity-Medium";
        font-weight: bold;
        font-size: 45rem;
    }

    .login-form{
        width: 100%;
    }

    .login-form *{
        font-size: 20rem;
        margin: 20px 0;
    }

    .login_error{
        font-size: 10rem;
        color: red;
    }

    .forgot-password{
        color: #929292;
        text-decoration: underline;
        display: flex;
        align-self: flex-end;
        font-size: 13rem;
    }

    .login-button{
        border-radius: 20px;
        font-size: 20rem;
        padding: 5% 35%;
        background-image: linear-gradient(to right, #8537CC, #5B41DC);
    }

    .has-account{
        font-size: 15rem;
    }

    .sign-up-instead{
        text-decoration: underline;
    }

    .drawer-header {
        padding: 50 16 16 16;
        margin-bottom: 16;
        background-color: #53ba82;
        color: #ffffff;
        font-size: 24;
    }

    .drawer-item {
        padding: 8 16;
        color: #333333;
        font-size: 16;
    }
</style>
