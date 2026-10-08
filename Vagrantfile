Vagrant.configure("2") do |config|

  config.vm.box = "debian/bookworm64"

  config.vm.define "server" do |srv|
    srv.vm.hostname = "server"

    srv.vm.network "public_network",
      bridge: "Realtek Gaming 2.5GbE Family Controller #3"

    srv.vm.network "private_network",
      ip: "192.168.57.10",
      virtualbox__intnet: "intnet"
  end


  config.vm.define "c1" do |c1|
    c1.vm.hostname = "c1"

    c1.vm.network "private_network",
      type: "dhcp",
      virtualbox__intnet: "intnet"
  end


  config.vm.define "printer" do |printer|
    printer.vm.hostname = "printer"

    printer.vm.network "private_network",
      mac: "08002781ed05",
      type: "dhcp",
      virtualbox__intnet: "intnet"
  end

end