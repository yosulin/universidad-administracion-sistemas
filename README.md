# Laboratorio de Administración de Sistemas

Actividad universitaria para preparar una infraestructura Linux reproducible.
Proyecto académico personal, independiente de cualquier entorno empresarial.

## Arquitectura

| Máquina | Sistema | Función prevista | vCPU | RAM | IP privada |
|---|---|---|---:|---:|---|
| srv-universidad | Debian 13 | OpenLDAP, NFS y controlador Ansible | 2 | 2048 MB | 192.168.200.10 |
| lab01 | Debian 13 | Cliente | 1 | 1024 MB | 192.168.200.21 |
| lab02 | Debian 13 | Cliente | 1 | 1024 MB | 192.168.200.22 |
| lab03 | Kali Linux Rolling | Cliente futuro | Por definir | Por definir | Por definir |

**Estado de fase 1: tres Debian 13 desplegadas y comprobadas.**
Verificados SSH, sudo, Python 3, DNS, Internet, repositorios Debian y
comunicación privada entre todas las máquinas. El ciclo `vagrant halt` /
`vagrant up` conserva las IP y los hostnames. Las tres se dejan arrancadas.
El Vagrantfile define únicamente las tres Debian. Kali y los servicios de las
fases siguientes todavía no se han desplegado.

## Requisitos e instalación

- Windows x86-64 con virtualización por hardware habilitada.
- Oracle VirtualBox 7.2 estable y HashiCorp Vagrant 2.4.9.
- Git; GitHub CLI solo es necesario para publicar el proyecto.
- 4 GiB de RAM para las VMs, más memoria libre para Windows y el hipervisor;
  se recomienda disponer de al menos 6 GiB libres antes de arrancar las tres.
- Espacio libre suficiente para la imagen base y tres discos de crecimiento
  dinámico; reservar al menos 30 GiB y revisar el consumo posteriormente.

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
La imagen es `bento/debian-13`, versión fija `202510.26.0`, proveedor
VirtualBox, arquitectura AMD64. La descarga inicial requiere Internet.
La imagen aporta un disco virtual de 64 GiB por VM; se verificaron unos
55 GiB libres en la raíz de cada máquina.

```powershell
vagrant ssh srv-universidad
vagrant ssh lab01
vagrant ssh lab02
```

Se conserva el usuario administrativo de la imagen y el acceso con claves
gestionado por Vagrant. Dentro de cada VM, `sudo -n true` permite comprobar sudo.

```powershell
vagrant halt
vagrant up
```

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

- `Vagrantfile`: definición de las tres Debian.
- `docs/infraestructura.md`: inspección, decisiones y pruebas realmente ejecutadas.
- `data/`: reservado para el futuro CSV de usuarios; los CSV locales no se publican.
- `ansible/`: reservado para futuros playbooks; todavía vacío.

Las próximas fases incorporarán Kali Rolling, OpenLDAP, NFS y Ansible.
No se configuran PAM, usuarios académicos ni autenticación centralizada en fase 1.
No subir `.vagrant/`, discos, imágenes, claves privadas ni contraseñas.
