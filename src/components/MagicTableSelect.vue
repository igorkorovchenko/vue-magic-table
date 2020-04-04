<template>
  <div class="magic-table-input" :class="[
          { 'magic-table-input_fetching' : fetching },
          { 'magic-table-input_valid' : isFetchedCorrectly && easing },
          { 'magic-table-input_invalid' : !isFetchedCorrectly && easing }
        ]" >
    <label :for="'id-' + name" class="magic-table-input__label">
      <p v-if="!editing" @click="editValue">{{ modelValue }}</p>
      <select v-else
              class="magic-table-input__select"
              ref="selectInput"
              :id="'id-' + name"
              :name="name"
              @keyup.enter="editValue">
        <option v-for="(option, option_index) in val.values"
                :key="option_index"
                :value="option.value"
                :selected="option.selected"
                >
          {{ option.label }}
        </option>
      </select>
      <p v-if="!valid || !isFetchedCorrectly" class="magic-table-input__message">{{ message }}</p>
    </label>
  </div>
</template>

<script>
export default {
  name: 'MagicTableSelect',
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
        let value = this.$refs.selectInput.value
        for (let i = 0; i < this.val.values.length; i++) {
          if (this.val.values[i].value === value) {
            this.modelValue = this.val.values[i].label
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
.magic-table-input__select {
  width: 100%;
}
.magic-table-input__message {
  color: dimgray;
  font-family: sans-serif;
}
</style>
