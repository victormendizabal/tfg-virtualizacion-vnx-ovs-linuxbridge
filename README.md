# Desarrollo de escenarios Ethernet virtualizados con Linux Bridge y Open vSwitch

Este repositorio contiene los escenarios de virtualización de red desarrollados como parte del Trabajo Fin de Grado (TFG) **"Desarrollo de escenarios de redes Ethernet con soporte de VLANes en Linux: implementación y comparativa mediante Open vSwitch y Linux Bridge"**, realizado en la Escuela Técnica Superior de Ingenieros de Telecomunicación (ETSIT-UPM).

El objetivo del proyecto es desplegar topologías Ethernet virtualizadas sobre GNU/Linux para estudiar y comparar el comportamiento de dos tecnologías de conmutación de Capa 2 ampliamente utilizadas:

* **Linux Bridge**
* **Open vSwitch (OVS)**

## Requisitos previos

Para ejecutar los escenarios es necesario disponer de un sistema GNU/Linux con las siguientes herramientas instaladas:

* **VNX (Virtual Network over LinuX)** para la orquestación de escenarios.
* **LXC (Linux Containers)** para la virtualización de nodos.
* **Open vSwitch**.
* **bridge-utils**.
* **tcpdump**.
* **iperf3**.
* **sysstat**.

---

## Preparación de la imagen base

Los contenedores LXC se instancian a partir de una imagen Ubuntu mínima. Antes de desplegar los escenarios es necesario instalar las dependencias utilizadas durante las pruebas.

### Configuración temporal de DNS

```bash
echo "nameserver 8.8.8.8" | sudo tee /usr/share/vnx/filesystems/rootfs_lxc_ubuntu64/rootfs/etc/resolv.conf
```

### Instalación de dependencias

```bash
sudo chroot /usr/share/vnx/filesystems/rootfs_lxc_ubuntu64/rootfs /bin/bash

apt update

apt install -y \
    openvswitch-switch \
    bridge-utils \
    tcpdump \
    iperf3 \
    sysstat
```

---

## Estructura de los Escenarios (Archivos XML)

Los archivos se dividen según los criterios de evaluación del proyecto:
**Escenario 1. Prueba de concepto**
* `escenario-1.xml`: Topología base (híbrida) que establece una prueba de concepto para verificar la conectividad de extremo a extremo.

**Escenario 2. Interoperabilidad y Segmentación (VLAN)**
* `escenario-vlans.xml`: Segmentación estricta de dominios de difusión mediante el estándar IEEE 802.1Q a través de enlaces troncales.

**Escenario 3. Prevención de Bucles (STP / RSTP) (Escenario 3)**
* `escenario-stp.xml`: Plantilla base de topología en anillo con tres nodos.
* `escenario-stp-lb.xml`: Evaluación del protocolo STP clásico utilizando exclusivamente Linux Bridge.
* `escenario-stp-ovs.xml`: Evaluación del protocolo STP clásico utilizando exclusivamente Open vSwitch.
* `escenario-stp-mixto.xml`: Topología híbrida para validar el intercambio de tramas de control (BPDU) y la convergencia conjunta.
* `escenario-rstp-ovs.xml`: Evaluación de los tiempos de convergencia rápida mediante el protocolo RSTP (IEEE 802.1w) sobre Open vSwitch.

**Escenario 4. Pruebas de Rendimiento (Throughput y CPU)**
* `escenario-lineal-lb.xml`: Topología lineal para inyección masiva de tráfico y monitorización de interrupciones del núcleo en Linux Bridge.
* `escenario-lineal-ovs.xml`: Topología lineal análoga para evaluar el estrés computacional del *datapath* en Open vSwitch.

## Modo de Uso

Para desplegar cualquiera de los escenarios, utiliza el comando `vnx` con el parámetro de construcción y apunta al archivo XML deseado. Por ejemplo:

## Despliegue de escenarios

Para desplegar cualquier escenario se utiliza el comando `vnx` indicando el fichero XML correspondiente.

### Ejemplo: despliegue del escenario VLAN

```bash
sudo vnx -f escenario-vlans.xml -v --create
```

## Acceso a los nodos

Para realizar tareas de depuración, validación o captura de tráfico se recomienda acceder directamente al contenedor mediante `lxc-attach`.

### Ejemplo: acceso al nodo `s1`

```bash
sudo lxc-attach -n s1
```

Una vez dentro del contenedor pueden utilizarse herramientas como:

```bash
tcpdump
bridge
ovs-vsctl
ovs-appctl
iperf3
mpstat
```

---

## Eliminación del escenario

Una vez finalizadas las pruebas, el entorno puede destruirse mediante:

```bash
sudo vnx -f escenario-vlans.xml -v --destroy
```

Este comando detiene los contenedores y libera todos los recursos asociados al escenario.

---

## Autor

Víctor Mendizábal Gimeno

Trabajo Fin de Grado – ETSIT-UPM

Curso académico 2025-2026


```bash
sudo vnx -f escenario-vlans.xml -v --create
