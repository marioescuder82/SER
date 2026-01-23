# Apuntes: Fundamentos del Servicio de Correo Electrónico
**Asignatura:** Servicios en Red (SMR)  
**Tema:** Protocolos y Arquitectura de Correo  

---

## 1. Introducción: La Analogía del Mundo Real

Para comprender el funcionamiento técnico del correo electrónico, es útil compararlo con el **correo postal tradicional**. El email no viaja directamente de un ordenador a otro de forma mágica; pasa por una cadena de intermediarios.

| Concepto Digital | Analogía Postal |
| :--- | :--- |
| **Email** | La carta física. |
| **MUA (Cliente)** | Tu escritorio y bolígrafo (donde escribes). |
| **MTA (Servidor)** | La Oficina de Correos y centro de clasificación. |
| **SMTP (Protocolo)** | El camión de transporte entre oficinas. |

---

## 2. Los Actores: ¿Quién es quién?

En la arquitectura cliente-servidor del correo, distinguimos dos tipos de software principales:

### A. MUA (Mail User Agent) – El Cliente
Es el programa que interactúa directamente con el usuario.
* **Función:** Permite redactar, organizar y visualizar mensajes.
* **Ejemplos:** Mozilla Thunderbird, Microsoft Outlook, Apple Mail.
* **Webmail:** (Gmail, Outlook.com) funcionan como MUAs a través del navegador.

### B. MTA (Mail Transfer Agent) – El Servidor de Transporte
Es el software que gestiona el tráfico de correos en la red.
* **Función:** Enruta los mensajes. Decide si el correo es para un usuario local o si debe enviarlo a otro servidor (otro dominio).
* **Ejemplos:** Postfix, Microsoft Exchange, Sendmail.

> **Nota:** Existe también el **MDA (Mail Delivery Agent)**, un sub-componente que recibe el correo del MTA y lo deposita finalmente en el buzón del usuario.

---

## 3. Los Protocolos: El Idioma del Correo

Los protocolos se dividen según la dirección del mensaje: **Envío** o **Recepción**.

### Fase 1: Envío (Push) - SMTP
**SMTP (Simple Mail Transfer Protocol)** es el estándar para mover el correo.
* **Dirección:** Siempre "empuja" el correo hacia afuera (Cliente $\to$ Servidor, o Servidor $\to$ Servidor).
* **Puertos habituales:** 25 (entre servidores), 587 (envío autenticado de clientes).
* **Resumen:** Solo transporta, no sirve para leer correos recibidos.

### Fase 2: Recepción (Pull) - POP3 vs IMAP
Una vez el correo llega al servidor de destino, el usuario necesita un protocolo para acceder a él.

#### **POP3 (Post Office Protocol v3)**
* **Filosofía:** "Descargar y borrar".
* **Funcionamiento:** Descarga los mensajes al dispositivo local y (generalmente) los elimina del servidor.
* **Ventaja:** Lectura offline, ahorra espacio en servidor.
* **Desventaja:** Mala sincronización entre dispositivos (si lees en el móvil, no aparece leído en el PC). Si pierdes el dispositivo, pierdes los correos.

#### **IMAP (Internet Message Access Protocol)**
* **Filosofía:** "Gestión remota / Sincronización".
* **Funcionamiento:** Visualizas los correos directamente en el servidor.
* **Ventaja:** Ideal para múltiples dispositivos. El estado (leído/borrado) se sincroniza en todas partes.
* **Desventaja:** Requiere conexión a internet constante para acceder al histórico (salvo caché local) y consume más espacio en el servidor.

---

## 4. El Viaje de un Correo: Ejemplo Práctico

Caso: **Ana (ana@empresaA.com)** envía un email a **Luis (luis@empresaB.com)**.

1.  **Redacción:** Ana escribe el correo en su MUA (ej. Thunderbird).
2.  **Envío Local:** El MUA de Ana contacta con su servidor (MTA de empresaA) usando **SMTP**.
3.  **Enrutamiento:** El MTA de empresaA consulta al DNS para localizar al servidor de `empresaB.com`.
4.  **Transferencia:** El MTA de empresaA envía el correo al MTA de empresaB usando **SMTP** a través de Internet.
5.  **Almacenamiento:** El MTA de empresaB recibe el mensaje y lo guarda en el buzón de Luis.
6.  **Acceso:** Luis abre su móvil. Su MUA conecta con el servidor usando **IMAP** (o POP3) para ver el mensaje.

---

## 5. Resumen Comparativo

| Concepto | Rol | Dirección | Analogía |
| :--- | :--- | :--- | :--- |
| **MUA** | Interfaz Usuario | Cliente | Buzón de casa |
| **MTA** | Servidor de Correo | Servidor | Oficina Postal |
| **SMTP** | Transporte | Envío (Push) | Camión de reparto |
| **POP3** | Acceso | Recepción (Descarga) | Mudanza (lo llevo todo) |
| **IMAP** | Acceso | Recepción (Sincro) | Gafas de larga distancia |

## 6. Actividades

- [Sesión 1 - Preparando el entorno de trabajo](sesion1.md)
- [Sesión 2 - Instalación de POSTFIX y DOVECOT](sesion2.md)


