<template>
  <div>
    <v-layout class="cards_layout">
    <v-flex xs12 sm4 offset-sm4 v-if="card_arr.length == 0">
    <h2 class="txt_msg"><i>No Cards created yet...create your first card</i></h2>
    </v-flex>
      <v-flex xs12 sm2 offset-sm1 v-for="(card, index) in card_arr" :key="card.id">
        <v-card class="card_blk">
          <v-card-media class="card_photo">
            <img :src="card.image" height="200"/>
          </v-card-media>

          <v-card-title primary-title>
            <div>
              <h3 class="mb-0">{{card.name}} -- {{card.id}}</h3>
              <div>{{card.role}}</div>
            </div>
          </v-card-title>
          <v-card-actions class="btns">
            <v-btn small color="orange" @click="edit(card.id)">Edit</v-btn>
            <v-btn small color="red" class="edit_btn" @click="delete_card(index)">Delete</v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
    <v-divider></v-divider>
    <v-layout class="add_card_layout">
      <v-flex xs12 sm4 offset-sm4>
          <center><h2 v-if="edit_card">Update Card</h2><h2 v-else>Add Card</h2></center>
          <form>
            <div class="add_card_blk">
              <v-text-field
                v-model="name"
                :error-messages="nameErrors"
                label="Name"
                required
                @input="$v.name.$touch()"
                @blur="$v.name.$touch()"
              ></v-text-field>
              <v-text-field
                v-model="role"
                :error-messages="roleErrors"
                label="Role"
                required
                @input="$v.role.$touch()"
                @blur="$v.role.$touch()"
              ></v-text-field>
              <input
                type="file"
                style="display: none"
                ref="image"
                accept="image/*"
                @change="onFilePicked"
              >
              <center><span><img :src="imageUrl" height="150" v-if="imageUrl"/></span></center>
              <center><v-btn small color='white' @click="upload">Upload Image</v-btn></center>
            </div>
          <div class="btns-blk">
            <v-btn v-if="edit_card" color='pink' @click="update">update</v-btn>
            <v-btn v-else color='green' @click="submit">submit</v-btn>
            <v-btn color='blue' @click="clear" ref="clear_btn">clear</v-btn>
          </div>
        </form>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>

import Vue from 'vue'
import Vuetify from 'vuetify'
import { validationMixin } from 'vuelidate'
import { required } from 'vuelidate/lib/validators'

Vue.use(Vuetify)

export default {
  name: 'HelloWorld',
  mixins: [validationMixin],
  validations: {
    name: { required },
    role: { required }
  },
  data: () => ({
    card_arr: [],
    name: '',
    role: '',
    imageName: '',
    imageFile: '',
    imageUrl: '',
    id: 1,
    edit_card: false,
    edited_id: 0
  }),
  computed: {
    nameErrors () {
      const errors = []
      if (!this.$v.name.$dirty) return errors
      !this.$v.name.required && errors.push('Name is required.')
      return errors
    },
    roleErrors () {
      const errors = []
      if (!this.$v.role.$dirty) return errors
      !this.$v.role.required && errors.push('Role is required')
      return errors
    }
  },
  methods: {
    submit () {
      this.$v.$touch()
      if (this.name !== '' && this.role !== '' && this.imageUrl !== '') {
        let id = this.id++
        let obj = { id: id, name: this.name, role: this.role, image: this.imageUrl }
        this.card_arr.push(obj)
        this.clear()
        console.log(this.card_arr)
      } else {
        alert('All fields are required')
      }
    },
    clear () {
      this.$v.$reset()
      this.name = ''
      this.role = ''
      this.imageUrl = ''
      this.edit_card = false
    },
    upload () {
      this.$refs.image.click()
    },
    onFilePicked (e) {
      const files = e.target.files
      if (files[0] !== undefined) {
        this.imageName = files[0].name
        if (this.imageName.lastIndexOf('.') <= 0) {
          return
        }
        const fr = new FileReader()
        fr.readAsDataURL(files[0])
        fr.addEventListener('load', () => {
          this.imageUrl = fr.result
          this.imageFile = files[0] // this is an image file that can be sent to server...
        })
      } else {
        this.imageName = ''
        this.imageFile = ''
        this.imageUrl = ''
      }
    },
    edit (id) {
      for (var i = 0; i < this.card_arr.length; i++) {
        if (this.card_arr[i].id === id) {
          this.edit_card = true
          this.edited_id = this.card_arr[i].id
          this.name = this.card_arr[i].name
          this.role = this.card_arr[i].role
          this.imageUrl = this.card_arr[i].image
        }
      }
    },
    update () {
      for (var i = 0; i < this.card_arr.length; i++) {
        if (this.card_arr[i].id === this.edited_id) {
          this.edit_card = false
          this.card_arr[i].name = this.name
          this.card_arr[i].role = this.role
          this.card_arr[i].image = this.imageUrl
          this.clear()
        }
      }
    },
    delete_card (index) {
      if (index > -1) {
        this.card_arr.splice(index, 1)
      }
    }
  }
}
</script>

<style scoped>
.btns {
  margin-left: 4%;
}
.btns-blk {
  text-align: center;
}
.add_card_blk {
  border: 2px solid #6f786d;
  padding: 10%;
  background-color: #71c6c3;
  border-radius: 5px;
}
.add_card_layout {
  margin-top: 1%;
}
.cards_layout {
  margin-bottom: 1%;
}
.txt_msg {
  color: green;
  opacity: 0.7;
}
.card_photo {
  padding: 15px 15px 0px 15px !important;
}
.card_photo img {
  border-radius: 5px;
}
.card_blk {
  border: 4px solid #3cc09b;
}
</style>
