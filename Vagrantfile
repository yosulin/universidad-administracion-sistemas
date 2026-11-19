# Laboratorio universitario: tres Debian 13 y un cliente Kali Rolling.
Vagrant.configure("2") do |config|
  config.vm.box = "bento/debian-13"
  config.vm.box_version = "202510.26.0"
  config.vm.box_architecture = "amd64"
  config.vm.box_check_update = false

  # No se necesita compartir el directorio del anfitrion en esta fase.
  config.vm.synced_folder ".", "/vagrant", disabled: true
  config.vm.boot_timeout = 600
  config.ssh.forward_agent = false
  # Vagrant conserva NAT y publica SSH unicamente en el bucle local.
  config.vm.network "forwarded_port", guest: 22, host: 2222,
                    host_ip: "127.0.0.1", id: "ssh", auto_correct: true

  machines = [
    { name: "srv-universidad", ip: "192.168.200.10", cpus: 2, memory: 2048 },
    { name: "lab01", ip: "192.168.200.21", cpus: 1, memory: 1024 },
    { name: "lab02", ip: "192.168.200.22", cpus: 1, memory: 1024 },
    { name: "lab03", ip: "192.168.200.23", cpus: 1, memory: 2048,
      box: "kalilinux/rolling", box_version: "2026.2.0" }
  ]

  machines.each do |machine|
    config.vm.define machine[:name] do |vm|
      if machine[:box]
        vm.vm.box = machine[:box]
        vm.vm.box_version = machine[:box_version]
      end
      vm.vm.hostname = machine[:name]
      vm.vm.network "private_network", ip: machine[:ip], netmask: "255.255.255.0"

      if machine[:name] == "lab03"
        # La imagen Kali activa dos gestores: networkd aplica la IP de Vagrant.
        # NetworkManager debe dejar de intentar DHCP en la interfaz privada.
        vm.vm.provision "shell", inline: <<~SHELL
          set -eu
          mkdir -p /etc/NetworkManager/conf.d
          printf '%s\\n' '[keyfile]' 'unmanaged-devices=interface-name:eth1' > /etc/NetworkManager/conf.d/90-vagrant-private.conf
          nmcli general reload
          nmcli device set eth1 managed no
          networkctl reconfigure eth1
        SHELL
      end

      vm.vm.provider "virtualbox" do |vb|
        vb.name = "universidad-#{machine[:name]}"
        vb.gui = false
        vb.cpus = machine[:cpus]
        vb.memory = machine[:memory]
      end
    end
  end
end
