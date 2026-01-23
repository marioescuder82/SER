# Sesión 1: Despliegue del Servidor de Correo (MTA y MDA)

## 1. Introducción
En esta sesión vamos a transformar vuestra instancia de AWS en un nodo de mensajería funcional. Instalaremos **Postfix** como nuestro agente de transferencia de correo (MTA).

**Requisito previo:** Debes tener acceso a tu instancia de AWS y el servicio DNS (Bind9) funcionando correctamente con un dominio propio (ej. `grupoX.smr.com`).



---

## 2. Fase 1: Preparación del Entorno (AWS)
Antes de tocar el terminal, debemos permitir que el tráfico de correo fluya hacia nuestra instancia. 

1.  Accede a la consola de AWS -> **Security Groups**.
2.  Edita las reglas de entrada y abre los siguientes puertos para cualquier IP (`0.0.0.0/0`):
    * **SMTP:** Puerto 25 (Envío entre servidores)

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

## 4. Fase 3: Usuarios y Comprobación de Puertos
El servidor de correo utiliza usuarios reales del sistema operativo para gestionar los buzones.

1.  Crea dos usuarios de prueba (ej: `alumno1` y `alumno2`) con el comando `adduser`.
2.  **Verificación de red:** Utiliza el comando `ss -ltn` o `netstat -plnt` para verificar que tu servidor está escuchando en el puerto 25.

---

# 5. Práctica: Despliegue de Infraestructura de Correo Interconectada

## 1. Escenario de Red
En esta práctica, el grupo (3 personas) debe simular una red corporativa completa. No trabajaremos con dominios locales aislados, sino con una estructura de **subdominios** dependientes de un **servidor DNS centralizado**.

### Roles del grupo:
* **Nodo DNS (Alumno A):** Gestiona la resolución de nombres para todo el grupo.
* **Nodo MTA 1 (Alumno B):** Gestiona el subdominio `profesor.tudominio.com`.
* **Nodo MTA 2 (Alumno C):** Gestiona el subdominio `alumno.tudominio.com`.

---

## 2. Fase 1: Infraestructura y Red (AWS)
Antes de configurar los servicios, la red debe permitir el tráfico necesario.

* **Reto 1:** Identificar qué puertos TCP/UDP deben abrirse en los *Security Groups* de AWS para que el DNS y el correo (SMTP) funcionen entre las instancias.
* **Reto 2:** El Alumno A debe configurar **BIND9** para que responda consultas de sus compañeros.
* **Reto 3:** Los Alumnos B y C deben configurar su sistema operativo para que **todas** sus consultas DNS se dirijan a la IP del Alumno A (Configuración de *resolver*).

> **Verificación:** Desde el nodo B, se debe poder resolver el registro MX del nodo C usando el comando `dig`.



---

## 3. Fase 2: Configuración del MTA (Postfix)
Cada servidor de correo debe ser capaz de enviar y recibir mensajes de forma autónoma.

* **Configuración:** Instalar y configurar Postfix.
* **Investigación:**
    1.  ¿Qué parámetro define qué dominios se consideran "locales" para el servidor?
    2.  ¿Cómo configurar Postfix para que use el formato **Maildir** en lugar de Mbox?
    3.  Asegurar que el servidor no sea un *Open Relay* pero permita la escucha en todas las interfaces.

---

## 4. Fase 3: Pruebas de Funcionamiento y Protocolo
Una vez los servicios estén en *running*, realizad las siguientes pruebas:

### Prueba A: Envío automatizado
Desde el Alumno B, enviar un correo a un usuario del Alumno C usando el comando `mail`. 
* *Evidencia:* Localizar el archivo de texto del correo en el sistema de archivos del Alumno C (ruta `/home/...`).

### Prueba B: El protocolo SMTP a mano
Desde el Alumno C, responder al Alumno B conectándose manualmente al puerto 25 (usando `telnet` o `nc`).
* **Reto:** Debes completar el "apretón de manos" SMTP (`HELO`, `MAIL FROM`, `RCPT TO`, `DATA`, etc.) sin errores.



---

## 5. Informe de Errores (Troubleshooting)
Durante la práctica, es probable que surjan problemas. Debéis documentar:
1.  Qué ocurre si el Alumno B tiene su propio nombre en `mydestination` pero intenta enviar a un compañero.
2.  Qué mensaje aparece en `/var/log/mail.log` cuando el DNS no resuelve el destino.
3.  Cómo verificar mediante comandos de red que los puertos 25 y 143 están realmente abiertos y escuchando.

---