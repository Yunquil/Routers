<template>
  <div>
    <button> <router-link to="/">Login</router-link></button>
    <button> <router-link to="/Comprar">Comprar</router-link></button>
    <button><router-link to="/Ventas">ventas</router-link></button>
  
  <router-view></router-view>

  <div class="q-pa-md">
    <q-table
      flat bordered
      title="Treats"
      :rows="rows"
      :columns="columns"
      row-key="name"
      binary-state-sort
    >
      <template v-slot:body="props">
        <q-tr :props="props">
          <q-td key="name" :props="props">
            {{ props.row.name }}
            <q-popup-edit v-model="props.row.name" v-slot="scope">
              <q-input v-model="scope.value" dense autofocus counter @keyup.enter="scope.set" />
            </q-popup-edit>
          </q-td>
          <q-td key="calories" :props="props">
            {{ props.row.calories }}
            <q-popup-edit v-model="props.row.calories" title="Update calories" buttons v-slot="scope">
              <q-input type="number" v-model="scope.value" dense autofocus />
            </q-popup-edit>
          </q-td>
          <q-td key="fat" :props="props">
            <div class="text-pre-wrap">{{ props.row.fat }}</div>
            <q-popup-edit v-model="props.row.fat" v-slot="scope">
              <q-input type="textarea" v-model="scope.value" dense autofocus />
            </q-popup-edit>
          </q-td>
          <q-td key="carbs" :props="props">
            {{ props.row.carbs }}
            <q-popup-edit v-model="props.row.carbs" title="Update carbs" buttons persistent v-slot="scope">
              <q-input type="number" v-model="scope.value" dense autofocus hint="Use buttons to close" />
            </q-popup-edit>
          </q-td>
          <q-td key="protein" :props="props">{{ props.row.protein }}</q-td>
          <q-td key="sodium" :props="props">{{ props.row.sodium }}</q-td>
          <q-td key="calcium" :props="props">{{ props.row.calcium }}</q-td>
          <q-td key="iron" :props="props">{{ props.row.iron }}</q-td>
        </q-tr>
      </template>
    </q-table>
  </div>
  </div>
  
</template>

<script>
import { ref, computed, nextTick } from 'vue'

const columns = [
  {
    name: 'index',
    label: '#',
    field: 'index'
  },
  {
    name: 'name',
    required: true,
    label: 'Dessert (100g serving)',
    align: 'left',
    field: row => row.name,
    format: val => val,
    sortable: true
  },
  { name: 'calories', align: 'center', label: 'Calories', field: 'calories', sortable: true },
  { name: 'fat', label: 'Fat (g)', field: 'fat', sortable: true },
  { name: 'carbs', label: 'Carbs (g)', field: 'carbs' },
  { name: 'protein', label: 'Protein (g)', field: 'protein' },
  { name: 'sodium', label: 'Sodium (mg)', field: 'sodium' },
  { name: 'calcium', label: 'Calcium (%)', field: 'calcium', sortable: true, sort: (a, b) => parseInt(a, 10) - parseInt(b, 10) },
  { name: 'iron', label: 'Iron (%)', field: 'iron', sortable: true, sort: (a, b) => parseInt(a, 10) - parseInt(b, 10) }
]

const seed = [
  {
    name: 'Frozen Yogurt',
    calories: 159,
    fat: 6.0,
    carbs: 24,
    protein: 4.0,
    sodium: 87,
    calcium: '14%',
    iron: '1%'
  },
  {
    name: 'Ice cream sandwich',
    calories: 237,
    fat: 9.0,
    carbs: 37,
    protein: 4.3,
    sodium: 129,
    calcium: '8%',
    iron: '1%'
  },
  {
    name: 'Eclair',
    calories: 262,
    fat: 16.0,
    carbs: 23,
    protein: 6.0,
    sodium: 337,
    calcium: '6%',
    iron: '7%'
  },
]

// we generate lots of rows here
let allRows = []
for (let i = 0; i < 1000; i++) {
  allRows = allRows.concat(seed.slice(0).map(r => ({ ...r })))
}
allRows.forEach((row, index) => {
  row.index = index
})

const pageSize = 50
const lastPage = Math.ceil(allRows.length / pageSize)

export default {
  setup () {
    const nextPage = ref(2)
    const loading = ref(false)

    const rows = computed(() => allRows.slice(0, pageSize * (nextPage.value - 1)))

    return {
      columns,
      rows,

      nextPage,
      loading,

      pagination: { rowsPerPage: 0 },

      onScroll ({ to, ref }) {
        const lastIndex = rows.value.length - 1

        if (loading.value !== true && nextPage.value < lastPage && to === lastIndex) {
          loading.value = true

          setTimeout(() => {
            nextPage.value++
            nextTick(() => {
              ref.refresh()
              loading.value = false
            })
          }, 500)
        }
      }
    }
  }
}
</script>
