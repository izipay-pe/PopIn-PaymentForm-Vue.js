# PopIn-PaymentForm-Vue.js

## Índice

- [1. Introducción](#1-introducción)
- [2. Requisitos previos](#2-requisitos-previos)
- [3. Despliegue](#3-despliegue)
- [4. Datos de conexión](#4-datos-de-conexión)
- [5. Transacción de prueba](#5-transacción-de-prueba)
- [6. Implementación de la IPN](#6-implementación-de-la-ipn)
- [7. Personalización](#7-personalización)
- [8. Consideraciones](#8-consideraciones)

## 1. Introducción

En este manual podrás encontrar una guía paso a paso para configurar un proyecto con **VUE** con la pasarela de pagos de IZIPAY. Te proporcionaremos instrucciones detalladas y credenciales de prueba para la instalación y configuración del proyecto, permitiéndote trabajar y experimentar de manera segura en tu propio entorno local.
Este manual está diseñado para ayudarte a comprender el flujo de la integración de la pasarela para ayudarte a aprovechar al máximo tu proyecto y facilitar tu experiencia de desarrollo.

<p align="center">
  <img src="https://github.com/izipay-pe/Imagenes/blob/main/formulario_popin/formulario_popin.png" alt="Formulario" width="350"/>
</p>

<a name="Requisitos_Previos"></a>

## 2. Requisitos previos

- Comprender el flujo de comunicación de la pasarela. [Información Aquí](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/start.html)
- Extraer credenciales del Back Office Vendedor. [Guía Aquí](https://github.com/izipay-pe/obtener-credenciales-de-conexion)
- Debe instalar la [versión de LTS node.js](https://nodejs.org/en).
- Para este proyecto utilizamos la herramienta Visual Studio Code.
  > [!NOTE]
  > Tener en cuenta que, para que el desarrollo de tu proyecto, eres libre de emplear tus herramientas preferidas.

## 3. Despliegue

### Clonar el proyecto:

```sh
git clone [https://github.com/izipay-pe/PopIn-PaymentForm-Vue.js.git]
```

### Ejecutar proyecto

- Ingrese a la carpeta raiz del proyecto.

- A continuación, instale el cliente vue-cli:

  ```bash
  npm install -g @vue/cli
  ```

  Más detalles en la página web de [vue-cli web-site](https://cli.vuejs.org/guide/installation.html).

- Agregue la dependencia con:

  ```bash
  npm install --save @lyracom/embedded-form-glue
  ```

- Ejecútelo y pruébelo con el comando:

  ```sh
  npm run serve
  ```

  ver el resultado en http://localhost:8080/

## 4. Datos de conexión

**Nota**: Reemplace **[CHANGE_ME]** con sus credenciales de `API REST` extraídas desde el Back Office Vendedor, ver [Requisitos Previos](#Requisitos_Previos).

- Editar en public/index.html en la sección HEAD.

  ```html
  <!-- tema y plugins. debe cargarse en la sección HEAD -->
  <link
  	rel="stylesheet"
  	href="~~CHANGE_ME_ENDPOINT~~/static/js/krypton-client/V4.0/ext/classic-reset.css"
  />
  <script src="~~CHANGE_ME_ENDPOINT~~/static/js/krypton-client/V4.0/ext/classic.js"></script>
  ```

- Edite el componente predeterminado src/app/AddForm.vue ingresando sus credenciales extraidas desde [Requisitos Previos](#Requisitos_Previos).

  ```javascript
  const endpoint = "~~CHANGE_ME_ENDPOINT~~";
  const publicKey = "~~CHANGE_ME_PUBLIC_KEY~~";
  const formToken = "DEMO-TOKEN-TO-BE-REPLACED";
  ```

  Puede generar un `formToken` de prueba ingresando a la pestaña `Pruébame` dentro del siguiente link: [Aquí](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/playground/Charge/CreatePayment/).

  ![tarjeta](/src/assets/formToken.png)

- Actualice los estilos dentro de src/App.vue style:

  ```html
  <style scoped>
  	.container {
  		display: flex;
  		flex-direction: row;
  		justify-content: center;
  		align-items: center;
  	}
  </style>
  ```

  ## 4.1 Interactuar con un endpoint propio

  Edite el componente predeterminado src/app/AddForm.vue, con el siguiente codigo si quiere interactuar con el formulario de pago, con un endpoint propio.

  ```html
  <template>
  	<div class="hello">
  		<h1>{{ msg }}</h1>
  		<div class="container">
  			<div id="myPaymentForm">
  				<div class="kr-embedded" kr-popin>
  					<div class="kr-pan"></div>
  					<div class="kr-expiry"></div>
  					<div class="kr-security-code"></div>
  					<div class="kr-form-error"></div>
  					<button class="kr-payment-button"></button>
  				</div>
  			</div>
  		</div>
  		<div data-test="payment-message">{{ message }}</div>
  	</div>
  </template>

  <script>
  	/* import embedded-form-glue library */
  	import KRGlue from "@lyracom/embedded-form-glue";
  	import axios from "axios";

  	export default {
  		name: "AttachForm",
  		props: {
  			msg: String,
  		},
  		data() {
  			return {
  				message: "",
  			};
  		},
  		mounted() {
  			const endpoint = "~~CHANGE_ME_ENDPOINT~~";
  			const publicKey = "~~CHANGE_ME_PUBLIC_KEY~~";
  			let formToken = "DEMO-TOKEN-TO-BE-REPLACED";

  			// Generate the form token
  			axios
  				.post("http://localhost:3000/createPayment", {
  					paymentConf: { amount: 10000, currency: "USD" },
  				})
  				.then((resp) => {
  					formToken = resp.data;
  					return KRGlue.loadLibrary(
  						endpoint,
  						publicKey
  					); /* Load the remote library */
  				})
  				.then(({ KR }) =>
  					KR.setFormConfig({
  						/* set the minimal configuration */
  						formToken: formToken,
  						"kr-language": "en-US" /* to update initialization parameter */,
  					})
  				)
  				.then(({ KR }) => KR.onSubmit(this.validatePayment)) // Custom payment callback
  				.then(({ KR }) =>
  					KR.attachForm("#myPaymentForm")
  				) /* create a payment form */
  				.then(({ KR, result }) => {
  					KR.showForm(result.formId);
  					this.ready = true;
  				}) /* show the payment form */
  				.catch(
  					(error) =>
  						(this.message = error + " (see console for more details)")
  				);
  		},
  		methods: {
  			/* Validate the payment data */
  			validatePayment(paymentData) {
  				axios
  					.post("http://localhost:3000/validatePayment", paymentData)
  					.then((response) => {
  						if (response.status === 200) this.message = "Payment successful!";
  					});
  				return false; // Return false to prevent the redirection
  			},
  		},
  	};
  </script>

  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  	.container {
  		display: flex;
  		flex-direction: row;
  		justify-content: center;
  		align-items: center;
  	}
  </style>
  ```

## 5. Transacción de prueba

Antes de poner en marcha su pasarela de pago en un entorno de producción, es esencial realizar pruebas para garantizar su correcto funcionamiento.

Puede intentar realizar una transacción utilizando una tarjeta de prueba con la barra de herramientas de depuración (en la parte inferior de la página).

<p align="center">
  <img src="https://i.postimg.cc/3xXChGp2/tarjetas-prueba.png" alt="Formulario"/>
</p>

- También puede encontrar tarjetas de prueba en el siguiente enlace. [Tarjetas de prueba](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/kb/test_cards.html)

## 6. Implementación de la IPN

> [!IMPORTANT]
> Es recomendable implementar la IPN para comunicar el resultado de la solicitud de pago al servidor del comercio.

La IPN es una notificación de servidor a servidor (servidor de Izipay hacia el servidor del comercio) que facilita información en tiempo real y de manera automática cuando se produce un evento, por ejemplo, al registrar una transacción.
Los datos transmitidos en la IPN se reciben y analizan mediante un script que el vendedor habrá desarrollado en su servidor.

- Ver manual de implementación de la IPN. [Aquí](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/kb/payment_done.html)
- Vea el ejemplo de la respuesta IPN con PHP. [Aquí](https://github.com/izipay-pe/Redirect-PaymentForm-IpnT1-PHP)
- Vea el ejemplo de la respuesta IPN con NODE.JS. [Aquí](https://github.com/izipay-pe/Response-PaymentFormT1-Ipn)

## 7. Personalización

Si deseas aplicar cambios específicos en la apariencia de la pasarela de pago, puedes lograrlo mediante la modificación de código CSS. En este enlace [Código CSS - Incrustado](https://github.com/izipay-pe/Server-Personalization-PopIn) podrá encontrar nuestro script para un formulario incrustado.

## 8. Consideraciones

Para obtener más información, echa un vistazo a:

- [Formulario incrustado: prueba rápida](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/quick_start_js.html)
- [Primeros pasos: pago simple](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/javascript/guide/start.html)
- [Servicios web - referencia de la API REST](https://secure.micuentaweb.pe/doc/es-PE/rest/V4.0/api/reference.html)
