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

                    <FlexboxLayout class="content" flexDirection="column" justifyContent="space-between" alignItems="center">
                        <StackLayout class="title">
                            <Label text="Get on board" />
                        </StackLayout>

                        <FlexboxLayout class="signup-form" flexDirection="column">
                            <Label v-model="signup_error" :text="signup_error" class="signup_error" textWrap="true"/>

                            <TextField class="signup-name" v-model="signup_name" hint="Name" autocorrect="false" />
                            <TextField class="signup-email" v-model="signup_email" hint="E-mail" autocorrect="false" keyboardType="email" />
                            <TextField class="signup-password" v-model="signup_password" hint="Password" autocorrect="false" secure='true'/>
                            <TextField class="signup-confirm-password" v-model="signup_confirm_password" hint="Confirm Password" autocorrect="false" secure='true'/>
                        </FlexboxLayout>

                        <Label textWrap='true' class="TOS">
                            <FormattedString>
                                <span>By creating an account, you agree to the </span>
                                <span class="link">Terms and Conditions</span>
                                <span> and </span>
                                <span class="link">Privacy Policy.</span>
                            </FormattedString>
                        </Label>

                        <Button class="signup-button" text="Sign Up" @tap="signUp()"></Button>
                            
                        <Label class="has-account" @tap="goToLogin()" text="I am already a member" />
                    </FlexboxLayout>
                </GridLayout>
            </FlexboxLayout>
        </RadSideDrawer>
    </Page>
</template>

<script>

    import App from '~/components/App.vue'
    import Settings from '~/screens/Settings.vue'
    import Overview from '~/screens/Overview.vue'
    import Timeline from '~/screens/Timeline.vue'
    import Watchlist from '~/screens/Watchlist.vue'

    export default {
        data() {
            return {
                signup_email: "",
                signup_password: "",
                signup_confirm_password: "",
                signup_name: "",
                signup_error: ""
            }
        },
        methods:{
            signUp() {
                const firebase = require('nativescript-plugin-firebase')
                
                if( this.signup_email != "" && 
                    this.signup_name != "" && 
                    this.signup_password != "" && 
                    this.signup_confirm_password != "" /* Check if all fields are filled */
                ){
                    if(this.signup_password == this.signup_confirm_password){ /* Check if passwords match eachother */
                        // Create User
                        return firebase.createUser({
                            email: this.signup_email,
                            password: this.signup_password
                        })
                        .then(
                            result => {
                                console.log("SUCCES: " + JSON.stringify(result))
                                this.signup_error = ""

                                this.$navigateTo(Overview, {
                                        props: {
                                            user: JSON.stringify(result)
                                        }
                                    }
                                )
                            }
                        )
                        .catch(
                            error => {
                                console.log("ERROR: " + error)
                                this.signup_error = error
                            }
                        );
                    }
                }
            },
            goToLogin(){
                this.$navigateTo(App);
            }
        },
        components: {
            App,
            Settings,
            Overview,
            Timeline,
            Watchlist
        }
    };
</script>

<style scoped>
    .content{
        margin: 25% 5% 18% 5%;
    }

    .title{
        display: flex;
        align-self: flex-start;
        font-family: "Serenity", "Serenity-Medium";
        font-weight: bold;
        font-size: 45rem;
    }

    .signup-form{
        width: 100%;
    }

    .signup-form *{
        font-size: 20rem;
        margin: 20px 0;
    }

    .TOS{
        color: #929292;
        font-size: 13rem;
        width: 65%;
        text-align: center;
    }

    .TOS .link{
        text-decoration: underline;
    }

    .signup-button{
        border-radius: 20px;
        font-size: 20rem;
        padding: 5% 35%;
        background-image: linear-gradient(to right, #8537CC, #5B41DC);
    }

    .has-account{
        font-size: 15rem;
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
