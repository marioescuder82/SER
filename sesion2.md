# Sesión 1: Despliegue del Servidor de Correo (MTA y MDA)

## 1. Introducción
En esta sesión vamos a transformar vuestra instancia de AWS en un nodo de mensajería funcional. Instalaremos **Postfix** como nuestro agente de transferencia de correo (MTA) y **Dovecot** como agente de entrega local y servidor IMAP/POP3 (MDA).

**Requisito previo:** Debes tener acceso a tu instancia de AWS y el servicio DNS (Bind9) funcionando correctamente con un dominio propio (ej. `grupoX.smr.com`).



---

## 2. Fase 1: Preparación del Entorno (AWS)
Antes de tocar el terminal, debemos permitir que el tráfico de correo fluya hacia nuestra instancia. 

1.  Accede a la consola de AWS -> **Security Groups**.
2.  Edita las reglas de entrada y abre los siguientes puertos para cualquier IP (`0.0.0.0/0`):
    * **SMTP:** Puerto 25 (Envío entre servidores)
    * **POP3:** Puerto 110 (Recuperación de correo)
    * **IMAP:** Puerto 143 (Recuperación de correo moderna)
    * **SMTP Submission:** Puerto 587 (Envío desde clientes finales)

> **Reflexión:** ¿Por qué es necesario abrir el puerto 25 si mi intención es que mis compañeros me envíen correos desde sus propios servidores?

---

## 3. Fase 2: Instalación y Configuración de Postfix (MTA)
Postfix será el encargado de "hablar" con otros servidores.

1.  Actualiza los repositorios e instala el paquete `postfix`. 
2.  Durante el asistente, selecciona **"Sitio de Internet"**.
3.  El archivo principal de configuración es `/etc/postfix/main.cf`. Debes editarlo (con `sudo nano` o `vi`) y configurar los siguientes parámetros. Algunos ya existirán, otros deberás buscarlos o añadirlos:

| Parámetro | Descripción sugerida |
| :--- | :--- |
| `myhostname` | Tu FQDN completo (ej: mail.tunombre.com) |
| `mydomain` | El dominio que gestionas en el DNS |
| `myorigin` | ¿Qué dominio quieres que aparezca tras el @ al enviar? |
| `inet_interfaces` | Debe permitir conexiones de cualquier red (`all`) |
| `mydestination` | Lista de dominios que este servidor considera "propios" |
| `home_mailbox` | Configúralo como `Maildir/` (¡Cuidado con la mayúscula y la barra!) |

4. Tras cualquier cambio, recuerda: `sudo systemctl restart postfix`.

---

## 4. Fase 3: Instalación y Configuración de Dovecot (MDA)
Dovecot gestionará los buzones para que los usuarios puedan leer sus correos.

1. Instala los paquetes: `sudo apt install dovecot-imapd dovecot-pop3d`
2. La configuración de Dovecot está fragmentada en `/etc/dovecot/conf.d/`. Debes localizar y modificar:

* **Archivo `10-auth.conf`**: Busca la línea `disable_plaintext_auth`. Por defecto viene comentada o en `yes`. Para estas pruebas iniciales sin SSL, cámbiala a `no`.
* **Archivo `10-mail.conf`**: Localiza la variable `mail_location`. Debes configurarla para que apunte al mismo formato que elegiste en Postfix (`Maildir`). Investiga cuál es la sintaxis exacta para que busque en la carpeta personal del usuario.

3. Reinicia el servicio: `sudo systemctl restart dovecot`.

---

## 5. Fase 4: Usuarios y Comprobación de Puertos
El servidor de correo utiliza usuarios reales del sistema operativo para gestionar los buzones.

1.  Crea dos usuarios de prueba (ej: `alumno1` y `alumno2`) con el comando `adduser`.
2.  **Verificación de red:** Utiliza el comando `ss -ltn` o `netstat -plnt` para verificar que tu servidor está escuchando en los puertos 25, 110 y 143.

---

## 6. El Reto de la Sesión
Para dar la práctica por finalizada, debes demostrar lo siguiente:

1.  **Logs en vivo:** Abre una terminal secundaria y ejecuta `tail -f /var/log/mail.log`. Observa qué ocurre al reiniciar los servicios.
2.  **Estructura Maildir:** Si envías un correo de prueba (puedes usar el comando `mail` si instalas `mailutils`), verifica que en el `/home/usuario/` se ha creado automáticamente una carpeta llamada `Maildir` con las subcarpetas `new`, `cur` y `tmp`.

> **Pregunta final:** Si un compañero intenta enviarte un correo desde su servidor y recibe un error de "Connection timed out", ¿qué tres cosas revisarías primero? (Pista: una está en AWS, otra en el proceso y otra en el DNS).

---