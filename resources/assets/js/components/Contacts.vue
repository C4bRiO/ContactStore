<template>
<div class="container">
    <br>
    <div class="row">
        <div class="col-md-6 offset-md-3">
            <h1 class="text-center">Add Contact</h1>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
            <form action="#" @submit.prevent="edit ? updateContact(contact.id) : createContact()">
                <div class="form-group">
                    <label>Name</label>
                    <input v-model="contact.name" type="text" name="name" class="form-control" autocomplete="name">
                </div>
                <div class="form-group">
                    <label>Email</label>
                    <input v-model="contact.email" type="text" name="email" class="form-control" autocomplete="email">
                </div>
                    <div class="form-group">
                    <label>Phone</label>
                    <input v-model="contact.phone" type="text" name="phone" class="form-control" autocomplete="tel-national">
                </div>
                <div class="row">
                    <div class="col-md-12">
                        <div class="form-group">
                            <button v-show="!edit" type="submit" class="btn btn-primary float-right">New Contact</button>
                            <button v-show="edit" type="submit" class="btn btn-primary float-right">Update Contact</button>
                        </div>
                    </div>
                </div>
                
            </form>
        </div>
    </div>
    <div class="row align-items-center">    
        <div class="col-md-6 offset-md-3">
            <h1 class="text-center">Contacts</h1>
        </div>
        <div class="col-md-12">    
            <ul class="list-group">
                <li class="list-group-item" v-for="contact in list"  v-bind:key="contact.id">
                <strong  v-bind="contact">{{contact.name}}</strong> {{contact.email}} {{contact.phone}}
                    <button @click="showContact(contact.id)" class="btn btn-default btn-xs" >Edit</button>
                    <button @click="deleteContact(contact.id)" class="btn btn-danger btn-xs">Delete</button>
                </li>
            </ul>
        </div>    
    </div>    
</div>
    
</template>

<script>
    export default{
        data: function(){
            return {
                edit:false,
                list:[],
                contact:{
                    id:'',
                    name:'',
                    email:'',
                    phone:''
                }
            }
        },
        mounted: function(){
            console.log('Contacts Component Loaded');
            this.fetchContactList();
        },
        methods: {
            fetchContactList: function(){
                console.log('Fetching Contacts');
                axios.get('api/contacts')
                .then((response) =>{
                    console.log(response.data);
                    this.list = response.data;

                })
                .catch((error) =>{
                    console.log(error);
                });
                return;
            },
            createContact: function(){    
                console.log('creating contact ....');
                let self = this;
                let params =Object.assign({},self.contact);

                axios.post('api/contact/store',params)
                    .then(function(){
                        self.contact.name = '';
                        self.contact.email='';
                        self.contact.phone='';
                        self.edit = false;

                        self.fetchContactList();
                    })
                    .catch(function(error){
                        console.log(error);
                    });
                return;
            },
            showContact: function(id){
                console.log('Show selected contact');
                let self = this;
                axios.get('api/contact/'+id)
                    .then(function(response){
                        self.contact.id = response.data.id;
                        self.contact.name = response.data.name;
                        self.contact.email = response.data.email;
                        self.contact.phone = response.data.phone;
                    })
                    self.edit  = true;
            },
            updateContact:function(id){
                console.log('updating contact...');
                let self = this;
                let params =Object.assign({},self.contact);

                axios.patch('api/contact/'+id,params)
                    .then(function(){
                        self.contact.name = '';
                        self.contact.email='';
                        self.contact.phone='';
                        self.edit = false;

                        self.fetchContactList();
                    })
                    .catch(function(error){
                        console.log(error);
                    });                
            },
            deleteContact:function(id){

                axios.delete('api/contact/'+id)
                    .then(function(response){
                        self.fetchContactList();
                    })
                    .catch(function(error){
                        console.log(error);
                    })
            }
        }
    }

</script>