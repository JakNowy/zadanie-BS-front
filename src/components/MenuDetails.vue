<template>
<div style="margin: 0 20% 0 20%">
    <q-card class="fit q-pa-xl">
        <q-table
        :title="`${response.data.company_name}: ${response.data.name}`"
        class="q-mx-lg"
        dense
        :data="response.data.dishes"
        :columns="detailColumns"
        >
            <template v-slot:top-right>

            </template>
            <template v-slot:bottom></template>

        </q-table>
        <p>
            Opis restauracji: {{ response.data.description }}
        </p>
    </q-card>
</div>
</template>

<script>
export default {
    name: "MenuDetails",
    props: {
        response : Object,
    },
    mounted () {
        console.log(this.props.response)
    },
    data () {
        return {
            detailColumns: [
                { name: 'name', label: 'Nazwa', field: 'name', sortable: false , align: 'left' },
                { name: 'description', label: 'Opis', field: 'description', sortable: false , align: 'center' },
                { name: 'is_vegan', label: 'Wegańskie', field: 'is_vegan', sortable: false , align: 'center', format: val => val ? 'Tak' : 'Nie'},
                { name: 'price', label: 'Cena', field: 'price', sortable: false , align: 'center', format: val => `${val} zl`},
            ],
        }
    },
}
</script>

<style scoped>
</style>