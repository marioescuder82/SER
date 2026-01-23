# Sesión 3: Entrega de Correo (MDA) y Configuración de Dovecot

## 1. Introducción
En la sesión anterior logramos que los correos viajaran de un servidor a otro. Sin embargo, el usuario final no debería tener que entrar por SSH para leer sus correos con `cat`. 

Hoy instalaremos **Dovecot**, el agente encargado de gestionar los buzones y permitir que los clientes consulten sus mensajes mediante los protocolos **IMAP** y **POP3**.

---

## 2. Fase 1: Preparación y Protocolos
Antes de tocar el servidor, el grupo debe preparar el terreno.

* **Seguridad en AWS:** Investigad qué puertos utilizan los protocolos **IMAP** y **POP3** (en sus versiones estándar, no cifradas de momento). El Alumno A no necesita cambios, pero los Alumnos B y C deben abrir estos puertos en sus *Security Groups*.
* **El concepto MDA:** Investigad la diferencia entre IMAP y POP3. ¿Cuál de los dos deja los mensajes en el servidor y cuál los descarga al cliente?

---

## 3. Fase 2: Instalación y Configuración de Dovecot
Instalad el paquete `dovecot-imapd` y `dovecot-pop3d` en los servidores B y C.

### Retos de configuración (Investigación):
1.  **Habilitar protocolos:** Localizad el archivo donde se indica a Dovecot que debe escuchar en IMAP y POP3.
2.  **Sincronización con Postfix (Maildir):** Este es el punto crítico. Dovecot debe ir a buscar los correos exactamente al mismo sitio donde Postfix los deja. 
    * *Investigación:* Buscad el archivo `10-mail.conf`. ¿Cómo se le indica a Dovecot que use el formato `Maildir` y en qué ruta del usuario debe buscar?
3.  **Autenticación:** Por defecto, Dovecot puede exigir SSL/TLS. Para esta práctica, buscad cómo permitir la **autenticación en texto plano** y cómo desactivar la obligación de usar SSL en el archivo `10-auth.conf` y `10-ssl.conf`.



---

## 4. Fase 3: Pruebas de Acceso (La prueba del Telnet)
Antes de usar un cliente gráfico, debemos verificar que Dovecot responde.

1.  Desde vuestro PC (o desde la instancia del compañero), conectaos al puerto de IMAP usando `telnet` o `nc`.
2.  Intentad hacer un login manual. 
    * *Comando de pista:* `a1 LOGIN nombre_usuario contraseña`
3.  Si el servidor responde `OK Logged in`, ¡vuestro MDA funciona!

---

## 5. Fase 4: Integración Completa (MTA + MDA)
Ahora que ambos servicios funcionan, realizad un flujo completo:

1.  **Alumno B:** Envía un correo a `practica@alumno.tudominio.com` usando el comando `mail`.
2.  **Alumno C:** En lugar de buscar el archivo en la carpeta, debe verificar que puede "ver" ese correo conectándose por Telnet al servicio IMAP de su propio servidor.

---

## 6. Entregable de la Sesión
Para validar la práctica, el grupo debe mostrar:
1.  El archivo de configuración donde Dovecot y Postfix coinciden en la ruta del buzón.
2.  Una captura de pantalla o demostración de un **Login exitoso** vía Telnet al puerto IMAP.
3.  **El log de Dovecot:** Mostrad el archivo `/var/log/mail.log` (o el log específico de dovecot) donde se registre que un usuario ha iniciado sesión correctamente.