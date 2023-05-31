# removeViewLater
Pequeño programa para borrar la lista de Ver Más Tarde en YouTube

## Problema

Tengo (tenía) 690 vídeos pendientes de ver en YouTube. Al final, no tengo tiempo o cuando los queiro ver, busco de nuevo la información. Mejor partir de cero. ¿No?

Busqué información sobre como eliminar masivamente los vídeos, pero lo que encontré ya no funciona. Hay que hacer algo totalmente nuevo.

## Diagnóstico

Hoy (literalmente hoy, 30 de mayo), la lista *Ver más tarde* presenta un menú contextual que aparece al presionar un ícono de tres puntos, donde una de las opciones dice "Eliminar de Ver más tarde". Este menú contextual existe solo cuando se presiona el ícono de tres puntos.

Entonces hay que buscar el ícono de los tres puntos, ejecutar el evento *click()* y luego buscar en el menú contextual el item que corresponde a "Eliminar de Ver más tarde" y ejecutar el evento *click()*.

La sección con la lista de vídeos se dibuja en un bloque llamado *ytd-item-section-renderer*. Luego, los botones de tres puntos son un bloque *button* con clase *yt-icon-button*.

Al ejecutar el evento click() del botón de tres puntos, el menú contextual aparece más abajo. Cada ítem del menú se dibuja en un bloque *ytd-menu-service-item-renderer* y no tienen clases o atributos para distinguir el que hay que presionar. Entonces debo buscar por el texto interior usando la propiedad *innerText*.

Finalmente hay que coordinar esto para que se ejecute con un retardo, ya que al ejecutar el evento *click()* el menú contextual tarda unos milisegundos en aparecer. Además, después de ejecutar el evento *click()* del ítem del menú contextual que me interesa, pasan otros milisegundos antes de que el vídeo desaparezca de la lista.

## Solución

Este pequeño código que borra la lista completa. Tiene dos funciones simples que hacen lo siguiente>
* La función *findFirst()* busca el primer botón de tres puntos de la lista de vídeos y ejecuta su evento *click()*. Después de 300ms ejecuta la función *findRemove()*. 
* La función *findRemove()* busca el ítem que contiene el texto "Eliminar de Ver más tarde" y ejecuta su evento *click()*, lo que elimina el vídeo de la lista. Luego de 300ms ejecuta la función *findFirst()* nuevamente para buscar el nuevo primer vídeo a eliminar.

Para ejecutar este código, abrí la lista de YouTube "Ver más tarde", abrí la consola, copié el código y lo pegué en la consola. Después de unos segundos, está todo borrado.

````javascript
function findFirst() {
	let l=document.querySelector("ytd-item-section-renderer button.yt-icon-button");
	if (l) {
		l.click();
		setTimeout(findRemove,300);
	}
}

function findRemove() {
	let l=Array.from(document.querySelectorAll("ytd-menu-service-item-renderer"))
		.find(e=>e.innerText.trim()=="Eliminar de Ver más tarde");
	if (l) {
		l.click();
		setTimeout(findFirst, 300);
	}
}

findFirst();
````
