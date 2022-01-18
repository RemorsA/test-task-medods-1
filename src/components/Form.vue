<template>
  <!-- Начальная регистрация данных -->
  <div class="container-form">
    <div class="container-input">
      <div class="container-input-data">
        <h5 v-if="v$.form.lastName.$error">
          {{v$.form.lastName.$errors[0].$message}}
        </h5>
        <input class="input-data" type="text" placeholder="Фамилия" v-model="form.lastName">
      </div>
      <div class="container-input-data">
        <h5 v-if="v$.form.firstName.$error">
          {{v$.form.firstName.$errors[0].$message}}
        </h5>
        <input class="input-data" type="text" placeholder="Имя" v-model="form.firstName">
      </div>
      <div class="container-input-data">
        <h5 v-if="v$.form.middleName.$error">
          {{v$.form.middleName.$errors[0].$message}}
        </h5>
        <input class="input-data" type="text" placeholder="Отчество" v-model="form.middleName">
      </div>
      <div class="container-input-data">
        <h5 v-if="v$.form.dateBirth.$error">
          {{v$.form.dateBirth.$errors[0].$message}}
        </h5>
        <input class="input-data-date" type="date" v-model="form.dateBirth">
      </div>
      <div class="container-input-data">
        <h5 v-if="v$.form.phone.$error">
          {{v$.form.phone.$errors[0].$message}}
        </h5>
        <input class="input-data" type="text" placeholder="Номер телефона" v-model="form.phone">
      </div>
      <div class="container-checkbox">
        <select v-model="form.selectedSex">
          <option disabled value="">Ваш пол</option>
          <option value="male">Муж.</option>
          <option value="female">Жен.</option>
        </select>
        <input class="checkbox-sms" type="checkbox">
        <span>Не отправлять sms</span>
      </div>
    </div>
  </div>
  <!-- Выбор лечащего врача и его группы клиентов -->
  <div class="container-selector">
    <div class="selector">
      <p class="p" @click="areDoctor = !areDoctor">{{selected}}</p>
      <div class="doctor" v-if="areDoctor">
        <h4 v-for="doctor in doctors" :key='doctor.value' @click="selectDoctor(doctor)">
          {{doctor.name}}
        </h4>
      </div>
      <div class="clients">
        <div class="btn-clients" v-for="client in clients" :key="client.id">
          <button class='btn' @click="toggleClient(client.id)" :class="{'btn-color': selectClients.find(c => c.id === client.id)}">
            {{client.value}}
          </button>
        </div>
      </div>
    </div>
  </div>
  <!-- Данные о месте проживания -->
  <div class="container-address">
    <div class="container-input-address">
      <div class="container-data-address">
        <input class="input-data" type="text" placeholder="Индекс" v-model="address.index">
      </div>
      <div class="container-data-address">
        <input class="input-data" type="text" placeholder="Стран" v-model="address.country">
      </div>
      <div class="container-data-address">
        <input class="input-data" type="text" placeholder="Область" v-model="address.region">
      </div>
      <div class="container-data-address">
        <h5 v-if="v$.address.city.$error">
          {{v$.address.city.$errors[0].$message}}
        </h5>
        <input class="input-data" type="text" placeholder="Город" v-model="address.city">
      </div>
      <div class="container-data-address">
        <input class="input-data" type="text" placeholder="Улица" v-model="address.street">
      </div>
      <div class="container-data-address">
        <input class="input-data" type="text" placeholder="Дом" v-model="address.home">
      </div>
    </div>
  </div>
  <!-- Паспортные данные -->
  <div class="container-passport">
    <div class="container-passport-input">
      <div class="passport-data-vFor">
        <span class="btn-openCloseDocuments" @click="openCloseDocuments = !openCloseDocuments">{{selectDocument}}</span>
        <div class="container-vFor-types" v-if="openCloseDocuments">
          <div class="vFor-types" v-for="typeDocument in typesDocument" :key="typeDocument.id">
            <h4 @click="selectType(typeDocument)">{{typeDocument.name}}</h4>
          </div>
        </div>
      </div>
      <div class="passport-data">
        <input  class='input-data' type="text" placeholder="Серия" v-model="passport.series">
      </div>
      <div class="passport-data">
        <input  class='input-data' type="text" placeholder="Номер" v-model="passport.numbers">
      </div>
      <div class="passport-data">
        <textarea class="input-data-textarea" placeholder="Кем выдан" v-model="passport.whoIssuedIt"></textarea>
      </div>
      <div class="passport-data">
        <h5 v-if="v$.passport.dateOfIssue.$error">
          {{v$.passport.dateOfIssue.$errors[0].$message}}
        </h5>
        <input  class='input-data-date' type="date" placeholder="Дата выдачи" v-model="passport.dateOfIssue">
      </div>
    </div>
  </div>
  <div class="container-save-btn">
    <button class="save-btn" @click="addUser">Сохранить</button>
  </div>
