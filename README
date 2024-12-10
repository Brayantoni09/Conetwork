from netmiko import ConnectHandler
import getpass

# Pedir información del switch inicial.strip()
host = input("Ingrese la IP del switch inicial: ")
username = input("Ingrese el nombre de usuario: ")
password = getpass.getpass("Ingrese su contraseña: ")

# Dirección MAC a buscar
mac_address = "f430.b9a0.c3e8" 

# Configuración base del switch
base_config = {
    "device_type": "cisco_ios",
    "username": username,
    "password": password,
}

# set cra una Lista de switches visitados para evitar bucles
visited_switches = set()


def buscar_mac(switch_ip):
    "Busca la dirección MAC en un switch y explora vecinos si es necesario."
    "Va a llamar la funcion y la va a modificar, no creara otra nueva"
    global visited_switches
    visited_switches.add(switch_ip)

    switch_config = base_config.copy()
    switch_config["host"] = switch_ip

    try:
        # Conectarse al switch
        connection = ConnectHandler(**switch_config)
        print(f"\nConectado al switch {switch_ip}")

        # Buscar la MAC en la tabla de direcciones
        command_mac = f"show mac address-table | include {mac_address}"
        mac_output = connection.send_command(command_mac)

        if mac_address in mac_output:
            print("$"*70, '\n')
            print(f"MAC {mac_address} encontrada en el switch {switch_ip}:")
            print(mac_output)

            # Extraer el puerto asociado
            port = mac_output.split()[-1]
            print(f"Puerto asociado: {port}", '\n')

            # Verificar si el puerto es un trunk
            trunk_command = "show interfaces trunk"
            trunk_output = connection.send_command(trunk_command)
            if port in trunk_output:
                print(f"La MAC {mac_address} parece estar en otro switch a través del puerto {port}.")
            else:
                print("$"*70, '\n')
                print(f"La MAC {mac_address} está conectada directamente al puerto {port}.")
                print("$"*70, '\n')
                connection.disconnect()
                return True  # Detener la búsqueda

        else:
            print(f"MAC {mac_address} no encontrada en el switch {switch_ip}.")

        # Descubrir switches vecinos
        print("Buscando vecinos con CDP...")
        cdp_output = connection.send_command("show cdp neighbors detail")
        for line in cdp_output.split("\n"):
            if "IP address" in line:
                neighbor_ip = line.split()[-1]
                if neighbor_ip not in visited_switches:
                    print(f"Vecino encontrado: {neighbor_ip}")
                    if buscar_mac(neighbor_ip):  # Llamada recursiva
                        connection.disconnect()
                        return True

        # Cerrar conexión
        connection.disconnect()

    except Exception as e:
        print(f"Error al conectar con {switch_ip}: {e}")
        return False


# Iniciar la búsqueda desde el switch inicial
if not buscar_mac(host):
    print(f"\nLa MAC {mac_address} no fue encontrada en la red.")
