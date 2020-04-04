<template>
  <div class="magic-table-input" :class="[
          { 'magic-table-input_fetching' : fetching },
          { 'magic-table-input_valid' : isFetchedCorrectly && easing },
          { 'magic-table-input_invalid' : !isFetchedCorrectly && easing }
        ]" >
      <div v-if="editing" class="magic-table-input_wrapper">
        <label v-for="(radio, radio_index) in val.values"
                class="magic-table-input__label"
                :key="radio_index"
                :for="'id-' + name"
                @click="modelValue = val.values[radio_index].label"
                @keyup.enter="editValue">
          <input
                  class="magic-table-input__radio"
                  type="radio"
                  :id="'id-' + name"
                  :name="name"
                  :checked="radio.selected">
          {{ radio.label }}
        </label>
      </div>
      <p v-else @click="editValue">{{ modelValue }}</p>
      <p v-if="!valid || !isFetchedCorrectly" class="magic-table-input__message">{{ message }}</p>
  </div>
</template>

<script>
export default {
  name: 'MagicTableRadio',
  data () {
    return {
      editing: false,
      valid: false,
      modelValue: null,
      host: 'example.com',
      token: 'token',
      message: '',
      fetching: false,
      easing: false,
      isFetchedCorrectly: true
    }
  },
  props: [ 'val', 'name', 'col', 'row' ],
  methods: {
    editValue () {
      this.editing = !this.editing
      if (this.modelValue && !this.editing) {
        let value = ''
        for (let i = 0; i < this.val.values.length; i++) {
          if (this.val.values[i].value === this.modelValue) {
            value = this.modelValue
            break
          }
        }
        let fetchObject = {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            Accept: 'application/json',
            Authorization: this.token,
            Host: this.host,
            Origin: this.host,
            'Access-Control-Request-Method': 'POST'
          },
          body: JSON.stringify({name: this.name, value: value})
        }
        this.uploadData(fetchObject, (r) => {
          this.message = r
        })
        return true
      }
    },
    isValidated (newValue) {
      return true
    },
    uploadData (fetchObject, okFunction, afterOkFunction = null, notOkFunction = null) {
      this.fetching = true
      // Mocking
      setTimeout(() => {
        okFunction(fetchObject)
        this.fetching = false
        this.easing = true
        this.isFetchedCorrectly = true
        setTimeout(() => {
          this.easing = false
        }, 2000)
      }, 2000)
      // Real request for uploading
      // fetch(this.host, fetchObject)
      //   .then(function (response) {
      //     if (response.status === 200) {
      //       response.json()
      //         .then(json => okFunction(json))
      //         .then(() => {
      //           if (afterOkFunction) afterOkFunction()
      //           this.fetching = false
      //         })
      //     } else {
      //       if (notOkFunction) notOkFunction(response)
      //       this.fetching = false
      //     }
      //   })
    }
  },
  watch: {
    modelValue (newValue) {
      this.valid = this.isValidated(newValue)
    }
  },
  created () {
    for (let i = 0; i < this.val.values.length; i++) {
      if (this.val.values[i].selected) {
        this.modelValue = this.val.values[i].label
        break
      }
    }
    if (!this.modelValue) {
      this.modelValue = this.val.values[0].label
    }
  }
}
</script>

<style scoped>
.magic-table-input {
  height: 100%;
  width: 100%;
}
.magic-table-input p {
  text-align: center;
}
.magic-table-input_fetching {
  background-color: lightyellow;
}
.magic-table-input_valid {
  background-color: lightgreen;
}
.magic-table-input_invalid {
  background-color: lightcoral;
}
.magic-table-input__label {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  padding: 8px;
}
.magic-table-input__label p {
  font-family: sans-serif;
  margin: 0;
  text-align: center;
  vertical-align: middle;
}
.magic-table-input__radio {
  display: block;
}
.magic-table-input__message {
  color: dimgray;
  font-family: sans-serif;
}
</style>
