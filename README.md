# With Ansible
## [Vagrant - Ubuntu-bionic 64 - Apache - PHP - Composer - Mariadb - nodejs - yarn - Symfony 4.4 website]

La machine invitée contiendra :

* Ansible latest
* Apache2
* Mariadb 10.4
* PHP 7.2
* Composer latest
* nodejs 12.16
* yarn latest
* adminer latest
* Symfony 4.4 website-skeleton

### 1 - Vérification

* Choisir le provider de gestion de machine virtuelle.
[Liste des providers](https://www.vagrantup.com/docs/providers/)

* Vérifier quelle version du provider est compatible avec la version de votre provider.
[Exemple avec VirtualBox](https://www.vagrantup.com/docs/virtualbox/)

### 2 - Installer votre provider
* [VirtualBox](https://www.virtualbox.org/)
* [VMware](https://www.vmware.com/)
* [Docker](https://www.docker.com/)
* [Hyper-v](https://docs.microsoft.com/fr-fr/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

### 3 - Installer Vagrant
* [Vagrant](https://www.vagrantup.com/)

### 4 - Dossier projet
* Sur votre ordinateur créer le dossier qui va contenir votre projet de développement et la configuration de la machine virtuelle.

<img height="70" alt="creation_sossier" src="https://user-images.githubusercontent.com/26669933/74859827-f36cd200-5347-11ea-8b3d-e378526c2387.png">

### 5 - Initialisation de la machine vagrant
* Dans votre terminal placez-vous dans le dossier nouvellement créé
* lancez la commande  `vagrant init ubuntu/bionic64`
 
 Un fichier Vagrantfile sera créé dans le dossier du projet.
 
 
<img height="72" alt="vagrant_file" src="https://user-images.githubusercontent.com/26669933/74860597-37aca200-5349-11ea-9cde-e2ac4fd90485.png">

### 6 Configuration de la machnine virtuelle
* Ouvrir le fichier Vagrantfile.
* Décommenter la ligne  `config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"`
* Décommenter la ligne  `config.vm.synced_folder "../data", "/vagrant_data"` et modifier la ligne par les valeurs suivantes : `config.vm.synced_folder "./", "/var/www/html"` pour faire un lien symbolique entre le dossier du projet de la machine hôte avec le dossier html de la machine invitée.
* Remplacer le code 
 
   ```
   # config.vm.provider "virtualbox" do |vb|
   #   Display the VirtualBox GUI when booting the machine
   #   vb.gui = true
   #
   #   # Customize the amount of memory on the VM:
   #   vb.memory = "1024"
   # end
   ```
   par (pour allouer 2Go de mémmoire à la machine virtuelle vous pouvez bien sûre adapter la valeur à votre machine hôte)
   
 ```
   config.vm.provider "virtualbox" do |vb|
     #   # Display the VirtualBox GUI when booting the machine
     #   vb.gui = true
     #
     #   # Customize the amount of memory on the VM:
        vb.memory = "2048"
    end   
```

* Remplacer le code 
```
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
```

par (Ce code sera exécuter à l'installation de la machine pour installé toutes les technos nécessaire à votre projet Website Symfony)

```
config.vm.provision "shell", inline: <<-SHELL

     ip="127.0.0.1"
     pwddb="mot_de_passe_root_maraidb"
     dbname="nom_base_de_donnees"
     dbuser="nom_du_user_propre_a_la_base_de_donnees"
     pwduserdb="mot_de_passe_du_user_proore_a_la_base_de_donnees"
     versiondb="mariadb-10.4.12"
    
     apt update
     apt upgrade -y
     apt install -y python-software-properties
     apt update
     apt upgrade -y
     apt-get update
     apt-get install -y software-properties-common
     apt-add-repository --yes --update ppa:ansible/ansible
     apt-get -y install ansible
     echo "${ip}" >> /etc/ansible/host

     git clone https://github.com/Keolite/ansible-lamp-symfony-4.git /home/vagrant/ansible
     ansible-playbook /home/vagrant/ansible/general.yml -i "${ip}," -v -c local -u root --extra-vars "pwddb=${pwddb} dbname=${dbname} dbuser=${dbuser} pwduserdb=${pwduserdb} versiondb=${versiondb}"
    SHELL
```

* Modifier les valeurs ci-dessous par les votres (Ci dessous est un exemple)
```
     pwddb="siaAustralie545"
     dbname="blog"
     dbuser="symfonyadmin"
     pwduserdb="taylorSwift251"
```
### 7 Démarrage de la machine et installation des technos (Provision)
* Démarrer la machine virtuelle avec l'option de provisioner la machine
`vagrant up --provision`

### 8 Utiliser la machine virtuelle
* Pour accéder à la base de données saisir l'url `adminer.localhost:8080`

<img width="364" alt="adminer" src="https://user-images.githubusercontent.com/26669933/74973236-60a16580-5423-11ea-87dc-3863a420155b.png">

* Pour accéder à l'application saisir l'url `localhost:8080`

Pas besoin de .htaccess la configration est réalisée dans le fichier _/etc/apache2/sites-available/000-default.conf_

<img width="796" alt="symfo-4" src="https://user-images.githubusercontent.com/26669933/75065490-4336cf00-54e9-11ea-9b49-cb71682cf811.png">