<template>
    <app-layout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Participantes
            </h2>
        </template>
        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">

                    <div class="p-5 mb-6 flex flex-col md:flex-row md:justify-between items-center  ">
                       
                        <div class="w-full md:w-1/3 px-3 mb-6 md:mb-0">
                            <label class="block uppercase tracking-wide text-gray-700 text-xs font-bold mb-2">
                                Busca número de cédula
                            </label>
                            <input
                                class="appearance-none block w-full bg-gray-200 text-gray-700 border border-gray-200 rounded py-3 px-4 leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
                                v-model="form.search"
                                type="text"
                                placeholder="Escriba el número a buscar"
                            />
                        </div>

                        <button
                            class="sm:w-full md:w-auto border border-gray-400 hover:border-red-600 text-gray-500 hover:bg-red-600 hover:text-white font-bold py-3 mt-5 px-4 rounded"
                            type="button"
                            @click="showModal = true"
                        >
                        <svg class="h-5 w-5 inline-block " xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z" />
                        </svg>
                            Borrar Datos
                        </button>

                        <upload />

                    </div>

                    <div class="bg-white rounded shadow overflow-x-auto">
                        <table class="w-full whitespace-no-wrap">
                            <tr class="text-left font-bold">
                                <th class="px-6 pt-6 pb-4">Nombre</th>
                                <th class="px-6 pt-6 pb-4">Cédula</th>
                            </tr>
                            <affiliated
                                v-for="dato in datos.data"
                                :key="dato.id"
                                :dato="dato"
                            />
                        </table>
                    </div>
                </div>
                <pagination :links="datos.links" />
            </div>
        </div>
        <jet-confirmation-modal :show="showModal" @close="showModal = false">
            <template #title>
                Borrar datos
            </template>

            <template #content>
                ¿Estás seguro que quieres borrar los datos?
            </template>

            <template #footer>
                <jet-secondary-button @click.native="showModal = false">
                    Cancelar
                </jet-secondary-button>

                <jet-danger-button @click.native="destroy" class="ml-2" :class="{ 'opacity-25': processing }" :disabled="processing">
                    Borrar datos
                </jet-danger-button>
            </template>
        </jet-confirmation-modal>
    </app-layout>
</template>

<script>
    import debounce from "lodash/debounce";
    import pickBy from "lodash/pickBy";
    import mapValues from "lodash/mapValues";
    import AppLayout from "../../Layouts/AppLayout";
    import Pagination from "../../Components/Pagination";
    import Upload from "../../Components/Upload";
    import Affiliated from "../../Components/Affiliated";
    import JetConfirmationModal from "../../Jetstream/ConfirmationModal";
    import JetSecondaryButton from "../../Jetstream/SecondaryButton";
    import JetDangerButton from "../../Jetstream/DangerButton";

    export default {
        components: {Affiliated, AppLayout, Pagination, Upload, JetConfirmationModal, JetSecondaryButton, JetDangerButton},
        props: {
            datos: Object,
            filters: Object,
        },
        data() {
            return {
                processing: false,
                showModal: false,
                form: {
                    search: this.filters.search,
                    trashed: this.filters.trashed,
                }
            }
        },
        watch: {
            form: {
                handler: debounce(function() {
                    let query = pickBy(this.form)
                    this.$inertia.replace(this.route('dashboard.affiliated', query))
                }, 500),
                deep: true,
            }
        },
        methods: {
            reset() {
                this.form = mapValues(this.form, () => null);
            },
            destroy() {
                this.processing = true
                this.$inertia.delete(this.route('dashboard.destroy', 2))
                    .then(() => {
                        this.processing = false;
                        this.showModal = false;
                    })
            }
        }
    }
</script>
