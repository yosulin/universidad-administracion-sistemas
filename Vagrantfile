# Laboratorio universitario: fase 1 (solo Debian).
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
    { name: "lab02", ip: "192.168.200.22", cpus: 1, memory: 1024 }
  ]

  machines.each do |machine|
    config.vm.define machine[:name] do |vm|
      vm.vm.hostname = machine[:name]
      vm.vm.network "private_network", ip: machine[:ip], netmask: "255.255.255.0"

      vm.vm.provider "virtualbox" do |vb|
        vb.name = "universidad-#{machine[:name]}"
        vb.gui = false
        vb.cpus = machine[:cpus]
        vb.memory = machine[:memory]
      end
    end
  end
end
