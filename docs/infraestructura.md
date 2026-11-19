# Infraestructura del laboratorio

Fecha de inspección: 19 de noviembre de 2026 (Europe/Madrid).
Estados: **OK** = ejecutado y comprobado; **ERROR** = ejecutado y fallido;
**PENDIENTE** = todavía no ejecutado.

## Anfitrión inspeccionado

| Elemento | Resultado |
|---|---|
| Windows | Windows 11 Business, 26H2, compilación 26300.9911, x86-64 |
| Procesador | AMD Ryzen 7 250, 8 núcleos, 16 procesadores lógicos |
| RAM visible | Aproximadamente 29,7 GiB; inicialmente 2,4–2,6 GiB libres; unos 6,5 GiB libres con las tres VMs arrancadas |
| Almacenamiento | Volumen NTFS de unos 475 GiB; unos 116 GiB libres |
| Virtualización firmware | Activada según CIM |
| Hipervisor Windows | Presente; Hyper-V instalado; servicios vmms y vmcompute en ejecución |
| WSL2 | Una distribución existente en ejecución, versión 2 |
| VBS | Activo, estado 2; servicios de seguridad 2, 3 y 4 en ejecución |
| Credential Guard | No aparece el identificador 1 entre los servicios configurados/en ejecución |
| VirtualBox | Instalado durante esta fase; VBoxManage 7.2.22r175245 |
| Vagrant | Instalado durante esta fase; Vagrant 2.4.9 |
| VMware Workstation | No localizado; existe VMware Remote Console 13.0, que no es Workstation |
| Git | 2.51.0.windows.1 |
| GitHub CLI | 2.96.0 |

La consulta Get-WindowsOptionalFeature devolvió «requiere elevación».
Se resolvió mediante Get-CimInstance Win32_OptionalFeature, sin elevación:
Hyper-V, VirtualMachinePlatform y Windows Subsystem for Linux instalados
(InstallState 1); HypervisorPlatform no instalado (InstallState 2).
La propiedad CIM de traducción de direcciones de segundo nivel devolvió false
con el hipervisor activo; no se interpreta aisladamente como falta de soporte.
No se ha desactivado ninguna función de seguridad ni detenido software existente.

La memoria libre inicial era insuficiente. Tras avisar al usuario, se observó
un aumento hasta unos 7,5 GiB antes del despliegue. Las tres VMs pudieron
arrancar simultáneamente y quedaron unos 6,5 GiB disponibles en otra medición.
No se han cerrado aplicaciones ni detenido otras cargas automáticamente.

## Software seleccionado

