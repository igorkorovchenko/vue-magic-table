<template>
  <div class="magic-table">
    <table class="magic-table__table">
      <thead class="magic-table__head">
        <tr v-for="(hrow, hrow_index) in this.actualData.head" :key="hrow_index" class="magic-table__head-row">
          <th v-for="(hcol, hcol_index) in hrow.row" :key="hcol_index" class="magic-table__head-col">{{ hcol.name }}</th>
        </tr>
      </thead>
      <tbody class="magic-table__body">
        <tr v-for="(brow, brow_index) in this.actualData.rows" :key="brow_index" class="magic-table__body-row">
          <td v-for="(bcol, bcol_index) in brow.row" :key="bcol_index" class="magic-table__body-col">
            <MagicTableInput v-if="typeof bcol.value === 'string' || typeof bcol.value === 'number'" :val="bcol.value" :name="bcol.name" />
            <MagicTableSelect v-else-if="typeof bcol.value === 'object' && bcol.value.type === 'select'" :val="bcol.value" :name="bcol.name" />
            <MagicTableRadio v-else-if="typeof bcol.value === 'object' && bcol.value.type === 'radio'" :val="bcol.value" :name="bcol.name" />
            <p v-else>{{ bcol.value }}</p>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'MagicTable',
  data () {
    return {
      actualData: {}
    }
  },
  props: [ 'tabledata' ],
  methods: {
    loadData (url, okFunction, afterOkFunction = null, notOkFunction = null) {
      fetch(url)
        .then(function (response) {
          if (response.status === 200) {
            response.json()
              .then(json => okFunction(json))
              .then(() => {
                if (afterOkFunction) afterOkFunction()
              })
          } else {
            if (notOkFunction) notOkFunction(response)
          }
        })
    }
  },
  components: {
    MagicTableInput: () => import('./MagicTableInput'),
    MagicTableSelect: () => import('./MagicTableSelect'),
    MagicTableRadio: () => import('./MagicTableRadio')
  },
  created () {
    this.loadData(this.tabledata, (d) => {
      this.actualData = d
    })
  }
}
</script>

<style scoped>
.magic-table {
  align-items: center;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  margin: auto;
  padding: 33px 30px;
}
.magic-table__table {
  background: white;
  border-collapse: collapse;
  border-radius: 10px;
  border-spacing: 1;
  margin: 0 auto;
  overflow: hidden;
  position: relative;
  width: 100%;
}
.magic-table__table * {
  position: relative;
}
.magic-table__table .magic-table__body-col,
.magic-table__table .magic-table__head-col {
  padding: 8px 8px;
}
.magic-table__table .magic-table__head .magic-table__head-row {
  background: #36304a;
  height: 60px;
}
.magic-table__table .magic-table__body .magic-table__body-row {
  height: 50px;
}
.magic-table__table .magic-table__body .magic-table__body-row:last-child {
  border: 0;
}
.magic-table__table .magic-table__body-col,
.magic-table__table .magic-table__head-col {
  text-align: left;
}
.magic-table__head .magic-table__head-col {
  color: #fff;
  font-family: sans-serif;
  font-size: 18px;
  font-weight: unset;
  line-height: 1.2;
  text-align: center;
}
.magic-table__body .magic-table__body-row:nth-child(even) {
  background-color: #f5f5f5;
}
.magic-table__body .magic-table__body-row {
  color: #808080;
  font-family: sans-serif;
  font-size: 15px;
  font-weight: unset;
  line-height: 1.2;
}
.magic-table__body .magic-table__body-row:hover {
  background-color: #f5f5f5;
  color: #555555;
  cursor: pointer;
}
</style>
