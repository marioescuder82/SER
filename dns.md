# 📑 Chuleta: Configuración de DNS con BIND9
**Servicios en Red** | Guía rápida de ficheros y registros

---

## 1. `/etc/bind/named.conf.options`
**Función:** Configuración global del servidor (seguridad, reenvío y escucha).

* **Forwarders:** IPs de servidores externos (ej. 8.8.8.8) a los que el servidor consulta si no conoce una dirección.
* **Listen-on:** Define en qué interfaces o IPs escucha el servidor (usualmente `any;`).
* **Seguridad:** Control de quién puede hacer consultas (allow-query).

## 2. `/etc/bind/named.conf.local`
**Función:** Declaración de las zonas (dominios) que gestiona este servidor.

* **Zona Directa:** Asocia el nombre del dominio con su fichero de datos.
    ```bash
    zone "clase.smr" {
        type master;
        file "/etc/bind/db.clase.smr";
    };
    ```
* **Zona Inversa:** Asocia la red (al revés) con su fichero de datos.
    ```bash
    zone "0.168.192.in-addr.arpa" {
        type master;
        file "/etc/bind/db.192.168.0";
    };
    ```

---

## 3. Fichero de Zona Directa (`db.dominio`)
**Función:** Traduce **Nombres de Host $\to$ Direcciones IP**.

| Registro | Descripción | Ejemplo |
| :--- | :--- | :--- |
| **SOA** | Inicio de autoridad | Datos de contacto y tiempos de refresco de la zona. |
| **NS** | Name Server | Indica quién es el servidor DNS de la zona (`ns1`). |
| **A** | Address | El registro básico: `mail  IN  A  1.2.3.4`. |
| **MX** | Mail Exchanger | **Vital para correo.** Indica el servidor de email y su prioridad: `@  IN  MX  10  mail`. |
| **CNAME** | Canonical Name | Un alias para un nombre ya existente (ej. `www` es alias de `srv1`). |

> **IMPORTANTE:** No olvides el **punto final** en los nombres de dominio completos (FQDN) para evitar que Bind añada el dominio automáticamente (ej. `mail.clase.smr.`).

---

## 4. Fichero de Zona Inversa (`db.red`)
**Función:** Traduce **Direcciones IP $\to$ Nombres de Host**.

* Esencial para que otros servidores de correo confíen en nosotros (evita ser marcado como Spam).
* **Registro PTR (Pointer):** El "puntero" que asocia el último octeto de la IP con el nombre del servidor.
    * *Ejemplo:* `4  IN  PTR  mail.clase.smr.` (Para la IP terminada en .4).

---

## 5. Comandos de Verificación (Antes de reiniciar)

| Comando | Función |
| :--- | :--- |
| `sudo named-checkconf` | Comprueba errores de sintaxis en los archivos `named.conf`. |
| `sudo named-checkzone <dominio> <fichero>` | Verifica que un fichero de zona específico no tenga errores. |
| `sudo systemctl restart bind9` | Aplica los cambios reiniciando el servicio. |
| `sudo journalctl -u bind9 -f` | Ver el log de errores en tiempo real si el servicio falla al arrancar. |