- VirtualBox estable 7.2.22, instalador Windows AMD64 publicado por Oracle.
- Vagrant estable 2.4.9, instalador Windows AMD64 publicado por HashiCorp.
- Compatibilidad 7.2 confirmada en el
  [proveedor oficial](https://developer.hashicorp.com/vagrant/docs/providers/virtualbox)
  y en el [código de Vagrant 2.4.9](https://github.com/hashicorp/vagrant/blob/v2.4.9/plugins/providers/virtualbox/driver/meta.rb).
- No se necesita Extension Pack ni plugins externos.
- Instalación y funcionamiento: **OK**, autorizados expresamente por el usuario.
- VirtualBox: instalador finalizado con código 0. Componentes instalados:
  aplicación y adaptador host-only; sin controlador de puente, USB ni Extension Pack.
- Vagrant: instalador finalizado con código 3010 (reinicio solicitado).
  No se reinició Windows. La herramienta y las VMs funcionan en esta sesión;
  el reinicio solicitado por el instalador queda pendiente para cuando el
  usuario decida realizarlo. No se ha probado un reinicio del anfitrión.
- VBoxManage no mostraba VMs antes del despliegue; después registra exactamente
  las tres del proyecto. No se han modificado ni eliminado VMs preexistentes.
- El registro VBox.log confirma ejecución mediante NEM/Hyper-V. WSL2 sigue
  en ejecución y VBS permanece activo después de arrancar las tres VMs.

Instaladores descargados y verificados el 19 de noviembre de 2026 en una
carpeta local de descargas, fuera del repositorio. **OK**: SHA256 coincide con
las sumas oficiales y Authenticode devuelve firma válida en ambos archivos.

| Instalador | Firmante | SHA256 |
|---|---|---|
| VirtualBox-7.2.22-175245-Win.exe | Oracle America, Inc. | afd0ff1656cbe04ed88ce619594ae021419d97cce89fa9bdc443e9a963f1713f |
| vagrant_2.4.9_windows_amd64.msi | HashiCorp, Inc. | 3bdd967927705872a70c7c98e0576afd5acd9dd73b527695f4d9dd75dd26cbe3 |

Fuentes: [Oracle 7.2.22](https://download.virtualbox.org/virtualbox/7.2.22/)
y [HashiCorp 2.4.9](https://releases.hashicorp.com/vagrant/2.4.9/).

## Imagen base

| Propiedad | Selección verificada en el catálogo |
|---|---|
| Identificador | bento/debian-13 |
| Versión | 202510.26.0 (activa) |
| Sistema anunciado | Debian 13.1, sin escritorio |
| Procedencia | Bento, Progress Chef |
| Proveedor | virtualbox |
| Arquitectura | amd64, fijada explícitamente |
| Construcción anunciada | VirtualBox 7.2.4; probada por el editor con Vagrant 2.4.9 |
| Fecha de consulta | 19 de noviembre de 2026 |
| Descarga de la imagen | OK: 19 de noviembre de 2026, archivo completo de 1.127.410.458 bytes |

[Metadatos del catálogo](https://vagrantcloud.com/api/v2/vagrant/bento/debian-13).
El proveedor AMD64 existe y se descargó el archivo completo por HTTPS.
SHA256 local: `495517cc17a77b1fe35d4f62dcd81fb86514648d5a867f7e989491c6bb648d48`.
El catálogo no proporciona checksum para este artefacto: el hash local sirve
para identificar esta descarga, no como cotejo independiente con el editor.
El archivo se pudo listar con tar; contiene Vagrantfile, box.ovf, un VMDK y
metadata.json. Este último confirma proveedor virtualbox y arquitectura amd64.
La versión queda fijada para reproducibilidad; el acceso a los repositorios
se comprobó con apt-get update en las tres máquinas. El OVF declara un disco
de 64 GiB de capacidad virtual, suficiente inicialmente para el laboratorio.
En cada VM, df -h / muestra una raíz LVM de 59 GiB y unos 55 GiB libres.
No existe servicio display-manager configurado; no se instalaron escritorios.
Se conserva el disco de la imagen, sin plugins ni discos adicionales.
El OVF usa la etiqueta de VirtualBox Debian12_64; no acredita la versión del
sistema instalado. /etc/os-release confirmó Debian 13 (trixie), base 13.1,
en las tres máquinas; kernel 6.12.48+deb13-amd64 y Python 3.13.5.
Los metadatos actuales del repositorio anuncian Debian 13.7: se actualizaron
los índices de APT para comprobar acceso, sin ejecutar una actualización
completa de paquetes, que no formaba parte de las pruebas solicitadas.

## Máquinas y red

| VM | Nombre en VirtualBox | vCPU | RAM | IPv4 privada |
|---|---|---:|---:|---|
| srv-universidad | universidad-srv-universidad | 2 | 2048 MB | 192.168.200.10/24 |
| lab01 | universidad-lab01 | 1 | 1024 MB | 192.168.200.21/24 |
| lab02 | universidad-lab02 | 1 | 1024 MB | 192.168.200.22/24 |

Interfaz 1 NAT; interfaz 2 host-only, gestionada por Vagrant. SSH NAT solo
en bucle local. No se ha configurado puente ni reenvío LDAP/NFS.
Las tres comparten el adaptador host-only del laboratorio, cuya dirección en
el anfitrión es 192.168.200.1/24. Puertos SSH observados en bucle local:
servidor 2222, lab01 2200 y lab02 2201; Vagrant los ajusta automáticamente.
La IP NAT 10.0.2.15 se repite porque cada VM tiene su NAT independiente;
la comunicación entre máquinas utiliza las IP host-only.
Las rutas e interfaces IPv4 existentes se inspeccionaron y no se encontró
solapamiento con `192.168.200.0/24`. Hay interfaces físicas, VPN y redes
virtuales de Windows existentes; ninguna se ha modificado. Volver a comprobar
las rutas al desplegar si cambia la conexión de red o VPN.

## Registro de comprobaciones

| Comprobación | Estado | Evidencia |
|---|---|---|
| Identidad GitHub | OK | gh auth status y gh api user: yosulin, ID 193429901 |
| Repositorio remoto previo | OK | gh repo view: el repositorio solicitado no existe |
| Identidad Git local | OK | Yosu y noreply derivado del ID/login verificados; configuración exclusiva de este repositorio |
| Red propuesta sin solapamiento actual | OK | Get-NetIPAddress y Get-NetRoute |
| Descarga y autenticidad de instaladores | OK | SHA256 oficial y firmas Oracle/HashiCorp válidas |
| Descarga e inspección del archivo box | OK | 1.127.410.458 bytes; tar legible; metadata AMD64/VirtualBox y disco OVF de 64 GiB |
| Exclusiones de Git | OK | git check-ignore excluye estado Vagrant, claves, discos, ISO, secretos y logs; permite documentación y playbooks |
| vagrant validate | OK | Vagrantfile validated successfully |
| VBoxManage --version | OK | 7.2.22r175245 |
| vagrant --version | OK | Vagrant 2.4.9; sin plugins instalados |
| Tres VMs nuevas, importación y arranque | OK | VBoxManage list vms/list runningvms y vagrant status muestran las tres |
| Debian 13 y hostnamectl | OK | Debian 13 trixie; srv-universidad, lab01 y lab02 |
| RAM, CPU y disco | OK | Servidor 1,9 GiB visibles y 2 CPU; clientes 967 MiB y 1 CPU; raíz de 59 GiB |
| SSH, sudo y Python 3 | OK | vagrant ssh ejecutado en las tres; usuario vagrant, sudo -n id -u = 0; Python 3.13.5 |
| DNS, Internet y repositorios Debian | OK | getent ahostsv4 deb.debian.org, ping 1.1.1.1 y apt-get update correctos en las tres |
| Ping servidor/clientes y entre clientes | OK | Seis direcciones entre pares probadas; dos paquetes por destino, 0% de pérdida |
| Persistencia tras halt/up | OK | Las tres apagadas y arrancadas de nuevo; hostname, IP, SSH, sudo, Python, DNS, Internet y seis direcciones ICMP entre pares correctos |
| Publicación en GitHub | PENDIENTE | Se realizará después de validar las VMs y revisar secretos |

La API de correos privados de GitHub no está autorizada con los permisos
actuales del token. No se han ampliado permisos. El nombre Yosu se ha
corroborado en commits asociados por GitHub a yosulin. Se utiliza el formato
privado `ID+LOGIN@users.noreply.github.com` documentado por
[GitHub](https://docs.github.com/en/account-and-profile/reference/email-addresses-reference),
con ID y login obtenidos de la cuenta autenticada. La asociación del nuevo
commit en GitHub queda **PENDIENTE** hasta la publicación.

## Comandos de comprobación ejecutados

En las tres VMs: `cat /etc/os-release`, `hostnamectl`, `free -h`, `nproc`,
`df -h /`, `ip -4 -brief address`, `sudo -n id -u`, `python3 --version`,
`getent ahostsv4 deb.debian.org` y `sudo apt-get update`.
Se probó comunicación ICMP en ambas direcciones entre las tres IP privadas
y salida a Internet con `ping -c 2 -W 3`. Se contrastaron las tres VMs con
`VBoxManage list vms`, `VBoxManage list runningvms`, `VBoxManage showvminfo`
y `vagrant status`. `vagrant halt` finalizó con código 0 y las tres VMs se
comprobaron en estado poweroff, sin ninguna VM en ejecución.
El segundo `vagrant up` finalizó con código 0. Se repitieron las conexiones
`vagrant ssh` en las tres VMs: hostname e IP privada sin cambios, sudo
operativo, Python 3.13.5, resolución de deb.debian.org y ping a 1.1.1.1
correctos. Se repitieron las seis direcciones ICMP entre pares, todas con
0% de pérdida. El boot_id cambió en las tres, confirmando el nuevo arranque.
Las tres máquinas se dejan en ejecución al terminar las pruebas.

## Incidencias y soluciones

1. Faltaban VirtualBox y Vagrant: instalados con autorización y firmas verificadas.
2. RAM inicialmente insuficiente: se avisó al usuario y se comprobó el aumento
   de memoria disponible antes de arrancar las VMs.
3. Consulta de características Windows requería elevación: se sustituyó por
   consulta CIM de solo lectura, que sí funcionó sin modificar el sistema.
4. Instalador Vagrant solicita reinicio (3010): no se reinició; funcionamiento
   verificado en esta sesión. Reinicio del anfitrión pendiente de decisión del usuario.
5. Colisión del puerto SSH predeterminado: Vagrant asignó automáticamente
   2200 y 2201 a los clientes, manteniendo todos los puertos en 127.0.0.1.
6. hostnamectl sin sudo en el servidor emitió avisos al consultar UUID/serie
   del hardware; mostró correctamente el hostname y el SO. En los clientes
   se ejecutó con sudo, sin esos avisos. No afecta al acceso administrativo.

Las comprobaciones funcionales de la fase 1 están superadas. La publicación
en GitHub se verificará después de actualizar y revisar esta documentación.
