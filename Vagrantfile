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

end