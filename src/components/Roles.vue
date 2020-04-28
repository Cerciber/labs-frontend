<template>
  <div class="col-12 col-sm-10 col-md-8 offset-sm-1 offset-md-2">
    <div class="mt-5">
      <form class="border border-primary rounded form-inline" @submit="associate">

        <h2 class="col-12 text-center text-primary mt-3 mb-5">Roles</h2>

        <div class="form-group col-12 text-center">
          <span v-html="HTMLcontent"></span>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: "AddRole",
    data( ){
      return {
        password: '',
        role: '',
        roles: [],
        HTMLcontent: null
      }
    },
    beforeCreate( ){
      const rolesPath = '/usuarioRoles';
      console.log(this.$store.state.backURL + rolesPath)
      axios
        .post( this.$store.state.backURL + rolesPath , {}, {params: {access_token: localStorage.getItem( "token" )}})
        .then( response => {
          if( response.status !== 200 ){
            alert( "Error en la petición. Intente nuevamente" )
          }else{
            var aux = response.data;
            var response = ""
            //<label for="password" class="custom-label col-md-3">Contrase&ntilde;a</label>
            for (var i = 0; i < aux.length; i++) {
                response += '<label for="password" class=" text-center text-primary">' + aux[i].roleName + '</label>';
            }
            this.HTMLcontent = response;
            
          }
        }).catch( response => {
          alert( "No es posible conectar con el backend en este momento" );
        });
        
    },
    
    methods: {
      associate( event ){
        axios
          .post( this.buildURI( ), {
              password: this.password
            }, {
              params: {
                access_token: localStorage.getItem( "token" )
              }
            }
          ).then( response => {
            if( response.status !== 201 ){
              alert( "Error en la petición. Intente nuevamente" );
            }else{
              alert( "Se ha asignado exitosamente el nuevo rol" );
            }
          }).catch( response => {
            if( response.response.status === 401 ){
              alert( "¡Ups! Al parecer tu contraseña es incorrecta o la sesión ha finalizado" );
            }else if ( response.response.status === 400 ){
              alert( "¿Estás seguro de que aún no tienes ese rol asignado?" );
            }else{
              alert( "No es posible conectar con el backend en este momento" );
            }
          });
        event.preventDefault( );
      },
      buildURI( ){
        let associatePath = "/principal/roles/";
        return this.$store.state.backURL + associatePath + this.role;
      }
    }

  }
</script>

<style scoped>
  .form-inline .form-group{
    margin-bottom: 1rem;
  }
</style>
