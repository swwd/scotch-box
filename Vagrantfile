# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.vm.box = "scotch/box"
    config.vm.network "private_network", ip: "192.168.33.10"
    config.vm.hostname = "scotchbox"
    config.vm.synced_folder "www", "/var/www", :mount_options => ["dmode=777", "fmode=666"]
    
    # Optional NFS. Make sure to remove other synced_folder line too
    #config.vm.synced_folder ".", "/var/www", :nfs => { :mount_options => ["dmode=777","fmode=666"] }

    config.vm.provision "shell", inline: <<-SHELL

        # Disable scotchbox.local
        echo "Disabling scotchbox.local..."
        a2dissite scotchbox.local.conf

        # Enable dynamic *.box virtual hosts
        echo "Enabling Dynamic VirtualHost support..."
        a2enmod vhost_alias

        echo "Creating VirtualHost config for *.box..."
        sudo cp /etc/apache2/sites-available/scotchbox.local.conf /etc/apache2/sites-available/box.conf

        echo "Updating config for *.box..."
        # ServerName: scotchbox.local --> box
        sudo sed -i 's,scotchbox.local,box,g' /etc/apache2/sites-available/box.conf
        # ServerAlias: www.box --> *.box
        sudo sed -i 's,www.box,*.box,g' /etc/apache2/sites-available/box.conf
        ## DocumentRoot --> VirtualDocumentRoot
        sudo sed -i 's,DocumentRoot,VirtualDocumentRoot,g' /etc/apache2/sites-available/box.conf
        # DocumentRoot: /var/www/public --> /var/www/%-2+/public
        sudo sed -i 's,/var/www/public,/var/www/%-2+/public,g' /etc/apache2/sites-available/box.conf

        echo "Enabling .box config. This will require a restart of Apache"
        a2ensite box.conf

        echo "Restarting Apache..."
        service apache2 reload

        echo ".box Dynamic Virtual Host enabled."

    SHELL

end
