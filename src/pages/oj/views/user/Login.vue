<template>
  <div>
    <div class="modal-login">
      <div class="left-login">
        <!-- <carousel :slides="slides"></carousel> -->
        <img :src="`https://picsum.photos/id/${idImage}/200/300`" />
      </div>
      <div class="form">
        <Form ref="formLogin" :model="formLogin" :rules="ruleLogin">
          <FormItem prop="username">
            <Input type="text" v-model="formLogin.username" :placeholder="$t('m.LoginUsername')" size="large" @on-enter="handleLogin">
            <Icon type="ios-person-outline" slot="prepend"></Icon>
            </Input>
          </FormItem>
          <FormItem prop="password">
            <Input type="password" v-model="formLogin.password" :placeholder="$t('m.LoginPassword')" size="large" @on-enter="handleLogin">
            <Icon type="ios-lock-outline" slot="prepend"></Icon>
            </Input>
          </FormItem>
          <FormItem prop="tfa_code" v-if="tfaRequired">
            <Input v-model="formLogin.tfa_code" :placeholder="$t('m.TFA_Code')">
            <Icon type="ios-lightbulb-outline" slot="prepend"></Icon>
            </Input>
          </FormItem>
          <Button
            type="primary"
            @click="handleLogin"
            class="btn" long
            :loading="btnLoginLoading">
            {{$t('m.UserLogin')}}
          </Button>
          <br>
        </Form>
        <el-row class="login-method">
            <el-col>
              <!-- <img src="../../../../../static/img/GoogleLogo.png" class="logo-icon"/> -->
            </el-col>
            <el-col>
              <!-- <img src="../../../../../static/img/FacebookLogo.png" style="width: 30px;height: 30px; position: absolute; margin-left: 25%;" /> -->
              <!-- 326022817735322 -->
              <facebookLogin class="button"
                appId="314930319788683"
                @login="getUserData"
                @logout="onLogout"
                @get-initial-status="getUserData" 
                >
              </facebookLogin>
            </el-col>
            <el-col>
              <!-- <img src="../../../../../static/img/GithubLogo.png" class="logo-icon"/> -->
            </el-col>
          </el-row>
      </div>
    </div>
    <div class="footer">
      <a v-if="website.allow_register" @click.stop="handleBtnClick('register')">{{$t('m.No_Account')}}</a>
      <a @click.stop="goResetPassword" style="float: right">{{$t('m.Forget_Password')}}</a>
    </div>
  </div>
</template>

<script>
  import { mapGetters, mapActions } from 'vuex'
  import api from '@oj/api'
  import { FormMixin } from '@oj/components/mixins'
  import facebookLogin from 'facebook-login-vuejs'
  // import Carousel from '../../components/Carousel.vue'
  export default {
    mixins: [FormMixin],
    components: {
      facebookLogin
      // Carousel
    },
    data () {
      const CheckRequiredTFA = (rule, value, callback) => {
        if (value !== '') {
          api.tfaRequiredCheck(value.toLowerCase()).then(res => {
            this.tfaRequired = res.data.data.result
          })
        }
        callback()
      }
      return {
        tfaRequired: false,
        btnLoginLoading: false,
        idImage: 1,
        formLogin: {
          username: '',
          password: '',
          tfa_code: ''
        },
        ruleLogin: {
          username: [
            {required: true, trigger: 'blur'},
            {validator: CheckRequiredTFA, trigger: 'blur'}
          ],
          password: [
            {required: true, trigger: 'change', min: 6, max: 20}
          ]
        }
      }
    },
    methods: {
      ...mapActions(['changeModalStatus', 'getProfile']),
      handleBtnClick (mode) {
        this.changeModalStatus({
          mode,
          visible: true
        })
      },
      getUserData (user) {
        // const { authResponse } = new Promise(user.FB.login)
        // if (!authResponse) return
        // apiAuthenticate(authResponse.accessToken);
        console.log('log info:', user)
      },
      loginWithF (title) {
        console.log(title)
      },
      handleLogin () {
        this.validateForm('formLogin').then(valid => {
          this.btnLoginLoading = true
          let formData = Object.assign({}, this.formLogin)
          if (!this.tfaRequired) {
            delete formData['tfa_code']
          }
          api.login(formData).then(res => {
            this.btnLoginLoading = false
            this.changeModalStatus({visible: false})
            this.getProfile()
            this.$success(this.$i18n.t('m.Welcome_back'))
          }, _ => {
            this.btnLoginLoading = false
          })
        })
      },
      goResetPassword () {
        this.changeModalStatus({visible: false})
        this.$router.push({name: 'apply-reset-password'})
      }
    },
    computed: {
      ...mapGetters(['website', 'modalStatus']),
      visible: {
        get () {
          return this.modalStatus.visible
        },
        set (value) {
          this.changeModalStatus({visible: value})
        }
      }
    }
  }
</script>

<style scoped lang="less">
  .footer {
    overflow: auto;
    margin-top: 20px;
    margin-bottom: -15px;
    text-align: left;
    justify-content: center;
    a{
      color: aliceblue;
    }
  }
  .form{
    float: left;
  }
  .left-login{
    width: 50%;
    max-height: 500px;
    min-height: 300px;
    img{
      width: 100%;
      border-radius: 12px;
      box-shadow: 1px 2px 5px #eee275;
    }
  }
  .btn {
      margin: auto;
    }
  .modal-login{
    display: flex;
    justify-content: space-between;
    Form{
      margin-top: 10%;
    }
  }
  .logo-icon{
    width: 30px;
    height: 30px;
    border-radius: 50%
  }

  .login-method{
    margin: 10% auto;
    display: flex;
    justify-content: space-around;
    width: 60%;
  }
</style>
