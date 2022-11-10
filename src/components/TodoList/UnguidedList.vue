<template>
    <v-main class="list">
        <v-card>
            <v-list-item>
                <v-list-item-avatar color="grey"></v-list-item-avatar>
                <v-list-item-content>
                    <v-list-item-title class="headline">To Do List</v-list-item-title>
                    <v-list-item-subtitle>by 200710683</v-list-item-subtitle>
                </v-list-item-content>
            </v-list-item>
            <v-card-title>
                <v-text-field
                v-model="search"
                append.icon="mdi-magnify"
                label="Search"
                outlined
                hidden
                details
                style="margin-top: 30px">
                </v-text-field>
                <v-spacer></v-spacer>
                <v-btn color="success" dark @click="dialog = true"> Tambah </v-btn>
            </v-card-title>
        </v-card>

        <v-card>
            <v-expansion-panel>
                <v-data-table :headers="headers" :items="todos" :search="search" :expanded="expanded" item-key="task" show-expand>   
                <template v-slot:[`item.priority`]="{ item }">
                    <v-icon v-show="item.priority=='Penting'" color="red">mdi-alert-octagon</v-icon>
                    <v-icon v-show="item.priority=='Tidak Penting'" color="blue">mdi-alert-octagon</v-icon>
                    <v-icon v-show="item.priority=='Biasa'" color="green">mdi-alert-octagon</v-icon>
                </template>

                <template v-slot:expanded-item="{ headers, item }">
                    <td v-if="item.priority=='Penting'" class="red darken-2" :colspan="headers.length">
                        <v-chip
                        color="red"
                        text-color="white">
                            <v-icon>mdi-fire</v-icon>Penting
                        </v-chip>
                        <br/>
                        <v-chip
                        color="yellow darken-4"
                        text-color="white">
                            <v-icon>mdi-note-text</v-icon>
                            Note Task: {{ item.note }}
                        </v-chip>
                    </td>

                    <td v-if="item.priority=='Tidak Penting'" class="blue darken-2" :colspan="headers.length">
                        <v-chip
                        color="blue"
                        text-color="white">
                        <v-icon>mdi-fire</v-icon>Tidak Penting
                        </v-chip>
                        <br/>
                        <v-chip
                        color="yellow darken-4"
                        text-color="white">
                            <v-icon >mdi-note-text</v-icon>
                            Note Task: {{ item.note }}
                        </v-chip>
                    </td>

                    <td v-if="item.priority=='Biasa'" class="green darken-2" :colspan="headers.length">
                        <v-chip
                        color="green"
                        text-color="white">
                        <v-icon>mdi-fire</v-icon>Biasa
                        </v-chip>
                        <br/>
                        <v-chip
                        color="yellow darken-4"
                        text-color="white">
                            <v-icon>mdi-note-text</v-icon>
                            Note Task: {{ item.note }}
                        </v-chip>
                    </td> 
                </template>

                <template v-slot:[`item.actions`]="{ item }">
                    <v-btn small class="button" fab color="red" @click="editItem(item)"><v-icon color="white">mdi-pen</v-icon></v-btn>
                    <v-btn small class="button" fab color="success" @click="deleteItem(item)"><v-icon color="white">mdi-trash-can</v-icon></v-btn>
                </template>

                </v-data-table>
            </v-expansion-panel>
        </v-card>
        
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline"> Form Todo List</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                        v-model="formTodo.task"
                        label="Task"
                        required>
                        </v-text-field>

                        <v-select
                        v-model="formTodo.priority"
                        :items="[`Penting`, `Biasa`, `Tidak Penting`]"
                        label="Priority"
                        required></v-select>

                        <v-textarea
                        v-model="formTodo.note"
                        label="Note"
                        required></v-textarea>

                        <v-select
                        v-model="formTodo.status"
                        :items="[`Selesai`, `Belum Selesai`]"
                        label="Status"
                        required></v-select>
                    </v-container>
                </v-card-text>

                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">Cancel</v-btn>
                    <v-btn color="blue darken-1" text @click="save">Save</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogEdit" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline"> Form Edit Todo List</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                        v-model="formTodo.task"
                        label="Task"
                        value=formTodo.task
                        required>
                        </v-text-field>
                        <v-select
                        v-model="formTodo.priority"
                        :items="[`Penting`, `Biasa`, `Tidak Penting`]"
                        label="Priority"
                        value="formTodo.priority"
                        required></v-select>

                        <v-textarea
                        v-model="formTodo.note"
                        label="Note"
                        value="formTodo.note"
                        required></v-textarea>

                        <v-select
                        v-model="formTodo.status"
                        :items="[`Selesai`, `Belum Selesai`]"
                        label="Status"
                        value="formTodo.status"
                        required></v-select>
                    </v-container>
                </v-card-text>

                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">Cancel</v-btn>
                    <v-btn color="blue darken-1" text @click="saveEdit">Save</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDelete" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Are you sure to delete?</span>
                </v-card-title>
                
                <v-card-actions>
                    <v-btn class="align-content-center d-flex mx-auto" color="green" text @click="confirm">Yes</v-btn>
                    <v-btn class="align-content-center d-flex mx-auto" color="red" text @click="cancel">No</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

    </v-main>
</template>

<script>
export default{
    name: "ListItem",
    data(){
        return{
            search: null,
            dialog: false,
            dialogEdit: false,
            dialogDelete: false,
            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                {text: "Priority", value: "priority"},
                {text: "Status", value: "status"},
                {text: "Actions", value: "actions"},
            ],
            todos: [
                {
                    task: "Coding",
                    priority: "Biasa",
                    note: "Males Ngoding",
                    status: "Belum Selesai",
                },
                {
                    task: "Cooking",
                    priority: "Tidak Penting",
                    note: "Indomie saat begadang ngerjain coding tidak sehat",
                    status: "Selesai",
                },
                {
                    task: "Gaming",
                    priority: "Penting",
                    note: "Seru Sekali",
                    status: "Belum Selesai",
                },
            ],
            formTodo: {
                task: null,
                priority: null,
                note: null,
                status: null,
            },
        };
    },
    methods: {
        save(){
            this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;        
        },
        cancel(){
            this.resetForm();
            this.dialog = false;
            this.dialogEdit = false;
            this.dialogDelete = false
        },
        saveEdit(){
            this.temp.task = this.formTodo.task;
            this.temp.priority = this.formTodo.priority;
            this.temp.note = this.formTodo.note;
            this.temp.status = this.formTodo.status;
            
            this.resetForm();
            this.dialogEdit = false;
        },
        resetForm(){
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
                status: null,
            };
        },
        editItem(item){
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note: item.note,
                status: item.status,
            };
            this.temp = item
            
            this.dialogEdit = true;
            
        },
        deleteItem(item){
            this.item = this.todos.indexOf(item)
            this.dialogDelete = true
        },
        confirm(){
            this.todos.splice(this.item, 1)
            this.dialogDelete = false
        }
    },
};
</script>

<style>
    .text {
        font-family: "Franklin Gothic Medium", "Arial Narrow", Arial, sans-serif;
        font-size: 40px;
        font-style: italic;
    }
</style>