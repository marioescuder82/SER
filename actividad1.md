# Actividad Práctica: Infraestructura Cloud y DNS para Servicios de Correo
**Asignatura:** Servicios en Red  
**Ciclo:** Sistemas Microinformáticos y Redes (SMR)  

---

## 1. Introducción
Para que un servicio de correo electrónico funcione, no basta con instalar el software. El pilar fundamental es el **DNS (Domain Name System)**. Sin los registros adecuados, los servidores de Gmail, Outlook o cualquier otro no sabrán a qué IP deben enviar los mensajes destinados a tu dominio.

En esta práctica prepararás el terreno en la nube (AWS) configurando la red y el servidor de nombres.

---

## 2. Fase 1: Despliegue en AWS (EC2) y Seguridad
El primer paso es crear el servidor físico (virtualizado) y configurar sus perímetros de seguridad.

1.  **Instancia:** Lanza una instancia EC2 `t3.micro` con **Ubuntu Server** (o Debian).
2.  **IP Elástica:** Asocia una *Elastic IP* a tu instancia para evitar que la dirección cambie al reiniciar.
3.  **Security Groups (Firewall):** Configura las reglas de entrada (*Inbound Rules*). 
    * Deberás abrir los puertos necesarios para: **SSH**, **DNS**, **SMTP**, **POP3** e **IMAP**.

---

## 3. Fase 2: Configuración del Servidor DNS (Bind9)
Accede por SSH a tu máquina y realiza la instalación del servicio DNS.

### Pasos Técnicos:
1.  **Instalación:**
    ```bash
    sudo apt update && sudo apt install bind9 bind9utils bind9-doc -y
    ```
2.  **Configuración de Zona:** Declara tu dominio (ej. `tuapellido.com`) en el archivo `/etc/bind/named.conf.local`.
3.  **Archivo de Registros:** Crea el archivo de zona y añade los siguientes registros esenciales:
    * **Registro A:** Vincula el nombre de tu servidor (ej. `mail.tuapellido.com`) con la IP pública.
    * **Registro MX (Mail Exchanger):** Define qué servidor gestiona el correo. 
        * *Sintaxis ejemplo:* `@  IN  MX  10  mail.tuapellido.com.`
4.  **Reinicio:** Verifica la sintaxis y reinicia el servicio:
    ```bash
    sudo named-checkconf
    sudo systemctl restart bind9
    ```

---

## 4. Fase 3: Documento de Entrega (Cuestionario)
Crea un documento en formato **LibreOffice Writer** (o PDF) donde respondas de forma técnica y razonada a los siguientes puntos:

### Sección A: Seguridad de Red
* **A.1.** Enumera todas las **Reglas de Entrada** que has creado en AWS. Indica: *Puerto | Protocolo (TCP/UDP) | Servicio*.
* **A.2.** ¿Es necesario modificar las **Reglas de Salida**? ¿Por qué un servidor de correo podría tener problemas si bloqueamos las conexiones de salida hacia el puerto 25?
* **A.3.** ¿Es necesario configurar el firewall dentro del Ubuntu Server? ¿Es necesario? ¿Por qué?

### Sección B: Configuración DNS
* **B.1.** Explica la función del registro **MX**. ¿Qué pasaría si configuramos un servidor de correo pero olvidamos crear este registro en el DNS?
* **B.2.** ¿Qué es la **Prioridad** en un registro MX (ese número "10" que solemos poner) y para qué sirve tener más de uno?

[Configuración de DNS con Bind9](dns.md)

---

## 5. Fase 4: Verificación Final
Antes de entregar, debes demostrar que tu infraestructura es "visible" desde el exterior. Realiza estas pruebas desde la terminal de **tu ordenador personal** (no desde el servidor):

1.  **Prueba DNS:** Comprueba que tu servidor responde correctamente quién es el encargado del correo:
    ```bash
    nslookup -type=MX tu-dominio.com IP_PUBLICA_AWS
    ```
2.  **Prueba de Conectividad:** Verifica que el puerto DNS está abierto y escuchando:
    ```bash
    nc -zv IP_PUBLICA_AWS 53
    ```

---

> [!TIP]
> **Recordatorio de Seguridad:** No abras el puerto 22 (SSH) a todo el mundo (`0.0.0.0/0`) si quieres seguir buenas prácticas; intenta limitarlo a tu IP actual si es fija, aunque para esta práctica de clase se permite apertura total por simplicidad.


