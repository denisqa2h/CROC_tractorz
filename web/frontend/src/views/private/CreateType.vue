<template>
  <div class="app-main-layout">
    <NavBar />
    <main class="app-content" :class="{full: !$store.state.visible}">
      <div class="app-page">


        <div>
          <div class="page-title">
            <h3>Добавление типа происшествий</h3>
          </div>

          <section>
            <div class="user-container">
              <p class="flow-text">Название происшествия:</p>
              <div class="input-field col s6 user">
                <input name="type" type="text" class="validate" v-model="type"/>
              </div>
            </div>
            <button class="green darken-3 btn" @click="save">Сохранить</button>
            <button class="teal lighten-2 btn rghtbtn" @click="back">Отмена</button>
          </section>
        </div>

      </div>
    </main>
  </div>
</template>

<script>
import Axios from 'axios';
import NavBar from '@/components/NavBar';


export default {
  data() {
    return {
      type: ''
    }
  },
  components: {
    NavBar,
  },
  methods: {
    onClick() {
      this.visible = !this.visible;
    },
    back() {
      this.$router.push('/administration')
    },
    async save() {
      await Axios.post(`http://127.0.0.1:8000/api/v1/AccidentsType`, {'TypeName': this.type}, {
        headers: {
          Authorization: 'Bearer ' + localStorage.getItem('access')
        }
      })
          .then((res) => {
            this.$router.push('/administration');
          })
          .catch((e) => {
            console.log('error', e);
            if(e === 'Error: Request failed with status code 401') {
              localStorage.clear();
              this.$router.push('/login');
            }
          })
    }
  }
}
</script>

<style scoped>
.user-container {
  display: flex;
}
.rghtbtn {
  margin-left: 30px;
}
</style>
