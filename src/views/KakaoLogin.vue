<template>
  <div>
    <div v-if="Object.keys(userInfo).length == 0">
      <a id="custom-login-btn" @click="kakaoLogin()">
        <img
          src="//k.kakaocdn.net/14/dn/btqCn0WEmI3/nijroPfbpCa4at5EIsjyf0/o.jpg"
          width="222"
        />
      </a>
    
    </div>
    <div v-else>
        카카오 nickname : {{userInfo.id}} <br>
        카카오 email : {{userInfo.pwd}} <br><br>
       <button type="button" @click="kakaoLogout" class="btn btn-danger">카카오 로그아웃</button>
     </div>
  </div>
</template>
<script>
// import axios from 'axios'

export default {
 computed: {
    userInfo() {
      return this.$store.state.user;
    }
  },
  methods: {
    kakaoLogin() {
      window.Kakao.Auth.login({
        scope: "profile_nickname, account_email",
        success: this.getKakaoAccount,
      });
    },
    getKakaoAccount() {
      window.Kakao.API.request({
        url: "/v2/user/me",
        success: (res) => {
          const kakao_account = res.kakao_account;
          const nickname = kakao_account.profile.nickname; //카카오 닉네임
          const email = kakao_account.email; //카카오 이메일

          console.log("nickname", nickname);
          console.log("email", email);
          //로그인 처리 구현

          console.log(kakao_account);
          // this.$store.commit("user", kakao_account);
          this.$store.commit('user', {"id":nickname, "pwd":email})

          alert("로그인 성공!");
           
        },
        fail: (error) => {
          // this.$router.push("/errorPage");
          console.log(error);
        },
      });
    },
     kakaoLogout() {
      if (!window.Kakao.Auth.getAccessToken()) {
        console.log("Not logged in.");
        return;
      }
      window.Kakao.Auth.logout((response) => {
        //로그아웃
        console.log("access token:", window.Kakao.Auth.getAccessToken());
        console.log("log out:", response);

        this.$store.commit('user', {})

      });
    },
  },
};
</script>