</template>

<script>
import useVuelidate from '@vuelidate/core'
import { required , minLength, maxLength, helpers} from '@vuelidate/validators'

const phoneValidate = (value) => value.match(/^\+?(\d{1,3})?[- .]?\(?(?:\d{2,3})\)?[- .]?\d\d\d[- .]?\d\d\d\d$/)

export default {
  data() {
    return {
      v$: useVuelidate(),

      form: {
        lastName: '',
        firstName: '',
        middleName: '',
        dateBirth: '',
        phone: '+7',
        selectedSex: '',
      },

      address: {
        index: '',
        country: '',
        region: '',
        city: '',
        stree: '',
        home: ''
      },

      passport: {
        series: '',
        numbers: '',
        whoIssuedIt: '',
        dateOfIssue: ''
      },
      typesDocument: [
        {id: 1, name: 'Паспорт'},
        {id: 2, name: 'Свид. о рождении'},
        {id: 3, name: 'Вод. удостоверение'}
      ],
      openCloseDocuments: false,
      selectDocument: 'Выберите тип документа',
      arrayObjectsWithErrorTypeDocument: [],

      clients: [
        {value: 'VIP', id: 1},
        {value: 'Проблемные', id: 2},
        {value: 'ОМС', id: 3}
      ],
      selectClients: [],

      doctors: [
        {name: 'Иванов', value: 1,},
        {name: 'Захаров', value: 2},
        {name: 'Чернышева', value: 3}
      ],
      selected: 'Выберите лечащего врача',
      areDoctor: false,
    }
  },
  validations() {
    return {
      form: {
        lastName: { 
          required: helpers.withMessage('Обязательно для заполнения', required),
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 10 символов`, maxLength(10)),
        },
        firstName: { 
          required: helpers.withMessage('Обязательно для заполнения', required),
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 10 символов`, maxLength(10)),
        },
        middleName: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 10 символов`, maxLength(10)),
        },
        dateBirth: {
          required: helpers.withMessage('Обязательно для заполнения', required),
        },
        phone: {
          required: helpers.withMessage('Обязательно для заполнения', required),
          phoneValidate: helpers.withMessage(() => `Ошибка, не верный номер телефона`, phoneValidate)
        },
      },
      address: {
        index: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 1 символов`, minLength(1)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 6 символов`, maxLength(6)),
        },
        country: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 30 символов`, maxLength(30)),
        },
        region: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 30 символов`, maxLength(30)),
        },
        city: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 30 символов`, maxLength(30)),
          required: helpers.withMessage('Обязательно для заполнения', required),
        },
        stree: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 30 символов`, maxLength(30)),
        },
        home: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 30 символов`, maxLength(30)),
        }
      },
      passport: {
        series: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 1 символов`, minLength(1)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 4 символов`, maxLength(4)),
        },
        numbers: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 1 символов`, minLength(1)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 6 символов`, maxLength(6)),
        },
        whoIssuedIt: {
          minLength: helpers.withMessage(() => `Ошибка, не меньше 2 символов`, minLength(2)),
          maxLength: helpers.withMessage(() => `Ошибка, не больше 30 символов`, maxLength(150)),
        },
        dateOfIssue: {
          required: helpers.withMessage('Обязательно для заполнения', required),
        },
      }
    }
  },
  methods: {
    addUser() {
      this.v$.$validate()
      if (this.selectClients.length === 0) {
        alert('Вы не выбрали категорию `VIP, Проблемные или ОМС`')
        return
      }
      if (this.arrayObjectsWithErrorTypeDocument.id === undefined) {
        alert('Вы не выбрали тип документа')
        return
      }
      if (
        this.v$.form.$error ||
        this.v$.address.$error ||
        this.v$.passport.$error) {
          alert('Ошибка, проверьте введенные данные')
          return
        } else {
          alert('Вы зарегестрировались')
        }
    },
    selectDoctor(doctor) {
      this.selected = doctor.name
      this.areDoctor = false
    },
    addClient(id) {
      this.selectClients.push(this.clients.find(t => t.id === id))
    },
    deleteClient(id) {
      this.selectClients = this.selectClients.filter(t => t.id !== id)
    },
    toggleClient(id) {
      if (this.selectClients.find(c => c.id === id)) {
        this.deleteClient(id)
      } else {
        this.addClient(id)
      }
    },
    selectType(typeDocument) {
      this.selectDocument = typeDocument.name
      this.openCloseDocuments = false
      this.arrayObjectsWithErrorTypeDocument = typeDocument
    }
  },
}
</script>

<style lang="sass" scoped>
  @import url('https://fonts.googleapis.com/css2?family=Comfortaa:wght@300&family=Oswald:wght@200&display=swap')
  @import "@/sass/_variables.sass"
</style>
