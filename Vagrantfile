Vagrant.configure(2) do |config|

    # Setting proxy configurations for the host box. This also sets common proxy settings
    # and files for other applications, such as apt-get/yum
    if Vagrant.has_plugin?("vagrant-proxyconf")
       config.proxy.http = "http://plcyber06.draper.com:3128"
       config.proxy.https = "http://plcyber06.draper.com:3128"
       config.proxy.no_proxy = "localhost, 127.0.0.1"
   end

   config.vm.define "elk" do |elk|
    elk.vm.box = "ubuntu/trusty64"

        # Change the default elk vagrant box folder to point to the 
        # elk directory within the project. This will allow separation between
        # elk and developer folders.
        elk.vm.synced_folder "dashboard/", "/vagrant"

        # Network configuration:
        # - Setups a static IP address to allow the client Vagrant box 
        # to know where to connect within the local network of Vagrant 
        # boxes.
        # - Expose the following ports to be used within this box to
        # host data being sent between the web server and the ELK server.
        elk.vm.network "private_network", ip: "172.16.1.100"

        # Provisioner: Runs the provisioning script that will provision
        # the vagrant box for the first time, or forced. 
        elk.vm.provision "shell" do |s| 
            s.path = "dashboard/scripts/install.sh"
            s.privileged = false
        end

        # Provisioner: Run this script always. This allows the box to setup
        # the elk server and web service all the times. This could be done
        # in an initrc file, but this will do.
        elk.vm.provision "shell", path: "dashboard/scripts/restart.sh", run: "always"
        elk.ssh.forward_x11 = true
        # Host configuration: Set specific requirements for the host to
        # provide the Guest Box to use.
        elk.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--cpus", "2", "--memory", "2048"]
        end
    end

    config.vm.define "developer" do |dev|
        # Specify a base virtual machine that is based on Ubuntu Trusty Tahr
        dev.vm.box = "ubuntu/trusty64"

        # Setup a static IP to allow both vagrant boxes to know where 
        # to contact each other. This will allow communication between the
        # web developer and the logging server.
        dev.vm.network "private_network", ip: "172.16.1.10"

        # Specify the provisioning script that will be used in order to 
        # install the necessary files needed for this vagrant box
        dev.vm.provision "shell", inline: "twistd -y /vagrant/twisted_client.py &", 
        run: "always"

        # Change the default client vagrant box folder to point to the 
        # client directory within the project. This will allow separation between
        # client and server folders.
        dev.vm.synced_folder "client/", "/vagrant"
    end
end
