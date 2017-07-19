# Installer les bases 
<code>sudo apt-get install apache2 mysql-server mysql-client php-mysql phpmyadmin php-mbstring php-gd</code>
générer puis entrer les mots de pass demandés
 
# Configurer Apache 

## Serveur mono-site  

modifier avec nano en ssh  : `sudo nano /etc/apache2/sites-enabled/000-default.conf`


# Laravel 

modifier les lignes pour se retrouver a cela : 

    <VirtualHost *:80>
        
        ServerName 164.132.194.180
        DocumentRoot /var/www/public

        <Directory "/var/www/public">
           AllowOverride all
           Order allow,deny
           Allow from all
        </Directory>
     
    </VirtualHost>

# Serveur quelconque 

    <VirtualHost *:80>
        
        DocumentRoot /your/final/directory
     
    </VirtualHost>

# Configurer mysql 

#####Plusieurs sites 1 base de données  

1. Modifier le fichier : <code> /etc/mysql/mariadb.conf.d/50-server.cnf </code>
 * modifier le ligne bind adress et mettre 0.0.0.0

Cela sert a **Ecouter** sur le port 3306 de mysql 

aller dans phpmyadmin et modifier les règles :
  
* Ajouter un utilisateur 
* Lui attribuer un mot de pass fort !
   * minimum 16 caractères
   * caractères A-Z a-z 0-9 "(-_$*/;. "
* Si le site est distand a la base de données 
   * attribuer a l'utilisateur l'adresse ip du site distant 

# Configuer le par-feu

**Installer iptables**

<code>sudo apt-get install iptables</code>

 **vérifier que iptables a bien été installé**

<code>iptables -L</code>

créer un script , ex:  firewall.sh , le modifier et appliquer des regles !


**Besoins divers**

* Documentation [Iptables](https://doc.ubuntu-fr.org/iptables)
* Documentation [Commandes linux de base](https://doc.ubuntu-fr.org/tutoriel/console_commandes_de_base)
* Documentation [Apache](https://httpd.apache.org/docs/2.4/fr/)


