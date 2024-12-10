# Descripción del Código
#Por: Bryan Antonio Garcia Larez.
##Ingenieria en redes y telecomunicaciones de la universidad politecnica de durango.

##intruduccion.
Este script en Python utiliza la biblioteca **Netmiko** para conectarse a switches Cisco y realizar una búsqueda de una dirección MAC específica dentro de una red. Es útil en entornos de redes para rastrear dispositivos conectados y analizar la topología mediante el protocolo **CDP (Cisco Discovery Protocol)**.
Este trabajo se realizo con el aprendizaje obtenido con el mestro Eliud Bueno Moreno, como proyecto para la implementacion de la automatizacion en las redes.

---

## Funcionalidad Principal

El script realiza los siguientes pasos:

1. **Conexión al Switch Inicial:**
   - Solicita al usuario la dirección IP, el nombre de usuario y la contraseña para acceder al switch de inicio.
   - Configura la conexión utilizando las credenciales proporcionadas y la dirección IP del switch.

2. **Búsqueda de la Dirección MAC:**
   - Ejecuta el comando `show mac address-table | include <MAC>` para encontrar el puerto asociado a la dirección MAC especificada.
   - Verifica si el puerto es un **trunk** o si la dirección MAC está conectada directamente al switch.

3. **Exploración de Vecinos:**
   - Utiliza el comando `show cdp neighbors detail` para identificar los switches vecinos conectados.
   - Si la dirección MAC no se encuentra en el switch actual, el script se conecta recursivamente a los switches vecinos y repite la búsqueda.

4. **Evita Bucle Infinito:**
   - Mantiene un conjunto (`visited_switches`) de los switches ya visitados para evitar conexiones repetidas.

5. **Resultados:**
   - Imprime si la dirección MAC se encuentra en un switch, el puerto asociado, y si está conectada directamente o a través de un enlace trunk.
   - Si la MAC no se encuentra, notifica al usuario.

---

## Requisitos

- **Python 3.x**
- Biblioteca **Netmiko**
- Credenciales válidas para acceder a los switches Cisco
- Protocolo **CDP** habilitado en los switches

---

## Instalación de Dependencias

Asegúrate de tener instaladas las dependencias necesarias antes de ejecutar el script. Puedes hacerlo utilizando el siguiente comando:

```bash
pip install netmiko
