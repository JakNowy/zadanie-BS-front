<template>
  <div class="flex flex-center q-ma-lg">
    <q-card class="q-pa-lg">
      <q-dialog v-model="detailMenuDialog" full-height full-width>
        <MenuDetails
        :response="detailResponse"
        />
      </q-dialog>

      <q-table
        style="min-height: 350px"
        :columns="tableColumns"
        :data="tableData"
        @row-click="handleRowClick"
        row-key="name"
      >
        <template v-slot:bottom>
          <div style="width: 100%" class="row q-pt-md">

            <div class="col-md-4"></div>

            <div class="col-md-4 justify-around flex">
              <q-btn @click="fetchMenus(pagination.prev, null, null)" :disable="!pagination.prev">
                Wstecz
              </q-btn>

              <q-btn @click="fetchMenus(pagination.next, null, null)" :disable="!pagination.next">
                Dalej
              </q-btn>
            </div>

            <div class="col-md-4 justify-end flex">
              <q-select
                style="width: 80%"
                filled
                dense
                options-dense
                v-model="orderring"
                label="Sortuj"
                :options="orderringOptions"
                behavior="menu"
              />
            </div>

          </div>
        </template>

        <template v-slot:top>
          <div class="text-h5">Menu dla Ciebie:</div>
        </template>
      </q-table>
    </q-card>
  </div>
</template>

<script>
import axios from 'axios';
import MenuDetails from "@/components/MenuDetails";
import dayjs from "dayjs";

export default {
  components: {
    MenuDetails
  },
  mounted() {
    let url2 = process.env.VUE_APP_BACKEND_SERVER
    console.log(url2)
    this.fetchMenus('http://localhost:8000/api/menus/?', null, 3, null)
  },
  data () {
    return {
      detailResponse: null,
      detailMenuDialog: false,
      pagination: {
        offset: 0,
        rowsPerPage: 3, //limit
        count: null
      },
      orderringOptions: [
        {label: 'Nazwa', value: 'name'},
        {label: 'Nazwa (malejąco)', value: '-name'},
        {label: 'Liczba dań', value: 'dish_count'},
        {label: 'Liczba dań (malejąco)', value: '-dish_count'},
      ],
      orderring: {
        label: 'Nazwa',
        value: 'id',
      },
      tableColumns: [
        { name: 'id', label: 'ID', field: 'id', sortable: false , align: 'left' },
        { name: 'Restauracja', label: 'Restauracja', field: 'company_name', sortable: true, align: 'center'},
        { name: 'Nazwa', label: 'Nazwa', field: 'name', sortable: true , align: 'center' },
        { name: 'Opis', label: 'Opis', field: 'description', align: 'center' },
        { name: 'Dania', label: 'Liczba dań', field: 'dish_count', sortable: true , align: 'center' },
        { name: 'Modyfikacja', label: 'Data modyfikacji', field: 'date_modified', sortable: false , align: 'center',
          format: val => dayjs(val).format('DD-MM-YYYY') },
        { name: 'Dodano', label: 'Data dodania', field: 'date_created', sortable: false , align: 'center',
          format: val => dayjs(val).format('DD-MM-YYYY')},
      ],
      tableData: [
      ],
    }
  },
  filters: {
    formatDate: function (isoDate) {
      return isoDate ? dayjs(isoDate).format('ddd MMM YYY') : ''
    }
  },
  watch: {
    orderring: function () {
      this.fetchMenus('http://localhost:8000/api/menus/?', null, this.pagination.rowsPerPage, this.orderring.value)
    }
  },
  methods: {
    fetchMenus (url, offset, limit, orderring) {
      offset = offset ? `&offset=${offset}` : ''
      limit = limit ? `&limit=${limit}` : ''
      orderring = orderring ? `&orderring=${orderring}` : ''
      url = url + orderring + limit + offset
      axios.get(url).then((response) => {
        this.pagination.rowsNumber = response.data.count
        this.pagination.count = response.data.count
        this.pagination.next = response.data.next
        this.pagination.prev = response.data.previous
        this.tableData = response.data.results
      }).catch((error) => {
        console.log(error)
      })
    },
    handleRowClick (event, row) {
      this.detailMenuDialog = true
      let url = 'http://localhost:8000/api/menus/' + row.id
      axios.get(url).then((response) => {
        this.detailResponse = response
      })
    },
  }
}
</script>

<style>
</style>
