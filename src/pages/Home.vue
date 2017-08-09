<template>
    <div>
        <sidebar></sidebar>
        <main>
            <v-container fluid>
                    <div>
                        <v-card>
                            <v-card-title>
                                {{ title }}
                                <v-spacer></v-spacer>
                                <v-text-field append-icon="search" label="Search" single-line hide-details v-model="searchQuery"></v-text-field>
                                <v-btn floating small class="indigo" @click.native="showModalAdd = true"><v-icon light>add</v-icon></v-btn>
                            </v-card-title>
                            <demogrid :data="gridData">
                            </demogrid>
                        </v-card>
                    </div>

                    
                    <pre>{{ $data }}</pre>
            </v-container>
        </main>
    </div>

</template>

<script>

import Sidebar from '../components/include/Sidebar'


export default {
    // bootstrap the demo
    //name: '#demo',
    components: { Sidebar },
    data () {
        return {
            title: 'Clientes',
            searchQuery: '',
            gridData: [],
            showModalAdd: false,
            showModalEdt: false,
            showModalDel: false
        }
    },
    mounted: function(){
        console.log("bonina");
        this.getAllItems();
    },
    methods: {
        
    }
}
</script>

<style>
</style>