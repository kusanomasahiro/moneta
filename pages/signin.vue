<template>
  <v-card class="elevation-12 mt-8">
    <v-toolbar dark color="primary">
      <v-toolbar-title>口座開設</v-toolbar-title>
    </v-toolbar>
    <v-card-text>
      <v-form v-model="valid">
        <v-select
          label="銀行"
          item-text="name"
          item-value="id"
          :items="banks"
          :value="bankId"
          :rules="bankRules"
          @input="$store.commit('signin/bankId', $event)"
        />
        <v-select
          label="支店"
          item-text="name"
          item-value="id"
          :items="branches(bankId)"
          :value="branchId"
          :rules="branchRules"
          @input="$store.commit('signin/branchId', $event)"
        />
        <v-text-field
          label="ユーザーＩＤ"
          type="text"
          :value="user"
          :rules="userRules"
          @input="$store.commit('signin/user', $event)"
        />
        <v-text-field
          label="お名前"
          type="text"
          :value="name"
          :rules="nameRules"
          @input="$store.commit('signin/name', $event)"
        />
        <v-text-field
          label="パスワード"
          :value="password"
          :rules="passwordRules"
          @input="$store.commit('signin/password', $event)"
        />
      </v-form>
    </v-card-text>
    <v-card-actions>
      <v-spacer />
      <v-btn :disabled="!valid"  @click.stop="signin()" color="primary">口座開設 </v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
import { mapGetters } from "vuex";
import md5 from "blueimp-md5";

export default {
  layout: "login",
  data: () => ({
    valid: false,
    passwordRules: [
      v => v && v.length >= 4 || "パスワードは4文字以上です",
      v => /[0-9]/.test(v) || "パスワードは1文字以上数字を含みます",
    ],
    bankRules:[
      v => v != undefined || "銀行を選択してください"
    ],
    branchRules:[
　　　 v => v != undefined || "支店を選択してください"
    ],
    userRules:[
　　　 v => v != undefined || "IDを入力してください"
    ],
    nameRules:[
　　　 v => v != undefined || "お名前を入力してください"
    ]   
  }),
  computed: {
    ...mapGetters("signin", [
      "bankId",
      "branchId",
      "user",
      "name",
      "password",
      "account",
      "statement",
    ]),
    ...mapGetters("banks", ["banks"]),
    ...mapGetters("branches", ["branches"]),

    accounts() {
      return this.$store.getters["accounts/accounts"]("ALL");
    },
    hashedPassword() {
      return md5(this.password);
    },
    account() {
      return this.accounts.find(a => a.user === this.user);
    },
  },
  
  methods: {
    async signin() {
      this.accounts;
      if (this.account != undefined) {
        alert('そのIDは使えません')
      }else{
        await this.$store.dispatch("signin/signin");
        this.$router.push("/");
      }
    },
  },
};
</script>
