<template>
    <div>
        <vc-row>
            <vc-box :title="$t('Filtros')" :width="12" :rounded="true" :collapsed="false" :close="true">
                <vc-form id="resourceFiltersForm">

                    <vc-row>
                        <vc-input
                            :width='4'
                            :label="$t('Licença')"
                            type="select"
                            :multi="false"
                            :rounded="true"
                            :api-url="urls.licenses"
                            option-value="id"
                            :option-text="['code', 'description']"
                            v-model="filters.licenses_id">
                        </vc-input>

                        <vc-input
                            :width='4'
                            :label="$t('Grupo')"
                            type="select"
                            :multi="false"
                            :rounded="true"
                            :api-url="urls.getGroup"
                            option-value="group_id"
                            :option-text="['name', 'description']"
                            v-model="filters.group_id">
                        </vc-input>

                        <vc-input
                            :width='4'
                            :label="$t('Tipo')"
                            type="select"
                            :multi="false"
                            :rounded="true"
                            :api-url="urls.getType"
                            option-value="type_id"
                            :option-text="['name', 'description']"
                            v-model="filters.type_id">
                        </vc-input>
                    </vc-row>

                    <vc-col :width='6'></vc-col>
                    <vc-row>
                        <vc-button
                            template="exportar-csv"
                            :text="$t('Excel')"
                            :width="2"
                            :rounded="true"
                            @click.native="exportar">
                        </vc-button>

                        <vc-button
                            :width="2"
                            template="filtrar"
                            :text="$t('Pesquisar')"
                            type="primary"
                            form="resourceFiltersForm"
                            :rounded="true"
                            @click.native="resourceTableReload">
                        </vc-button>

                        <vc-button
                            template="limpar-filtros"
                            :width="2"
                            :aligned="true"
                            :rounded="true"
                            @click.native="clearFilters">
                        </vc-button>
                    </vc-row>
                </vc-form>
            </vc-box>
        </vc-row>

        <vc-row>
            <vc-box :rounded="true" :title="$t('Licenças de Transporte')">
                <vc-row>
                    <vc-button
                        template="novo"
                        :text="$t('Novo')"
                        :width="2"
                        :rounded="true"
                        :aligned="false"
                        @click.native="openResourceModal">
                    </vc-button>
                    <vc-col :width="12">
                        <vc-table ref="tableProvider"
                                  dom="<'row'<'col-sm-6'B><'col-sm-6'f>><'row'<'col-sm-12'tr>><'row'<'col-sm-6'i><'col-sm-6'p>>"
                                  :items="items"
                                  :api-url="urls.gridLicense"
                                  :server-side="true"
                                  :cols="colsTable"
                                  :options="{'searching': false, 'select': true}"
                                  :filters="filters"
                                  :row-callback="rowCallback">
                        </vc-table>
                    </vc-col>
                </vc-row>
            </vc-box>
        </vc-row>

        <vc-row>
            <vc-box :rounded="true" :title="$t('Treinamentos e Licenças')">
                <vc-row>
                    <vc-button
                        template="novo"
                        :text="$t('Novo')"
                        :width="2"
                        :disabled="isDisabled"
                        :rounded="true"
                        :aligned="false"
                        @click.native="openResourceModalLicensesTrainings">
                    </vc-button>
                    <vc-col :width="12">
                        <vc-table ref="tableLicensesTrainings"
                                  dom="<'row'<'col-sm-6'B><'col-sm-6'f>><'row'<'col-sm-12'tr>><'row'<'col-sm-6'i><'col-sm-6'p>>"
                                  :items="items"
                                  :api-url="urls.gridLicenseTraining"
                                  :server-side="true"
                                  :cols="colsTableLicensesTrainings"
                                  :options="{'searching': false, 'select': true}"
                                  :filters="filtersLicensesTrainings"
                                  :row-callback="rowCallback2">
                        </vc-table>
                    </vc-col>
                </vc-row>
            </vc-box>
        </vc-row>

        <vc-modal ref="resourceModal" :title="`${'Cadastro de Licenças de Transporte'} ${formData.licenses_id ? $t('(Editar)') : $t('(Adicionar)')}`" :rounded="true">
            <template slot="body">
                <vc-form id="resourceForm">
                    <vc-row>
                        <vc-input
                            v-if="!formData.licenses_id"
                            :label="$t('Código')"
                            name="code"
                            type="select"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            :api-url="urls.getSequence"
                            option-value="code"
                            :option-text="[code]"
                            v-model="formData.code"
                        ></vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            v-if="formData.licenses_id"
                            :label="$t('Código')"
                            name="code"
                            type="number"
                            disabled="true"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            v-model="formData.code">
                        </vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Descrição')"
                            name="license_des"
                            type="text"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            :api-url="urls.getGroup"
                            option-value="group_id"
                            :option-text="['name', 'description']"
                            v-model="formData.license_groups_id">
                        </vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Grupo')"
                            name="license_groups_id"
                            type="select"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            :api-url="urls.getGroup"
                            option-value="group_id"
                            :option-text="['name', 'description']"
                            v-model="formData.license_groups_id">
                        </vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Tipo')"
                            name="license_types_id"
                            type="select"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            :api-url="urls.getType"
                            option-value="type_id"
                            :option-text="['name', 'description']"
                            v-model="formData.license_types_id">
                        </vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Obrig.')"
                            name="license_required"
                            type="select"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            :data="status"
                            v-model="formData.license_required">
                        </vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Vencimento')"
                            name="license_validity"
                            type="date"
                            :aligned="false"
                            :required="true"
                            :group="false"
                            v-model="formData.license_validity">
                        </vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Prazo de Alerta')"
                            name="license_time_alert"
                            type="number"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            v-model="formData.license_time_alert">
                        </vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Validar Lic.')"
                            name="license_validate_license"
                            type="select"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            :data="status"
                            v-model="formData.license_validate_license">
                        </vc-input>
                    </vc-row>
                </vc-form>
            </template>

            <template slot="footer">
                <vc-row>
                    <vc-button
                        :text="$t('Cancelar')"
                        icon="fa fa-times"
                        :aligned="false"
                        :offset="6"
                        :width="3"
                        :rounded="true"
                        @click.native="closeResourceModal">
                    </vc-button>

                    <vc-button
                        :witdh="12"
                        type="primary"
                        :text="$t('Salvar')"
                        icon="fa fa-check"
                        buttonType="submit"
                        form="resourceForm"
                        :aligned="false"
                        :width="3"
                        :rounded="true"
                        @click.native="saveResource">
                    </vc-button>
                </vc-row>
            </template>
        </vc-modal>



        <vc-modal ref="resourceModalLicensesTrainings" :title="`${'Cadastro de Treinamentos E Licenças'} ${formData.training_id ? $t('(Editar)') : $t('(Adicionar)')}`" :rounded="true">
            <template slot="body">
                <vc-form id="resourceFormLincesesTrainings">
                    <vc-row>
                        <vc-row>
                            <vc-input
                                name="licenses_id"
                                type="hidden"
                                :rounded="true"
                                v-model="formData.licenses_id">
                            </vc-input>
                        </vc-row>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Treinamento')"
                            name="trainings_id"
                            type="select"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            :api-url="urls.training"
                            option-value="id"
                            :option-text="['title']"
                            v-model="formData.trainings_id">
                        </vc-input>
                    </vc-row>

                    <vc-row>
                        <vc-input
                            :label="$t('Obrigatório')"
                            name="required"
                            type="select"
                            :uppercase="true"
                            :normalize="false"
                            :rounded="true"
                            :data="status"
                            v-model="formData.required">
                        </vc-input>
                    </vc-row>

                </vc-form>
            </template>

            <template slot="footer">
                <vc-row>
                    <vc-button
                        :text="$t('Cancelar')"
                        icon="fa fa-times"
                        :aligned="false"
                        :offset="6"
                        :width="3"
                        :rounded="true"
                        @click.native="closeResourceModalLicensesTrainings">
                    </vc-button>

                    <vc-button
                        :witdh="12"
                        type="primary"
                        :text="$t('Salvar')"
                        icon="fa fa-check"
                        buttonType="submit"
                        form="resourceFormLincesesTrainings"
                        :aligned="false"
                        :width="3"
                        :rounded="true"
                        @click.native="saveResourceLicensesTrainings">
                    </vc-button>
                </vc-row>
            </template>
        </vc-modal>
    </div>
