<template>
  <div class="magic-table-input" :class="[
          { 'magic-table-input_fetching' : fetching },
          { 'magic-table-input_valid' : isFetchedCorrectly && easing },
          { 'magic-table-input_invalid' : !isFetchedCorrectly && easing }
        ]" >
    <label :for="'id-' + name" class="magic-table-input__label">
      <p v-if="!editing" @click="editValue">{{ modelValue }}</p>
      <input v-if="typeof val ==='string' && editing"
            class="magic-table-input__text"
            type="text"
            v-model="modelValue"
            :id="'id-' + name"
            :name="name"
            @keyup.enter="editValue">
      <input v-if="typeof val ==='number' && editing"
            class="magic-table-input__number"
            type="number"
            v-model="modelValue"
            :name="name"
            @keyup.enter="editValue">
      <p v-if="!valid || !isFetchedCorrectly" class="magic-table-input__message">{{ message }}</p>
    </label>
  </div>
</template>

<script>
export default {
  name: 'MagicTableInput',
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
          body: JSON.stringify({name: this.name, value: this.modelValue})
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
  created () {
    this.modelValue = this.val
  },
  watch: {
    modelValue (newValue) {
      this.valid = this.isValidated(newValue)
    }
  }
}
</script>

<style scoped>
.magic-table-input {
  height: 100%;
  width: 100%;
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
.magic-table-input__label p {
  font-family: sans-serif;
  margin: 0;
  text-align: center;
  vertical-align: middle;
}
.magic-table-input__text {
  width: 100%;
}
.magic-table-input__number {
  width: 100%;
}
.magic-table-input__message {
  color: dimgray;
  font-family: sans-serif;
}
</style>
