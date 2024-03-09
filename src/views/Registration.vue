<template>
  <div id="content">
    <h1>Вход в систему</h1>
    <hr>
    <form id="form" @sumbit.prevent="logIn">
      <div id="username">
        <label for="usernameInput">Введите логин:</label>
        <input type="text"
               id="usernameInput"
               required
               placeholder="Логин"
               v-model.trim="username"/>
      </div>
      <div id="password">
        <label for="passwordInput">Введите пароль:</label>
        <input type="password"
               id="passwordInput"
               required
               placeholder="Пароль"
               v-model.trim="password"/>
      </div>
      <div id="buttons">
        <Button color="blue" label="Войти в систему" @click.native="logIn"/>
        <Button color="white" style="color: black" label="Зарегистрироваться" @click.native="register"/>
      </div>
    </form>
  </div>
</template>

<script>
import Button from "@/components/Button";

export default {
  name: "Registration",
  components: {
    Button,
  },
  data(){
    return{
      username: "",
      password: ""
    }
  },
  methods: {
    logIn(e) {
      e.preventDefault();

      async function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
      }

      this.$axios.post('http://localhost:24620/api/auth', {
        username: this.username,
        password: this.password
      }).then(async response => {
        // Сохраняем токен в localStorage
        localStorage.setItem("jwt", response.data.token);

        // Ждем 2 секунды перед перенаправлением на главную страницу
        await sleep(1000);

        // Перенаправляем на главную страницу
        this.$router.push({ name: 'app-page' });
      }).catch(error => {
        this.AxiosErrorHandler(error.response.data);
      });
    },
    register(e){
      e.preventDefault();
      this.$axios.post('http://localhost:24620/api/registration', {
        username: this.username,
        password: this.password
      }).then(() => {
        this.$notify({
          group: 'success',
          title: 'Регистрация',
          text: 'Вы теперь можете войти',
          type: 'success'
        });
      }).catch(error => {
        this.AxiosErrorHandler(error.response.data);
      });
    },
    AxiosErrorHandler(errorText){
      this.$notify({
        group: 'error',
        title: 'Error',
        text: errorText,
        type: 'error'
      })
    }
  }
}
</script>

<style scoped>
#form button {
  margin: 20px 10px 10px 10px;
}
#username, #password {
  margin: 5px;
}

#form {
  position: relative;
  font-size: 20px;
  flex-direction: column;
  margin: 30px auto auto;
  background-color: ghostwhite;
  padding: 20px;
  display: table;
  text-align: center;
  box-shadow: 0 0 10px 1px #7fffff;
  border-radius: 10px;
}

input {
  margin-left: 13px;
  padding: 5px 0 5px 2px;
}


</style>