</template>

<script>
// Importação de componentes utilizados na página
import VcBox from '@components/VcBox.vue';
import VcButton from '@components/VcButton.vue';
import VcCol from '@components/VcCol.vue';
import VcInput from '@components/VcInput.vue';
import VcModal from '@components/VcModal.vue';
import VcRow from '@components/VcRow.vue';
import VcTable from '@components/VcTable.vue';
import VcForm from "@components/VcForm";
import VcInputRange from "@components/VcInputRange.vue";

// Services
import { ApiGatewayService } from "@js/services/ApiGatewayService";

export default {
    // Registro dos componentes
    components: {
        VcBox,
        VcButton,
        VcCol,
        VcInput,
        VcModal,
        VcRow,
        VcTable,
        VcForm,
        VcInputRange
    },

    data() {
        return {
            title: window.Laravel.title || 'LicensesTrainings',
            file: null,
            filters: {},
            filtersLicensesTrainings: {
                licenses_id: -1
            },
            licenseId: '',
            items: [],
            isDisabled: true,
            isDisabledBox: true,
            apiGateway: new ApiGatewayService(),
            code: '', // Inicialize a propriedade code com uma string vazia
            formData: {
                code: '',
            },
            status: [
                {id: 0, text: 'Não'},
                {id: 1, text: 'Sim'},
                {id: '', text: 'NA'}
            ],
            url: window.Laravel.gatewayBaseUri,
            urls: {
                training: window.Laravel.gatewayBaseUri + '/services/carriers/api/training',
                licenseTraining: window.Laravel.gatewayBaseUri + '/services/carriers/api/license-trainings',
                licenses: window.Laravel.gatewayBaseUri + '/services/carriers/api/licenses',
                getGroup: window.Laravel.gatewayBaseUri + '/services/carriers/api/licenses-group-data',
                getType: window.Laravel.gatewayBaseUri + '/services/carriers/api/licenses-type-data',
                getSequence: window.Laravel.gatewayBaseUri + '/services/carriers/api/get-sequence',
                gridLicense: window.Laravel.gatewayBaseUri + '/services/carriers/api/grid-license',
                gridLicenseTraining: window.Laravel.gatewayBaseUri + '/services/carriers/api/grid-license-training',

            },
            colsTable: [
                {
                    title: this.$i18n.t('Ações'),
                    name:'licenses.id',
                    data: 'licenses_id',
                    width: '10%',
                    orderable: false,
                    class: 'text-center table-action'
                },
                {
                    //prviders
                    title: this.$i18n.t('Código'),
                    name: 'code',
                    data: 'code',
                },
                {
                    title: this.$i18n.t('Descrição'),
                    name: 'licenses.description',
                    data: 'license_des',
                },
                {
                    title: this.$i18n.t('Grupo'),
                    name: 'license_groups.description',
                    data: 'group_des',
                },
                {
                    title: this.$i18n.t('Tipo'),
                    name: 'license_types.description',
                    data: 'type_des',
                },
                {
                    title: this.$i18n.t('Obrig.'),
                    name: 'licenses.required',
                    data: 'license_required',
                    render: function(data) {
                        if (data === '1') {
                            return 'Sim'
                        }
                        return 'Não'
                    }
                },
                {
                    title: this.$i18n.t('Vencimento'),
                    name: 'licenses.validity',
                    data: 'license_validity',
                    class:'text-center',
                    render: function(data) {
                        return moment(data).format('DD/MM/YYYY');
                    }
                },
                {
                    title: this.$i18n.t('Prazo de Alerta'),
                    data: 'license_time_alert',
                    name: 'license_time_alert',
                },
                {
                    title: this.$i18n.t('Validar Lic.'),
                    data: 'license_validate_license',
                    name: 'license_validate_license',
                    render: function(data) {
                        if (data === '1') {
                            return 'Sim'
                        }
                        return 'Não'
                    }
                }
            ],
            colsTableLicensesTrainings: [
                {
                    title: this.$i18n.t('Ações'),
                    data: 'training_id',
                    name:'training_id',
                    width: '10%',
                    orderable: false,
                    class: 'text-center table-action2'
                },
                {
                    title: this.$i18n.t('Obrigatório'),
                    name: 'training_required',
                    data: 'training_required',
                    render: function(data) {
                        if (data === '1') {
                            return 'Sim'
                        }
                        return 'Não'
                    }
                },
                {
                    title: this.$i18n.t('Licença'),
                    name: 'license_des',
                    data: 'license_des',
                },
                {
                    title: this.$i18n.t('Treinamentos'),
                    name: 'training_title',
                    data: 'training_title',
                },
            ],
        }
    },

    methods: {

        plusOne() {
            axios({
                method: 'GET',
                headers: {
                    'Authorization': 'Bearer ' + window.Laravel.userToken
                },
                url: this.url + this.getSequence
            }).then(response => {
                this.code = response.data.valor; // Atualize a propriedade 'code' com o valor recuperado do banco de dados
                this.formData.code = this.code; // Também atualize a propriedade 'code' em formData com o valor recuperado do banco de dados
            }).catch(error => {
                console.log(error);
            });
        },

        clearFilters() {
            this.filters = {};
            this.filtersLicensesTrainings.model = "";
            setTimeout(() => { this.resourceTableReload(); },1);
        },

        resourceTableReload() {
            this.$refs.tableProvider.tableApi.ajax.reload();
            if (this.filtersLicensesTrainings.licenses_id != -1) {
                this.filtersLicensesTrainings.licenses_id = -1;
                this.$refs.tableLicensesTrainings.tableApi.ajax.reload();
            }
        },

        exportar(){
            Event.$emit('VcLoader', true);
            axios({
                method: 'POST',
                headers: {
                    'Authorization': 'Bearer ' + window.Laravel.userToken
                },
                url: this.url + '/services/carriers/api/export-licenses',
                data: { filters: this.filters }
            }).then((response) => {
                Event.$emit('VcLoader', false);

                let link = document.createElement('a');
                link.target = '_blank';
                link.href = 'data:application/xlsx;base64,' + response.data.data;
                link.download = 'provider-box-model' + moment().format('YYYY-MM-DD-HH-mm-ss') + '.xlsx';
                link.click();
            }).catch((e) => {
                Event.$emit('VcLoader', false);
                if (e.response.status === 422) {
                    Event.$emit('VcAlert', {
                        type: 'warning',
                        title: this.$i18n.t('Atenção!'),
                        text: this.$i18n.t(e.response.data)
                    });
                } else {
                    Event.$emit('VcAlert', {
                        type: 'error',
                        title: this.$i18n.t('Erro!'),
                        text: this.$i18n.t('Houve uma falha no processamento.')
                    });
                }
            });
        },

        /**
         * Incluo  na coluna Ação dois Botoes um de Editar registro e outro de excluir
         * @param row
         * @param data
         */
        rowCallback(row, data) {
            let act = new Vue({
                template: `<td class="text-center table-action">
                    <vc-button template="table-editar"  class="edit-row" width="inline"></vc-button> |
                    <vc-button template="table-excluir" class="delete"   width="inline"></vc-button>
                </td>`,
                components: {
                    VcButton
                }
            });

            /**
             * No momento que estou montando informo qual é o numero da coluna em td:eq
             */
            act.$mount($('.table-action', row)[0]);

            /**
             * Informo que o botão edit-row pertence a funcao modal
             */
            $('.table-action',row).on('click','.edit-row', () => {
                this.openResourceModal(data);
            })

            /**
             * Informo que a botão delete pertence a funcao delete
             */
            $('.table-action',row).on('click','.delete', () => {
                this.deleteResource(data);
            })
        },

        /**
         * Incluo  na coluna Ação dois Botoes um de Editar registro e outro de excluir
         * @param row
         * @param data
         */
        rowCallback2(row, data) {
            let act = new Vue({
                template: `<td class="text-center table-action2">
                    <vc-button template="table-editar"  class="edit-row" width="inline"></vc-button> |
                    <vc-button template="table-excluir" class="delete"   width="inline"></vc-button>
                </td>`,
                components: {
                    VcButton
                }
            });

            /**
             * No momento que estou montando informo qual é o numero da coluna em td:eq
             */
            act.$mount($('.table-action2', row)[0]);

            /**
             * Informo que o botão edit-row pertence a funcao modal
             */
            $('.table-action2',row).on('click','.edit-row', () => {
                this.openResourceModalLicensesTrainings(data);
            })

            /**
             * Informo que a botão delete pertence a funcao delete
             */
            $('.table-action2',row).on('click','.delete', () => {
                this.deleteResourceLicensesTrainings(data);
            })
        },

        /**
         * Função que abre, o modal que funciona tanto para editar registro e novo registo.
         * @param data
         */
        openResourceModal(data) {
            /**
             * Limpo os valores das variaveis do modal
             */
            this.formData = {};

            Event.$emit('VcLoader', true);

            /**
             * Verifico se dentro do valor da variavel data tem id , se tem siguinifica que será um modal de edição
             * então gravo na variavel colsModal os valores da linha para edição
             */
            if (data && data.licenses_id) {
                this.formData = { ...data };
            }

            /**
             * Abro o Modal
             */
            this.$refs.resourceModal.show();
            Event.$emit('VcLoader', false);
        },

        /**
         * Função que abre, o modal que funciona tanto para editar registro e novo registo.
         * @param data
         */
        openResourceModalLicensesTrainings(data) {
            /**
             * Limpo os valores das variaveis do modal
             */
            this.formData = {};

            Event.$emit('VcLoader', true);

            /**
             * Verifico se dentro do valor da variavel data tem id , se tem siguinifica que será um modal de edição
             * então gravo na variavel colsModal os valores da linha para edição
             */
            // console.log(data.training_id);
            if (data && data.training_id) {
                this.formData = { ...data };
            } else {
                this.formData.licenses_id = this.licenseId;
            }

            /**
             * Abro o Modal
             */
            this.$refs.resourceModalLicensesTrainings.show();
            Event.$emit('VcLoader', false);
        },


        closeResourceModal() {
            this.$refs.resourceModal.hide();
        },

        closeResourceModalLicensesTrainings() {
            this.$refs.resourceModalLicensesTrainings.hide();
        },

        saveResource() {
            let method = 'post';
            let endpoint = this.urls.licenses;

            let data = { ...this.formData };

            data.license_groups_id = parseInt(data.license_groups_id);
            data.license_types_id = parseInt(data.license_types_id);
            data.license_required = parseInt(data.license_required);
            data.license_validate_license = parseInt(data.license_validate_license);
            data.license_time_alert = parseInt(data.license_time_alert);

            if (data.licenses_id) {
                method = 'put';
                endpoint = `${endpoint}/${data.licenses_id}`;
            }

            this.callApiGateway(method, endpoint, data);
            this.closeResourceModal();
            setTimeout(() => this.$refs.tableProvider.tableApi.ajax.reload(), 300);
        },

        saveResourceLicensesTrainings() {
            let method = 'post';
            let endpoint = this.urls.licenseTraining;

            let data = { ...this.formData };

            data.required = parseInt(data.required);
            // console.log(data);
            if (data.training_id) {
                method = 'put';
                endpoint = `${endpoint}/${data.training_id}`;
            }

            this.callApiGateway(method, endpoint, data);
            this.closeResourceModalLicensesTrainings();
            setTimeout(() => this.$refs.tableLicensesTrainings.tableApi.ajax.reload(), 300);
        },

        deleteResource(data) {
            let endpoint = this.urls.licenses;
            Event.$emit('VcAlert', {
                mode: 'alert',
                type: 'error',
                dangerMode: true,
                title: `${this.title} (Excluir)`,
                text: `Você tem certeza que deseja excluir o registro?.`,
                cancelButtonText: '<i class="fa fa-times"></i> Cancelar',
                confirmButtonText: '<i class="fa fa-trash"></i> Excluir',
                caseConfirm: () => {
                    this.callApiGateway('delete', `${endpoint}/${data.licenses_id}`);
                    window.onunhandledrejection = function(e) {
                        Event.$emit('VcAlert', {
                            type: 'warning',
                            title: 'Atenção!',
                            text: 'É necessário excluir as caixas vinculadas a este fornecedor para poder exclui-lo.'
                        })
                    }
                    setTimeout(() => this.$refs.tableProvider.tableApi.ajax.reload(), 300);
                }
            })
        },

        deleteResourceLicensesTrainings(data) {
            let endpoint = this.urls.licenseTraining;
            Event.$emit('VcAlert', {
                mode: 'alert',
                type: 'error',
                dangerMode: true,
                title: `${this.title} (Excluir)`,
                text: `Você tem certeza que deseja excluir o registro?.`,
                cancelButtonText: '<i class="fa fa-times"></i> Cancelar',
                confirmButtonText: '<i class="fa fa-trash"></i> Excluir',
                caseConfirm: () => {
                    this.callApiGateway('delete', `${endpoint}/${data.training_id}`);
                    setTimeout(() => this.$refs.tableLicensesTrainings.tableApi.ajax.reload(), 300);
                }
            })
        },

        clearFilter() {
            this.likeFilters.code = "";
            this.likeFilters.qr_code = "";
            this.likeFilters.sector_id = "";
            this.filters.id="";
            this.filters.code="";
            this.filters.qr_code="";
            this.filters.sector_id="";
            this.resourceTableReload();
        },

        callApiGateway: function (method, url, data = {}) {
            Event.$emit('VcLoader', true);
            Event.$emit('VcInputError', {});

            this.apiGateway.request(method, url, data)
                .then(res => {
                    Event.$emit('VcAlert', {
                        mode: 'alert',
                        type: 'success',
                        title: this.title,
                        text: 'Operação realizada com sucesso!',
                        caseConfirm: () => this.closeResourceModal()
                    });

                    this.resourceTableReload();
                })
                .catch(err => {
                    if (err.response && err.response.status === 422) {
                        Event.$emit('VcInputError', err.response.data.error || {});
                    }

                    if (data.codigo === this.formData.codigo) {
                        Event.$emit('VcAlert', {
                            type: 'error',
                            title: this.title,
                            text: 'Cadastro não permitido. Código já existente!'
                        });
                    } else {
                        Event.$emit('VcAlert', {
                            type: 'error',
                            title: this.title,
                            text: 'Ocorreu um erro ao enviar os dados!'
                        });
                    }

                    console.error(err.message);
                })
                .finally(() => {
                    Event.$emit('VcLoader', false);
                });
        }
    },

    watch: {
        licenseId(newVal) {
            if (newVal != -1) {
                this.isDisabled = false;
            } else {
                this.isDisabled = true;
            }
        },

        'filters.training_id': function(newValue, oldValue) {
            if (newValue != '') {
                this.isDisabledBox = false;
                this.urls.returnListLicensesTrainings = this.urls.licenseTraining + "/" + newValue;

            } else {
                this.isDisabledBox = true;
            }
        },
    },

    mounted() {
        axios({
            method: 'GET',
            headers: {
                'Authorization': 'Bearer ' + window.Laravel.userToken
            },
            url: this.url + this.getSequence
        })
            .then(response => {
                this.code = response.data.valor;
            })
            .catch(error => {
                console.log(error);
            });

        this.$refs.tableProvider.tableApi.on('select', (e, dt, type, index) => {
            let data = this.$refs.tableProvider.tableApi.row(index).data();
            if (data.licenses_id) {
                this.filtersLicensesTrainings.licenses_id = data.licenses_id;
                this.filtersLicensesTrainings.model = data.model;
                this.licenseId = data.licenses_id;
            } else {
                this.filtersLicensesTrainings.licenses_id = data.id;
                this.licenseId = data.id;
            }
            setTimeout(() => this.$refs.tableLicensesTrainings.tableApi.ajax.reload(), 300);
        });
        this.$refs.tableProvider.tableApi.on('deselect', (e, dt, type, index) => {
            this.filtersLicensesTrainings.licenses_id = -1;
            this.licenseId = -1;
            setTimeout(() => this.$refs.tableLicensesTrainings.tableApi.ajax.reload(), 300);
        });
    },
}

$(document).ready(function() {
    $(".allow-numeric").bind("keypress", function (e) {
        var keyCode = e.which ? e.which : e.keyCode

        if (!(keyCode >= 48 && keyCode <= 57)) {
            return false;
        }
    });
});

</script>
