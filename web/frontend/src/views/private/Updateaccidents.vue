<template>
  <div class="app-main-layout">
    <NavBar />
    <main class="app-content" :class="{full: !$store.state.visible}">
      <div class="app-page">


        <div>
          <div class="page-title">
            <h3>Редактирование происшествия</h3>
          </div>

          <section>
            <table class="tableWidth">
              <tbody>
                <tr>
                  <td>Номер поста</td>
                  <td>{{ this.$store.state.post }}</td>
                </tr>
                <tr>
                  <td>Время фиксации</td>
                  <td>{{ this.$store.state.fixtime }}</td>
                </tr>
              </tbody>
            </table>
            <div class="row">
              <p class="flow-text">Время устранения</p>
              <div class="input-field col s12">
                <input class="textSize"  v-model="EndTime" type="datetime-local">
              </div>
            </div>
            <div class="row">
              <p class="flow-text">Тип происшествия</p>
              <div class="accidents">
                <div class="accident btn" v-for="value in type" @click="addType(value)">{{ value.TypeName }}</div>
              </div>
            </div>
            <div class="row">
              <p class="flow-text">Описание</p>
              <div class="input-field col s12">
                <textarea id="textarea2" class="materialize-textarea textSize" data-length="120" v-model="Description" placeholder="Описание просишествия"></textarea>
              </div>
            </div>
            <div class="groupbtn">
              <button class="green darken-3 btn" @click="update">Сохранить</button>
              <button class="teal lighten-2 btn rghtbtn" @click="back">Отмена</button>
            </div>
          </section>
        </div>

      </div>
    </main>
  </div>
</template>

<script>
import {Form, Field, ErrorMessage } from 'vee-validate';
import * as yup from 'yup';
import Axios from 'axios';
import NavBar from '@/components/NavBar';

export default {
  data() {
    const schema = yup.object({
      login: yup.string().required("Поле обязательно для заполнения"),
    });
    return {
      schema,
      Description: this.$store.state.description,
      EndTime: this.$store.state.endtime,
      type: null,
      typeName: null,
    }
  },
  components: {
    Field,
    Form,
    ErrorMessage,
    NavBar
  },
  mounted() {
    Axios
        .get('http://127.0.0.1:8000/api/v1/AccidentsType', {
          headers: {
            Authorization: 'Bearer ' + localStorage.getItem('access')
          }
        })
        .then((response) => {
          this.type = response.data;
        })
        .catch((e) => {
          console.log('error ', e);
          if(e === 'Error: Request failed with status code 401') {
            localStorage.clear();
            this.$router.push('/login');
          }
        });
  },
  methods: {
    onClick() {
      this.visible = !this.visible;
    },
    addType(value) {
      this.typeName = value.TypeName;
    },
    back() {
      this.$router.push('/monitoring');
    },
    async update() {
      await Axios
          .patch(`http://localhost:8000/api/v1/Accidents/` + this.$store.state.accidentid, {'Description': this.Description, 'EndTime': this.EndTime}, {
            headers: {
              Authorization: 'Bearer ' + localStorage.getItem('access')
            },
          })
          .then(() => {
            this.$router.push('/monitoring');
          }).catch((e) => {
            console.log('error', e);
            if(e === 'Error: Request failed with status code 401') {
              localStorage.clear();
              this.$router.push('/login');
            }
          });
    }
  }
}
</script>

<style scoped>
.accidents {
  display: flex;
}
.accident {
  margin-left: 10px;
  background: white;
  color: black;
}

.activeclass {
  background: aqua;
}
.accident:first-child {
  margin-left: 0;
}
.row{
  margin-top: 30px;
}
.textSize {
  max-width: 500px;
}
.tableWidth {
  max-width: 600px;
}
.rghtbtn {
  margin-left: 30px;
}
.groupbtn {
  margin-top: 30px;
}

:focus::-webkit-input-placeholder {
  color: transparent
}

:focus::-moz-placeholder {
  color: transparent
}

:focus:-moz-placeholder {
  color: transparent
}

:focus:-ms-input-placeholder {
  color: transparent
}
</style>
