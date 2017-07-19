Cette page remplace l'ancienne procédure du wiki hébergement : [[heberg:Création d'une archive des données client et leur suppression éventuelle]]

h1. Archivage données v12 

Se connecter sur le serveur v12 concerné.

Initialiser les variables avec les bonnes infos :

> set -u
> PBDD=tesst
> IDSKY=moniid
> NDD=monndd.com
> HOST=calypso.prowebce.net
> read -s -p "Mdp root MySQL> " MDP

Tester la déclaration des variables (doit afficher OK) :
<pre>
if [ -n "$IDSKY" ];then echo "ok pour IDSKY : $IDSKY";else echo "IDSKY non renseigné "; fi; if [ -n "$NDD" ];then echo "ok pour NDD : $NDD";else echo "NDD non renseigné "; fi; if [ -n "$HOST" ];then echo "ok pour HOST : $HOST";else echo "HOST non renseigné "; fi; if [ -n "$PBDD" ];then echo "ok pour PBDD : $PBDD";else echo "PBDD non renseigné "; fi; if [ -n "$MDP" ];then echo "ok pour MDP";else echo "MDP non renseigné "; fi;
</pre>

Se placer dans le répertoire du client :
<pre>
cd /home/adminweb/domaines/$NDD/
</pre>

Supprimer le cache serveur :
<pre>
rm -Rvf com/upload/_compress/_*/* com/_cache/* com/upload/_cache/* com/piwik gestion/upload/_ressources/* gestion/cache/*
[ -d com_test ] && rm -Rvf com_test/upload/_compress/_*/* com_test/_cache/* com_test/upload/_cache/* 
[ -d gestion_test ] && rm -Rvf gestion_test/upload/_ressources/* gestion_test/cache/prod/assetic/assets/*
[ -d gestion_archive ] && rm -Rvf gestion_archive/upload/_ressources/* gestion_archive/cache/prod/assetic/assets/*
[ -d gestion_ecole ] && rm -Rvf gestion_ecole/upload/_ressources/* gestion_ecole/cache/prod/assetic/assets/*
</pre>

Supprimer les restes de la moulinette v8 -> v12 si il y en a :
<pre>
[ -d com/upload/moulinette ] && rm -Rvf com/upload/moulinette/
[ -d gestion/upload/moulinette ] && rm -Rvf gestion/upload/moulinette/
[ -d com_test/upload/moulinette ] && rm -Rvf com_test/upload/moulinette/
[ -d gestion_test/upload/moulinette ] && rm -Rvf gestion_test/upload/moulinette
[ -d gestion_archive/upload/moulinette ] && rm -Rvf gestion_archive/upload/moulinette
[ -d gestion_ecole/upload/moulinette ] && rm -Rvf gestion_ecole/upload/moulinette
</pre>

Enregistrer les versions de la com et de la gestion dans un fichier versions.txt :
<pre>
fich=versions.txt
touch $fich
[ -h com_v12 ] && echo "com_v12 -> " $(readlink -f com_v12) >> $fich
[ -h com_v12_test ] && echo "com_v12_test -> " $(readlink -f com_v12_test) >> $fich
[ -h pwb_v12 ] && echo "pwb_v12 -> " $(readlink -f pwb_v12) >> $fich
[ -h pwb_v12_test ] && echo "pwb_v12_test -> " $(readlink -f pwb_v12_test) >> $fich
[ -h pwb_v12_archive ] && echo "pwb_v12_archive -> " $(readlink -f pwb_v12_archive) >> $fich
[ -h pwb_v12_ecole ] && echo "pwb_v12_ecole -> " $(readlink -f pwb_v12_ecole) >> $fich
</pre>

Créer un répertoire apache et BDD :
<pre>
mkdir -p apache/logs
mkdir apache/conf
mkdir bdd
</pre>

Copier la conf apache 
<pre>
[ -f /etc/httpd/sites-available/$NDD.conf ] && cp /etc/httpd/sites-available/$NDD.conf /home/adminweb/domaines/$NDD/apache/conf/
[ -f /etc/httpd/sites-available/${NDD}_ssl.conf ] && cp /etc/httpd/sites-available/${NDD}_ssl.conf /home/adminweb/domaines/$NDD/apache/conf/
</pre>

Copier les logs apache :
<pre>
cp /var/log/httpd/${NDD}*_log /home/adminweb/domaines/$NDD/apache/logs/
cp /var/log/httpd/${NDD}*.gz /home/adminweb/domaines/$NDD/apache/logs/
</pre>

Extraire la BDD dans le répertoire bdd/ :
<pre>
BASES=$(mysql -u root -p$MDP -Bse "show databases like '${PBDD}_%';");for db in $BASES ;do mysqldump -u root -p$MDP $db > bdd/${db}_$(date +"%Y%m%d").sql;done
</pre>

Extraire les noms d'utilisateurs dans un fichier (la commande peut créer des doublons, si c'est le cas, supprimer les lignes en double dans le fichier / proposer une amélioration de la commande) :
<pre>
USERS=$(mysql -u root -p$MDP -Bse "select user from mysql.user where user like '${PBDD}_%';");for user in $USERS;do   echo $USERS >> bdd/users_bdd.txt;done
</pre>

Vérification, si "Dump completed" s'affiche pour tous les .sql, c'est OK : 
>tail -n1 bdd/*.sql



Archiver les données dans backups_a_conserver :
<pre>
cd /home/adminweb/backups_a_conserver/
tar -czvf $(date +"%Y-%m-%d_%H-%M")_id-${IDSKY}_${NDD}_${HOST}.tar.gz /home/adminweb/domaines/$NDD/
</pre>

h1. Suppression appli v12 

Se référer à ce wiki : https://redmine.prowebce.net/projects/v12/wiki/Suppression_appli_V12
