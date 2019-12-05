<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">
                          <h3 class="box-title">Users Table</h3>
                             <div class="card-tools">
                       <button class="btn btn-success" @click="newModel">Add new  <i class="fas fa-user"></i></button>
                  </div>

                        </div>

                    <div class="card-body">
                
          <div class="box">
        <!-- /.box-header -->
            <div class="box-body table-responsive no-padding">
              <table class="table table-hover">
                <tbody><tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                    <th>Registered at</th>
                  <th>Modify</th>
                </tr>
                <tr v-for="user in users" :key="user.id">
                  <td>{{user.id}}</td>
                  <td>{{user.name | capitalize}}</td>
                  <td>{{user.email}}</td>
                     <td><span class="label label-success">Approved</span></td>
                  <td>{{user.created_at}}</td>
               
                  <td><a href="#" @click="editModel(user)"><i class="fa fa-edit blue"></i></a>
                  <a href="#" @click="deleteUser(user.id)"><i class="fa fa-trash red"></i></a></td>
                </tr>
                
              </tbody></table>
            </div>
            <!-- /.box-body -->
        
          <!-- /.box -->
        </div>
                    </div>
                </div>
            </div>
        </div>
        <div id="addnew" class="modal fade" role="dialog">
  <div class="modal-dialog">

    <!-- Modal content-->
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal">&times;</button>
       
      </div>
      <div class="modal-body">
        <form @submit.prevent="editmode ? updateUser(): createUser()" @keydown="form.onKeydown($event)">
    <div class="form-group">
      <label>Name</label>
      <input v-model="form.name" type="text" name="name"
        class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
      <has-error :form="form" field="name"></has-error>
    </div>
    <div class="form-group">
      <label>Email</label>
      <input v-model="form.email" type="text" name="name"
        class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
      <has-error :form="form" field="email"></has-error>
    </div>
    <div class="form-group">
      <label>Password</label>
      <input v-model="form.password" type="password" name="password"
        class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
      <has-error :form="form" field="password"></has-error>
    </div>
    <button :disabled="form.busy"  v-show="!editmode" type="submit" class="btn btn-primary">Add new</button>
     <button :disabled="form.busy"  v-show="editmode" type="submit" class="btn btn-primary">Update</button>
  </form>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
      </div>
    </div>

  </div>
</div>
    </div>
    
</template>

<script>
    export default {
    data() {
           return {
             editmode: false,
              users:{},
              form: new Form({
                id:'',
                name: '',
                password: '',
                email: ''
      })
           }

      },
        methods: {
          updateUser() {
             this.$Progress.start();
             this.form.put('api/user/'+ this.form.id)
             .then(()=>{

               Fire.$emit('AfterCreate');
                $('#addnew').modal('hide');
               toast.fire({
                         icon: 'success',
                         title: 'Updated successfully'
                       })
  
                this.$Progress.finish();

             })
             .catch(() =>{
              this.$Progress.fail();

             })
          },
          newModel()
          {
            this.editmode = false;
            this.form.reset();
             $('#addnew').modal('show');

          },
            editModel(user)
          {
            this.editmode = true;
             this.form.reset();
             $('#addnew').modal('show');
              this.form.fill(user);
          },
            deleteUser(id){
             Swal.fire({
  title: 'Are you sure?',
  text: "You won't be able to revert this!",
  icon: 'warning',
  showCancelButton: true,
  confirmButtonText: 'Yes, delete it!',
  cancelButtonText: 'No, cancel!',
  reverseButtons: true
}).then((result) => {

this.form.delete('api/user/'+id).then(() => {
if (result.value) {
    Swal.fire(
      'Deleted!',
      'Your file has been deleted.',
      'success'
    )
    Fire.$emit('AfterCreate');
  } 

}) 
.catch(() => {
Swal.fire(
      'Cancelled',
      'Your imaginary file is safe :)',
      'error'
    )

})
  
})

            },

            loadUsers() {
            axios.get('api/user').then(({data}) => (this.users = data.data))
            }, 
         createUser() {
              this.$Progress.start();
             this.form.post('api/user')
             .then(()=>{

               Fire.$emit('AfterCreate');
             $('#addnew').modal('hide');
              toast.fire({
    icon: 'success',
    title: 'User created successfully'
  })
  
         this.$Progress.finish();

             })
             .catch(() =>{


             })
             
         }

        },
        created() {
            
           this.loadUsers();
            Fire.$on('AfterCreate',() => { this.loadUsers();
            });
         //  setInterval(() =>  this.loadUsers(),3000);
        }
    }
</script>