<template>
    <div class="container container-task">
        <div class="row">
            <div class="col-md-6">
                <h2>Lista de tareas</h2>
                <table class="table text-center"><!--Creamos una tabla que mostrará todas las tareas-->
                        <thead>
                            <tr>
                                <th scope="col">Nombre</th>
                                <th scope="col">Descripción</th>
                                <th scope="col">Persona</th>
                                <th scope="col">Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="task in arrayTasks" :key="task.id"> <!--Recorremos el array y cargamos nuestra tabla-->
                                <td v-text="task.name"></td>
                                <td v-text="task.description"></td>
                                <td v-text="task.person"></td>
                                <td>
                                    <button class="btn btn-info" @click="" data-toggle="modal"
                                            data-target="#modalguardartarea" id="boton_modal">
                                        Guardar
                                    </button>

                                    <button class="btn btn-warning" @click="loadFieldsUpdate(task)" data-toggle="modal"
                                            data-target="#modalmodificartarea" id="boton_modal">
                                        Modificar
                                    </button>

                                    <button class="btn btn-danger" @click="deleteTask(task)">
                                        Borrar
                                    </button>
                                </td>
                            </tr>
                        </tbody>
                </table>
            </div>
            <div class="col-md-6">
                <div class="container-buttons">
                    <br>
                    <br>
                    <br>
                    <br>
                    <center>
                        <button type="button" class="btn btn-success btn-lg" data-toggle="modal"
                                data-target="#modaltarea" id="boton_modal" style="border-radius:5px;">
                            Añadir Tarea
                        </button>
                    </center>
                </div>

                <div class="modal fade" id="modaltarea" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
                    <div class="modal-dialog" role="document">
                        <div class="modal-content">
                            <div class="modal-header">
                                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                    <span aria-hidden="true">&times;</span>
                                </button>
                                <h4 class="modal-title" id="myModalLabel"></h4>
                            </div>
                            <div class="modal-body">
                                <div class="container">
                                <h1 style="text-align: center;color:black;font-size:40px;margin-bottom:10px;">
                                    Añadir Nueva Tarea
                                </h1>
                                <hr>
                                    <div class="form-group"><!-- Formulario para la creación o modificación de nuestras tareas-->
                                        <label>Nombre</label>
                                        <input v-model="name" type="text" class="form-control">

                                        <label>Descripción</label>
                                        <input v-model="description" type="text" class="form-control">

                                        <label>Persona</label>
                                        <input v-model="person" type="text" class="form-control">
                                    </div>

                                </div>
                            </div>
                            <div class="modal-footer">
                                <button v-if="update == 0" @click="saveTasks()" class="btn btn-success" data-dismiss="modal">
                                    Añadir
                                </button>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="modal fade" id="modalmodificartarea" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
                    <div class="modal-dialog" role="document">
                        <div class="modal-content">
                            <div class="modal-header">
                                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                    <span aria-hidden="true">&times;</span>
                                </button>
                                <h4 class="modal-title" id="myModalLabel"></h4>
                            </div>
                            <div class="modal-body">
                                <div class="container">
                                <h1 style="text-align: center;color:black;font-size:40px;margin-bottom:10px;">
                                    Modificar Tarea
                                </h1>
                                <hr>
                                    <div class="form-group"><!-- Formulario para la creación o modificación de nuestras tareas-->
                                        <label>Nombre</label>
                                        <input v-model="name" type="text" class="form-control">

                                        <label>Descripción</label>
                                        <input v-model="description" type="text" class="form-control">

                                        <label>Persona</label>
                                        <input v-model="person" type="text" class="form-control">
                                    </div>

                                </div>
                            </div>
                            <div class="modal-footer">
                                <button v-if="update != 0" @click="updateTasks()" class="btn btn-warning" data-dismiss="modal">
                                    Actualizar
                                </button>
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>


<script>
    export default {
        data(){
            return{
                name:"",
                description:"",
                person:"",
                update:0,
                arrayTasks:[],
            }
        },
        methods:{
            getTasks(){
                let me =this;
                let url = '/tareas'
                axios.get(url).then(function (response) {
                    //creamos un array y guardamos el contenido que nos devuelve el response
                    me.arrayTasks = response.data;
                })
                .catch(function (error) {
                    // handle error
                    console.log(error);
                });
            },
            saveTasks(){
                let me =this;
                let url = '/tareas/guardar'
                axios.post(url,{
                    'name':this.name,
                    'description':this.description,
                    'person':this.person,
                }).then(function (response) {
                    me.getTasks();//llamamos al metodo getTask(); para que refresque nuestro array y muestro los nuevos datos
                    me.clearFields();//Limpiamos los campos e inicializamos la variable update a 0
                })
                .catch(function (error) {
                    console.log(error);
                });

            },
            updateTasks(){
                let me = this;
                axios.put('/tareas/actualizar',{
                    'id':this.update,
                    'name':this.name,
                    'description':this.description,
                    'person':this.person,
                }).then(function (response) {
                   me.getTasks();//llamamos al metodo getTask(); para que refresque nuestro array y muestro los nuevos datos
                   me.clearFields();//Limpiamos los campos e inicializamos la variable update a 0
                })
                .catch(function (error) {
                    console.log(error);
                });
            },
            loadFieldsUpdate(data){
                this.update = data.id
                let me =this;
                let url = '/tareas/buscar?id='+this.update;
                axios.get(url).then(function (response) {
                    me.name= response.data.name;
                    me.description= response.data.description;
                    me.person= response.data.person;

                })
                .catch(function (error) {
                    // handle error
                    console.log(error);
                });
            },
            deleteTask(data){
                let me =this;
                let task_id = data.id
                if (confirm('¿Seguro que deseas borrar esta tarea?')) {
                    axios.delete('/tareas/borrar/'+task_id
                    ).then(function (response) {
                        me.getTasks();
                    })
                    .catch(function (error) {
                        console.log(error);
                    });
                }
            },
            clearFields(){
                this.name = "";
                this.description = "";
                this.person = "";
                this.update = 0;
            }
        },
        mounted() {
           this.getTasks();
        }
    }
</script>
