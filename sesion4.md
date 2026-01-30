# Sesión 4: Uso de Mutt como Cliente de Correo en Terminal

## 1. Instalación y Configuración Inicial
Mutt necesita saber quién eres y dónde está tu correo para no dar errores de "Formato de mensaje" o "Encrypted connection unavailable".

Instalar Mutt:

```bash
sudo apt update && sudo apt install mutt -y
```

Crear el archivo de configuración personal: Crea el archivo .muttrc en el home de tu usuario:

```bash
nano ~/.muttrc
```

Pegar la siguiente configuración básica: (Sustituye los datos por los tuyos)

```
# Configuración de identidad
set from = "usuario@tudominio.com"
set realname = "Tu Nombre Real"

# Ruta del buzón local (Dovecot)
set folder = "~/Maildir"
set spoolfile = "~/Maildir"

# Forzar envío local vía Postfix
set sendmail = "/usr/sbin/sendmail"

# Desactivar exigencia de TLS para pruebas en laboratorio
set ssl_starttls = no
set ssl_force_tls = no
```

## 2. Cómo Leer Correos (Recepción)
Como ya hemos configurado la ruta spoolfile hacia tu Maildir, simplemente entra en el programa con el comando ```mutt```

Usa las flechas para moverte por la lista de correos.

Pulsa Enter para leer un correo.

Pulsa i para salir del correo y volver al índice.

Pulsa q para salir de Mutt.

## 3. Cómo Enviar Correos (Emisión)

### A. Desde la interfaz de Mutt (Interactivo):
Pulsa m para redactar.

To: Escribe el correo del compañero (ej: profe@profesor.com).

Subject: Escribe el asunto.

Se abrirá un editor de texto (Nano). Escribe tu mensaje, guarda y sal (Ctrl+O, Enter, Ctrl+X).

Estarás en la pantalla de envío. Pulsa y para enviar.

### B. Directamente desde la terminal (Modo rápido):
Ideal para comprobar conectividad con el compañero:

```bash
echo "Hola, este es un mensaje de prueba" | mutt -s "Prueba de red" compañero@tudominio.com
```