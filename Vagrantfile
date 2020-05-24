# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Ubuntu 18.04
  config.vm.define "certs" do |certs|
    certs.vm.box = "peru/ubuntu-18.04-server-amd64"
    certs.vm.hostname = "dev-ansible"
 
   #Network Settings
   certs.vm.network "private_network", ip: "10.22.3.10"
   certs.vm.network :forwarded_port, guest: 22, host: 2221, id: "ssh", auto_correct: true
   certs.vm.network :forwarded_port, guest: 80, host: 8080, id: "http", auto_correct: true
   certs.vm.network :forwarded_port, guest: 443, host: 8443, id: "https", auto_correct: true

   # Provider Settings
   ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'
   certs.vm.provider "virtualbox" do |vb|
      vb.name = "Master_Control"
      vb.memory = 4096
      vb.cpus = 1
     end

  # Synced Folder Settings
   certs.vm.synced_folder ".", "/vagrant", disabled: true

   # Provisioning settings
     certs.vm.provision "shell", inline: <<-SHELL
      apt-get update && apt-get upgrade -y
      apt-get install -y apache2
      apt-get install -y tasksel
      apt-get install ubuntu-desktop -y
     SHELL
  end


 # DC01
 config.vm.define "dc01" do |dc01|

  #Box Settings
  dc01.vm.box = "gusztavvargadr/windows-server"
  dc01.vm.guest = :windows
  dc01.vm.communicator = :winrm
  dc01.vm.hostname = "dev-win01"

  #Network Settings
  dc01.vm.network "private_network", ip: "10.22.3.14"
  dc01.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", auto_correct: true
  dc01.vm.network :forwarded_port, guest: 80, host: 8888, id: "http", auto_correct: true
  dc01.vm.network :forwarded_port, guest: 443, host: 4438, id: "https", auto_correct: true
  dc01.vm.network :forwarded_port, guest: 3389, host: 33894, id: "rdp", auto_correct: true
  dc01.vm.network :forwarded_port, guest: 5985, host: 59854, id: "winrm", auto_correct: true
  dc01.vm.network :forwarded_port, guest: 5986, host: 59864, id: "winrms", auto_correct: true

  #Provider Settings
  dc01.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.name = "WinSRV2019"
    vb.memory = 4096
    vb.cpus = 2
    vb.customize ["storageattach", :id, "--storagectl", "IDE Controller",  "--port", "0", "--device", "1", "--type", "dvddrive", "--medium", "emptydrive"]
    vb.customize ["modifyvm", :id, "--audio", "none"]
  end
  
  # Share an additional folder to the guest VM. 
  dc01.vm.synced_folder "C:/Anwar", "/Project_Tentixo"

  #provision Settings
  dc01.vm.provision "shell", privileged: "true", inline: <<-SHELL
   Set-TimeZone 'W. Europe Standard Time'
   Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://github.com/ansible/ansible/blob/devel/examples/scripts/ConfigureRemotingForAnsible.ps1'))
   Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((new-object net.webclient).DownloadString('https://github.com/rgl/choco-0.9.10.3-not-working-with-vagrant/blob/master/chocolatey-0.10.0.ps1'))
   rm $PROFILE
  SHELL
 
  dc01.winrm.guest_port = "5986"
  dc01.winrm.port = "59864"
  dc01.winrm.transport = :ssl
  dc01.winrm.retry_limit = 30
  dc01.winrm.retry_delay = 10
  dc01.winrm.username = "vagrant"
  dc01.winrm.password = "vagrant"
  dc01.winrm.ssl_peer_verification = false

  end

  # DC02
  config.vm.define "dc02" do |dc02|
    dc02.vm.box = "jborean93/WindowsServer2016"
    dc02.vm.guest = :windows
    dc02.vm.hostname = "dev-dc02"
    dc02.vm.network :private_network, ip: "10.22.3.15"
    dc02.vm.network :forwarded_port, guest: 22, host: 2225, id: "ssh", auto_correct: true
    dc02.vm.network :forwarded_port, guest: 3389, host: 33895, id: "rdp", auto_correct: true
    dc02.vm.network :forwarded_port, guest: 5985, host: 59855, id: "winrm", auto_correct: true
    dc02.vm.network :forwarded_port, guest: 5986, host: 59865, id: "winrms", auto_correct: true
    config.vm.provider :virtualbox do |v|
      v.name = "WinSRV2016"
      v.gui = true
      v.memory = 2048
      v.cpus = 1
      v.customize ["storageattach", :id, "--storagectl", "IDE Controller",  "--port", "0", "--device", "1", "--type", "dvddrive", "--medium", "emptydrive"]
      v.customize ["modifyvm", :id, "--audio", "none"]
    end

    # Share an additional folder to the guest VM. 
    dc02.vm.synced_folder "C:/Anwar", "/Project_Tentixo"

    dc02.vm.provision "shell", inline: <<-SHELL
      Set-TimeZone 'W. Europe Standard Time'
      Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1'))
      Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((new-object net.webclient).DownloadString('https://github.com/rgl/choco-0.9.10.3-not-working-with-vagrant/blob/master/chocolatey-0.10.0.ps1'))
      #rm $PROFILE
    SHELL

    dc02.winrm.guest_port = "5986"
    dc02.winrm.port = "59865"
    dc02.winrm.transport = :ssl
    dc02.winrm.username = "vagrant"
    dc02.winrm.password = "vagrant"
    dc02.winrm.ssl_peer_verification = false
  end
end
