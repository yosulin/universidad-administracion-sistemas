# Infraestructura del laboratorio

Fecha de inspección: 19 de noviembre de 2026 (Europe/Madrid).
Estados: **OK** = ejecutado y comprobado; **ERROR** = ejecutado y fallido;
**PENDIENTE** = todavía no ejecutado.

## Anfitrión inspeccionado

| Elemento | Resultado |
|---|---|
| Windows | Windows 11 Business, 26H2, compilación 26300.9911, x86-64 |
| Procesador | AMD Ryzen 7 250, 8 núcleos, 16 procesadores lógicos |
| RAM visible | Aproximadamente 29,7 GiB; 2,4–2,6 GiB disponibles en la inspección |
| Almacenamiento | Volumen NTFS de unos 475 GiB; unos 116 GiB libres |
| Virtualización firmware | Activada según CIM |
| Hipervisor Windows | Presente; servicios vmms y vmcompute en ejecución |
| WSL2 | Una distribución existente en ejecución, versión 2 |
| VBS | Activo, estado 2; servicios de seguridad 2, 3 y 4 en ejecución |
| Credential Guard | No aparece el identificador 1 entre los servicios configurados/en ejecución |
| VirtualBox | No localizado en PATH, ubicación habitual ni registro de aplicaciones |
| Vagrant | No localizado en PATH, ubicación habitual ni registro de aplicaciones |
| VMware Workstation | No localizado; existe VMware Remote Console 13.0, que no es Workstation |
| Git | 2.51.0.windows.1 |
| GitHub CLI | 2.96.0 |

La consulta detallada de características opcionales de Windows devolvió
«requiere elevación»: **PENDIENTE** de comprobar con privilegios. La presencia
del hipervisor, los servicios y WSL2 sí se verificó sin modificar el equipo.
La propiedad CIM de traducción de direcciones de segundo nivel devolvió false
con el hipervisor activo; no se interpreta aisladamente como falta de soporte.
No se ha desactivado ninguna función de seguridad ni detenido software existente.

CPU y disco permiten plantear el laboratorio; la RAM actualmente disponible
no permite dar por garantizado el arranque simultáneo. Liberar memoria cerrando
aplicaciones que el usuario elija antes de iniciar las tres VMs. No se han
cerrado aplicaciones ni detenido otras cargas automáticamente.

## Software seleccionado

- VirtualBox estable 7.2.22, instalador Windows AMD64 publicado por Oracle.
- Vagrant estable 2.4.9, instalador Windows AMD64 publicado por HashiCorp.
- Compatibilidad 7.2 confirmada en el
  [proveedor oficial](https://developer.hashicorp.com/vagrant/docs/providers/virtualbox)
  y en el [código de Vagrant 2.4.9](https://github.com/hashicorp/vagrant/blob/v2.4.9/plugins/providers/virtualbox/driver/meta.rb).
- No se necesita Extension Pack ni plugins externos.
- Instalación y funcionamiento: **PENDIENTE** de autorización administrativa.

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
| Descarga de la imagen | PENDIENTE; no confundir consulta del catálogo con descarga |

[Metadatos del catálogo](https://vagrantcloud.com/api/v2/vagrant/bento/debian-13).
El proveedor AMD64 existe y su URL respondió a la consulta HTTP. El catálogo
no proporciona checksum para este artefacto; no se ha inventado uno.
La versión queda fijada para reproducibilidad; la actualización de paquetes
deberá comprobarse en el despliegue. Tamaño real del disco, espacio útil y
ausencia de escritorio: **PENDIENTE** de verificar tras importar la imagen.
Se conserva inicialmente el disco de la imagen; si resultase insuficiente
para doce directorios de usuarios, se ampliará antes de concluir la fase.

## Máquinas y red

| VM | Nombre en VirtualBox | vCPU | RAM | IPv4 privada |
|---|---|---:|---:|---|
| srv-universidad | universidad-srv-universidad | 2 | 2048 MB | 192.168.200.10/24 |
| lab01 | universidad-lab01 | 1 | 1024 MB | 192.168.200.21/24 |
| lab02 | universidad-lab02 | 1 | 1024 MB | 192.168.200.22/24 |

Interfaz 1 NAT; interfaz 2 host-only, gestionada por Vagrant. SSH NAT solo
en bucle local. No se ha configurado puente ni reenvío LDAP/NFS.
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
| Exclusiones de Git | OK | git check-ignore excluye estado Vagrant, claves, discos, ISO, secretos y logs; permite documentación y playbooks |
| vagrant validate | PENDIENTE | Vagrant no instalado |
| VBoxManage --version | PENDIENTE | VirtualBox no instalado |
| vagrant --version | PENDIENTE | Vagrant no instalado |
| Tres VMs nuevas, importación y arranque | PENDIENTE | No se han creado VMs |
| Debian 13 y hostnamectl | PENDIENTE | Requiere despliegue |
| RAM, CPU y disco | PENDIENTE | Requiere free -h, nproc y df -h dentro de cada VM |
| SSH, sudo y Python 3 | PENDIENTE | Requiere vagrant ssh en las tres VMs |
| DNS, Internet y repositorios Debian | PENDIENTE | Requiere resolución DNS y sudo apt-get update |
| Ping servidor/clientes y entre clientes | PENDIENTE | Requiere despliegue |
| Persistencia tras halt/up | PENDIENTE | Requiere ciclo completo y repetir comprobaciones |
| Publicación en GitHub | PENDIENTE | Se realizará después de validar las VMs y revisar secretos |

La API de correos privados de GitHub no está autorizada con los permisos
actuales del token. No se han ampliado permisos. El nombre Yosu se ha
corroborado en commits asociados por GitHub a yosulin. Se utiliza el formato
privado `ID+LOGIN@users.noreply.github.com` documentado por
[GitHub](https://docs.github.com/en/account-and-profile/reference/email-addresses-reference),
con ID y login obtenidos de la cuenta autenticada. La asociación del nuevo
commit en GitHub queda **PENDIENTE** hasta la publicación.

## Pruebas que se ejecutarán después de la instalación

En las tres VMs: `cat /etc/os-release`, `hostnamectl`, `free -h`, `nproc`,
`df -h /`, `ip -4 address`, `sudo -n true`, `python3 --version`,
`getent hosts deb.debian.org` y `sudo apt-get update`.
Verificar comunicación ICMP en ambas direcciones entre las tres IP privadas
y salida a Internet. Ejecutar `vagrant halt` y después `vagrant up`;
repetir IP, hostname, SSH y comunicación entre máquinas. Contrastar las tres
VMs del proyecto con `VBoxManage list vms` y `vagrant status`, sin modificar otras.

## Incidencias abiertas

1. Falta VirtualBox y Vagrant; instalación requiere autorización administrativa.
2. Memoria libre inferior a los 4 GiB asignados: liberar memoria antes del arranque conjunto.
3. Compatibilidad de esta compilación de Windows con VirtualBox, coexistencia
   con el hipervisor Windows y funcionamiento real: pendientes de prueba.

La fase 1 **no está terminada** hasta superar las comprobaciones y publicar.
