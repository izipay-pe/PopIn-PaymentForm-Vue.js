<template>
  <div id="app">
    <h2>Ejemplo de un formulario PopIn con Vue.js</h2>
    
    <div>
      <img alt="Vue logo" src="./assets/Logo-161x66.png" />
    </div>  

    <img alt="Vue logo" src="./assets/logo.png"/> 
    
    
    <AddForm/>  
    
  </div>
</template>

<script>
import AddForm from './components/AddForm.vue'

export default {
  name: 'app',
  components: {
    AddForm
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

img{
  max-width: 100px;
  max-height: 161px;
}


/**Logo Cabecera**/
.kr-embedded[kr-popin] div.kr-popin-modal-header .kr-popin-modal-header-image img.kr-header-logo { 
    content: url("https://raw.githubusercontent.com/izipay-pe/Popin-Imagenes/main/logo-cabecera-200x200.png");
    transform: scale(1.2);
}


/**Logo footer**/
.kr-embedded[kr-popin].kr-help-button-inner-field .kr-popin-modal-footer .kr-whitelabel-logo img { 
    margin-top: -40px; border-radius: 4px !important;
	content: url("https://raw.githubusercontent.com/izipay-pe/Popin-Imagenes/main/logo%20color%20principal.png"); 
    max-height: 100% !important;
}


.kr-embedded[kr-popin], div.kr-popin-modal-header{
    border-color: #dfdcdc !important;
}

.kr-popin-modal-header-background{
    border-color: #fff !important;
    background-color: #fff !important;
}

/**Boton pagar**/
.kr-popin-button{
    background-color: #00A09D !important;
    border-radius: 4px !important;
    color: #ffffff !important;
}

.kr-popin-button:hover {
    background-color: #3DD2CE !important;
    border-radius: 4px !important;
    color: #ffffff !important;
}

/**Boton para pagar dentro del formulario flotante**/
.kr-embedded .kr-payment-button {
    color: #ffffff !important;
    background-color: #00A09D !important;
}

.kr-embedded .kr-payment-button:hover {
color: #ffffff !important;
background-color: #3DD2CE !important;
}

/**Boton pagar para que aparesca el modal**/
#botonPagar{
    color: #ffffff !important;
    background-color: #ff4240 !important;    
}

#botonPagar:hover{
    color: #ffffff !important;
    background-color: #ff0000 !important;    
}

</style>
