<template>
<div class="container">
  <div class="hello">
    <h1 class="text-center">{{ msg }}</h1>

  <form class="form-inline">
  <div class="form-group mb-2">
    <label for="select" class="form-label"> Please select any user field</label>
  </div>
  <div class="form-group mx-sm-3 mb-2">
   <select id="select" class="form-control form-select"  v-model="SelectedUserkey" @change="onChange()">
      <option v-for="userkey in Object.keys(this.userslist[0])" v-bind:key="userkey" >{{userkey}}</option>
    </select>
  </div>
  <SearchAutocomplete class="col-sm-3"
     :SelectedUserkey= SelectedUserkey
     :actualitems =actualitemsArray
      :items= filteredInputItems
     @onChangeInput="onChangeInputText"
    />
</form>

  
    
 
    <br>

    <table class="table">
  <thead v-if="list.length > 0">
    <tr>
      <td scope="col"></td>
      <th scope="col">User Name</th>
      <th scope="col">Organization</th>
      <th scope="col">Tickets</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for="item in list"  v-bind:key="item._id">

      <td><a id="item._id" class="text-primary" v-on:click="onclickremove(item._id)">Remove</a></td>
       <td>
         <a class="text-primary" v-on:click="oncliklist(item._id)">{{item.name}}</a>
         </td>
      <td>
        <div v-for="org in item.org"  v-bind:key="org._id">
        {{org.name}}
        </div>
      </td>
      
      <td>
        <div v-for="tik in item.tickets"  v-bind:key="tik._id">
        {{tik.subject}}
        </div>

      </td>
    </tr>
    
  </tbody>
</table>
<div v-if="showuserdetail">
<div  id="userDetail" v-for="user in selectedUser" v-bind:key="user._id">
<strong> User Detalis</strong>
<br>Url: {{user.url}}
<br>External Id: {{user.external_id}}
<br>Name: {{user.name}}
<br>Alias: {{user.alias}}
<br>Created_at: {{user.created_at}}
<br>Active: {{user.active}}
<br>Verified:{{user.verified}}
<br>Shared: {{user.shared}}
<br>Locale: {{user.locale}}
<br>Timezone: {{user.timezone}}
<br>Last_login_at: {{user.last_login_at}}
<br>Email:{{user.email}}
<br>Phone: {{user.phone}}
<br>Signature: {{user.signature}}
<br>Organization_id: {{user.organization_id}}
<br>Tags: <div v-for="tag in user.tags"  v-bind:key="tag">{{tag}}</div>
<br>Suspended: {{user.suspended}},
<br>Role: {{user.role}}
</div>


  </div>
  </div>
  </div>
</template>

<script>
import users from '../json/users.json'
import organizations from '../json/organizations.json'
import tickets from '../json/tickets.json'
import SearchAutocomplete from '../components/SearchAutocomplete.vue'

export default {
  name: 'Customer',
  components: {
    SearchAutocomplete
  },
  props: {
    msg: String
   
  },
  data() {
    return {
      userslist: users,
      organizationslist: organizations,
      ticketslist: tickets,
      SelectedUserkey:'_id',
      filteredInputItems:[],
      actualitemsArray:[],
      selectedInputText: "",
      selectedUser:[],
      selectedorganisation:[],
      userTickets:[],
      showuserdetail:false,
      list: []
    };
    
  },
    methods: {
        filterSelectedUserKey()
        {
          return this.userslist ;
        },
        filterbyKey (item){
            return [item.[this.SelectedUserkey]].join("");
        },
         filterbyKeyforactual (item){
            let rObj = {}
            rObj[this.SelectedUserkey] = item.[this.SelectedUserkey]
             rObj["id"]= item._id
            return rObj
        },
        onChange(){
         // console.log(this.SelectedUserkey);
          this.filteredInputItems =  this.userslist.map(this.filterbyKey);
          this.actualitemsArray =  this.userslist.map(this.filterbyKeyforactual);
         console.log(this.actualitemsArray);
        },
         filterUserbyKey (item){
            return item.[this.SelectedUserkey]===this.selectedInputText;
        },
         onChangeInputText(selectedvalue){
          // this.selectedInputText= selectedvalue;
          console.log(selectedvalue);
        this.selectedUser= this.userslist.filter((item) => {
          return item._id == selectedvalue[0].id ;
            });
         console.log(this.selectedUser);
      
  this.selectedorganisation=  this.organizationslist.filter((item) => {
          return item._id == this.selectedUser[0].organization_id ;
            });
console.log("selected user ");
  //console.log(this.selectedUser);
           console.log( this.selectedorganisation);

            this.userTickets=  this.ticketslist.filter((item) => {
          return item.organization_id == this.selectedUser[0].organization_id ;
            });
             console.log( this.userTickets);
            
           let selectedUser = {}
            selectedUser["_id"] = this.selectedUser[0]._id
             selectedUser["name"]= this.selectedUser[0].name
              selectedUser["org"]= this.selectedorganisation
                selectedUser["tickets"]=  this.userTickets
         if(!this.list.some(item => item._id === this.selectedUser[0]._id))
         {
           this.list.push(selectedUser);
         }
         console.log("list")
          console.log(this.list)

        },
          oncliklist(id){
           this.selectedUser = this.userslist.filter((item) => {
          return item._id == id ;
            });
             this.showuserdetail= true;
        },
        onclickremove(id){
         var listtoremove= this.list.filter((item) => {
          return item._id == id ;
            });
        var index= this.list.indexOf(listtoremove);
         this.list.splice(index, 1);
             this.showuserdetail= false;
        },
       
    },
    mounted: function(){
     // console.log(this.userslist);
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
