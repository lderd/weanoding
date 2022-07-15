<template>
  <el-dialog custom-class="signup-dialog" title="회원가입" v-model="state.dialogVisible" @close="handleClose">
    <el-form :model="state.form" :rules="state.rules" ref="signupForm" :label-position="state.form.align">
      <el-form-item prop="id" label="아이디" :label-width="state.formLabelWidth" >
        <el-input v-model="state.form.id" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item prop="username" label="유저이름" :label-width="state.formLabelWidth" >
        <el-input v-model="state.form.username" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item prop="password" label="비밀번호" :label-width="state.formLabelWidth">
        <el-input v-model="state.form.password" autocomplete="off" show-password></el-input>
      </el-form-item>
      <el-form-item prop="password2" label="비밀번호 확인" :label-width="state.formLabelWidth">
        <el-input v-model="state.form.password2" autocomplete="off" show-password></el-input>
      </el-form-item>
    </el-form>
    <template #footer>
      <span class="dialog-footer">
        <el-button type="primary" @click="clickIDCheck">ID 중복체크</el-button>
      </span>
      <span class="dialog-footer">
        <el-button type="primary" :disabled="state.disabled" @click="clickSignup">회원가입</el-button>
      </span>
    </template>
  </el-dialog>
</template>
<style>
.signup-dialog {
  width: 400px !important;
  height: 400px;
}
.signup-dialog .el-dialog__headerbtn {
  float: right;
}
.signup-dialog .el-form-item__content {
  margin-left: 0 !important;
  float: right;
  width: 200px;
  display: inline-block;
}
.signup-dialog .el-form-item {
  margin-bottom: 20px;
}
.signup-dialog .el-form-item__error {
  font-size: 12px;
  color: red;
}
.signup-dialog .el-input__suffix {
  display: none;
}
.signup-dialog .el-dialog__footer {
  margin: 0 calc(50% - 80px);
  padding-top: 0;
  display: inline-block;
}
.signup-dialog .dialog-footer .el-button {
  width: 120px;
}
</style>
<script>
import { reactive, computed, ref, onMounted } from 'vue'
import { useStore } from 'vuex'

export default {
  name: 'signup-dialog',

  props: {
    open: {
      type: Boolean,
      default: false
    }
  },

  setup(props, { emit }) {
    const store = useStore()
    // 마운드 이후 바인딩 될 예정 - 컨텍스트에 노출시켜야함. <return>
    const signupForm = ref(null)

    /*
      // Element UI Validator
      // rules의 객체 키 값과 form의 객체 키 값이 같아야 매칭되어 적용됨
      //
    */
    const state = reactive({
      form: {
        id: '',
        username: '',
        password: '',
        align: 'left'
      },
      rules: {
        id: [
          { required: true, message: 'Please input ID', trigger: 'blur' },
          {max: 16, message: "id 최대 16자까지", trigger: 'blur'},
        ],
        username: [
          { required: true, message: 'Please input username', trigger: 'blur' },
          {max: 30, message: "name 최대 30자까지", trigger: 'blur'}
        ],
        password: [
          { required: true, message: 'Please input password', trigger: 'blur' },
          {min:9, max: 16, message: "password 최소 9자에서 최대 16자까지", trigger: 'blur'},
          {validate: (rule, value, callback) => {
            let patternEngAtListOne = new RegExp(/[a-zA-Z]+/);
            let patternSpeAtListOne = new RegExp(/[~!@#$%^&*()_+|<>?:{}`]+/);
            let patternNumAtListOne = new RegExp(/[0-9]+/);

            let reg =
            patternEngAtListOne.test(value) &&
            patternSpeAtListOne.test(value) &&
            patternNumAtListOne.test(value);
              if (!reg) {
                callback(new Error('비밀번호는 영문, 숫자, 특수문자가 조합되어야 합니다.'))
              }
            },
            trigger: 'blur'
          }
        ],
        password2: [
          { required: true, message: 'please enter the same password again.', trigger: 'blur' },
          {min:9, max: 16, message: "password 최소 9자에서 최대 16자까지", trigger: 'blur'},
          {validate: (rule, value, callback) => {
            let patternEngAtListOne = new RegExp(/[a-zA-Z]+/);
            let patternSpeAtListOne = new RegExp(/[~!@#$%^&*()_+|<>?:{}`]+/);
            let patternNumAtListOne = new RegExp(/[0-9]+/);

            let reg =
            patternEngAtListOne.test(value) &&
            patternSpeAtListOne.test(value) &&
            patternNumAtListOne.test(value);
              if (!reg) {
                callback(new Error('비밀번호는 영문, 숫자, 특수문자가 조합되어야 합니다.'))
              }
              let reg2 = value == state.form.password
              if(!reg2) {
                callback(new Error('please enter the same password again.'))
              }
            },
            trigger: 'blur'
          },
        ],
      },
      dialogVisible: computed(() => props.open),
      formLabelWidth: '120px',
      disabled: 'disabled'
    })

    onMounted(() => {
      console.log(signupForm.value)
    })

    const clickSignup = function () {
      // 로그인 클릭 시 validate 체크 후 그 결과 값에 따라, 로그인 API 호출 또는 경고창 표시
      console.log('submit')
      console.log(signupForm.value.validate.valid)
      signupForm.value.validate((valid) => {
        console.log('제출')
        console.log(valid)
        if (valid) {
          console.log('submit')
          store.dispatch('root/requestSignup', { id: state.form.id, name: state.form.username, password: state.form.password})
          .then(function (result) {
            alert('accessToken: ' + "회원가입이 되었습니다!!")
            handleClose();
            clickLogin();
          })
          .catch(function (err) {
            alert(err)
          })
        } else {
          alert('Validate error!')
        }
      });
    }

    const clickLogin = () => {
      console.log("clickLogin")
      emit('openLoginDialog')
    }

    const clickIDCheck = () => {
      console.log("clickIDCheck")
      store.dispatch('root/requestIDCheck', state.form.id)
      .then(result => {
        if (result.status === 200) {
          alert('사용가능한 ID입니다')
          state.disabled = false
        }
      })
      .catch(err => {
        console.log(err.response.status)
        if (err.response.status === 409) {
          alert('이미 존재하는 ID입니다')
        }else if (err.response.status === 401) {
          alert('인증에 실패하였습니다')
        }
        state.disabled = 'disabled'
      })
    }

    const handleClose = function () {
      console.log("closeSignupDialog");
      state.form.username = ''
      state.form.id = ''
      state.form.password = ''
      state.form.password2 = ''
      emit('closeSignupDialog')
    }

    return { signupForm, state, clickSignup, clickLogin, handleClose, clickIDCheck }
  }
}
</script>
