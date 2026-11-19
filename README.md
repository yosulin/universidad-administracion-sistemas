# Laboratorio de Administración de Sistemas

Actividad universitaria para preparar una infraestructura Linux reproducible.
Proyecto académico personal, independiente de cualquier entorno empresarial.

Repositorio público: [yosulin/universidad-administracion-sistemas](https://github.com/yosulin/universidad-administracion-sistemas), rama principal `main`.

## Arquitectura

| Máquina | Sistema | Función prevista | vCPU | RAM | IP privada |
|---|---|---|---:|---:|---|
| srv-universidad | Debian 13 | OpenLDAP y NFS previstos | 2 | 2048 MB | 192.168.200.10 |
| lab01 | Debian 13 | Cliente | 1 | 1024 MB | 192.168.200.21 |
| lab02 | Debian 13 | Cliente | 1 | 1024 MB | 192.168.200.22 |
| lab03 | Kali Linux Rolling | Cliente | 1 | 2048 MB | 192.168.200.23 |

**Estado de fase 2: cuatro VMs desplegadas y comprobadas.**
SSH, sudo y Python 3 funcionan en las cuatro; la comunicación privada se
probó en ambas direcciones entre todos los pares. Kali también superó DNS,
Internet y el ciclo `vagrant halt lab03` / `vagrant up lab03`, conservando IP,
hostname y acceso. Las cuatro se dejan arrancadas. Las comprobaciones de
fase 1 y las incidencias de fase 2 constan en `docs/infraestructura.md`.
Los servicios académicos de las fases siguientes todavía no se han desplegado.

Vagrant define y despliega las VMs, sus imágenes, recursos y redes. Ansible
instalará y configurará los sistemas y servicios en fases posteriores, con
playbooks separados por responsabilidad. GitHub conserva el código para
reconstruir el laboratorio. El nodo controlador Ansible se elegirá en la
siguiente fase; `ansible/` conserva únicamente su marcador de carpeta vacía.

## Requisitos e instalación

- Windows x86-64 con virtualización por hardware habilitada.
- Oracle VirtualBox 7.2 estable y HashiCorp Vagrant 2.4.9.
- Git; GitHub CLI solo es necesario para publicar el proyecto.
- 6 GiB de RAM para las VMs, más memoria libre para Windows y el hipervisor;
  se recomienda disponer de al menos 8 GiB libres antes de arrancar las cuatro.
- Espacio libre suficiente para las imágenes base y cuatro discos de crecimiento
  dinámico; reservar al menos 50 GiB y revisar el consumo posteriormente.

Descargar los instaladores para Windows AMD64 desde
[Oracle](https://www.virtualbox.org/wiki/Downloads) y
[HashiCorp](https://releases.hashicorp.com/vagrant/2.4.9/).
Verificar SHA256 y firma digital antes de instalar con permisos administrativos.
No se necesita Extension Pack ni plugins de Vagrant. No desactivar Hyper-V,
WSL2, VBS ni protecciones de Windows. Si se solicita reiniciar, hacerlo antes
del despliegue y con autorización.

Versiones instaladas y comprobadas en este laboratorio: VirtualBox
**7.2.22r175245** y Vagrant **2.4.9**. El instalador de Vagrant solicitó reinicio
(código 3010); no se reinició Windows y se validó todo el laboratorio en esa
sesión. El reinicio del anfitrión queda pendiente cuando el usuario lo decida.
Abrir una terminal nueva para recoger los cambios de PATH del instalador.

```powershell
VBoxManage --version
vagrant --version
```

## Crear y utilizar el laboratorio

Ejecutar desde el directorio de este repositorio:

```powershell
vagrant validate
vagrant up --provider=virtualbox
vagrant status
```

Tras el primer despliegue, `vagrant up` vuelve a arrancar las máquinas.
Las tres Debian usan `bento/debian-13`, versión fija `202510.26.0`; Kali usa
la imagen oficial `kalilinux/rolling`, versión fija `2026.2.0`. Ambas son
VirtualBox AMD64. Fijar una versión de la imagen conserva un punto de partida
reproducible; Kali continúa utilizando la rama `kali-rolling`.
La descarga inicial requiere Internet. Debian aporta 64 GiB virtuales por VM;
Kali, 86 GB decimales (unos 80 GiB). El consumo físico crece con los datos.
La imagen oficial Kali incluye Xfce y herramientas preinstaladas; se conservan
y VirtualBox se ejecuta sin ventana gráfica. Un ajuste breve de red en el
Vagrantfile evita que NetworkManager interfiera con la IP privada aplicada
por systemd-networkd. Se ejecuta automáticamente al crear Kali y persiste
tras apagarla; no instala paquetes ni configura servicios académicos.

Para ampliar una instalación que ya tenga las tres Debian, desplegar solo Kali:

```powershell
vagrant validate
vagrant up lab03 --provider=virtualbox
vagrant status
```

```powershell
vagrant ssh srv-universidad
vagrant ssh lab01
vagrant ssh lab02
vagrant ssh lab03
```

Se conserva el usuario administrativo de la imagen y el acceso con claves
gestionado por Vagrant. Dentro de cada VM, `sudo -n true` permite comprobar sudo.

```powershell
vagrant halt
vagrant up
```

Para detener y arrancar únicamente Kali: `vagrant halt lab03` y
`vagrant up lab03`. No es necesario recrear las Debian para incorporar Kali.

Cada VM utiliza NAT para Internet y una interfaz privada host-only en
`192.168.200.0/24`. No se usa puente. SSH por NAT queda ligado a `127.0.0.1`;
Vagrant puede ajustar el puerto de cada VM para evitar colisiones. La red
host-only permite acceso desde el anfitrión. No se publican LDAP ni NFS.
La carpeta del repositorio no se comparte automáticamente con las VMs.

## Eliminación controlada

Cuando se decida eliminar exclusivamente este laboratorio, revisar primero
`vagrant status` desde este directorio y ejecutar `vagrant destroy` sin `-f`,
revisando la confirmación de cada máquina. Se perderán sus discos y datos.
Este comando no se ha ejecutado durante la preparación. No eliminar VMs
ajenas al proyecto ni borrar la imagen base si otros proyectos la utilizan.

## Estructura y próximos pasos

- `Vagrantfile`: definición de las cuatro VMs y selección de imagen por máquina.
- `docs/infraestructura.md`: inspección, decisiones y pruebas realmente ejecutadas.
- `data/`: reservado para el futuro CSV de usuarios; los CSV locales no se publican.
- `ansible/`: reservado para futuros playbooks; todavía vacío.

Las próximas fases incorporarán OpenLDAP, NFS y Ansible.
No se configuran todavía PAM, usuarios académicos ni autenticación centralizada.
No subir `.vagrant/`, discos, imágenes, claves privadas ni contraseñas.
