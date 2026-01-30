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