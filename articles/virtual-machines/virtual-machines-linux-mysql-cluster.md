<properties
	pageTitle="Utilisation de jeux d'équilibrage de la charge pour regrouper MySQL sur Linux"
	description="Article qui illustre les modèles de configuration d'un cluster Linux à charge équilibrée et haute disponibilité sur Azure en utilisant MySQL à titre d'exemple"
	services="virtual-machines"
	documentationCenter=""
	authors="bureado"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2015"
	ms.author="jparrel"/>

# Utilisation de jeux d'équilibrage de la charge pour regrouper MySQL sur Linux

* [Préparation](#getting-ready)
* [Configuration du cluster](#setting-up-the-cluster)
* [Configuration de MySQL](#setting-up-mysql)
* [Configuration de Corosync](#setting-up-corosync)
* [Configuration de Pacemaker](#setting-up-pacemaker)
* [Test](#testing)
* [STONITH](#stonith)
* [Limitations](#limitations)

## Introduction

L'objectif de cet article est d'explorer et d'illustrer les différentes approches disponibles pour déployer des services Linux hautement disponibles sur Microsoft Azure en explorant la haute disponibilité de MySQL Server. Une vidéo illustrant cette approche est disponible sur [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Nous présentons une solution haute disponibilité MySQL à maître unique, deux nœuds et sans partage basée sur DRBD, Corosync et Pacemaker. Seul un nœud à la fois exécute MySQL. La lecture et l'écriture à partir de la ressource DRBD sont également limitées à un seul nœud à la fois.

Les solutions d'adresses IP virtuelles, telles que LVS, ne sont pas nécessaires, car nous utilisons les jeux d'équilibrage de la charge de Microsoft Azure pour fournir la fonctionnalité de tourniquet (round robin), ainsi que la détection de points de terminaison, la suppression et la récupération sans perte de données des adresses IP virtuelles. L'adresse IP virtuelle est une adresse IPv4 globalement routable attribuée par Microsoft Azure lors de la création du service cloud.

Plusieurs architectures sont possibles pour MySQL, comme NBD Cluster, Percona et Galera, ainsi que plusieurs solutions intermédiaires, notamment au moins une disponible sous forme de machine virtuelle sur [VM Depot](http://vmdepot.msopentech.com). Tant que ces solutions peuvent se répliquer en monodiffusion, multidiffusion ou en diffusion et tant qu'elles ne reposent pas sur un stockage partagé ou plusieurs interfaces réseau, les scénarios doivent être faciles à déployer sur Microsoft Azure.

Bien sûr, ces architectures de clustering peuvent être étendues de la même façon à d'autres produits tels que PostgreSQL et OpenLDAP. Cette procédure d'équilibrage de charge sans partage peut notamment être testée correctement avec OpenLDAP à multiples maîtres. Vous pouvez regarder la vidéo sur le blog Channel 9.

## Préparation

Vous avez besoin d'un compte Microsoft Azure avec un abonnement valide, permettant de créer au moins deux (2) machines virtuelles (XS est utilisé dans cet exemple), un réseau et un sous-réseau, un groupe d'affinités et un groupe à haute disponibilité, ainsi que de créer des disques durs virtuels dans la même région que le service cloud et d'associer ces derniers aux machines virtuelles Linux.

### Environnement testé

- Ubuntu 13.10
  - DRBD
  - MySQL Server
  - Corosync et Pacemaker

### Groupe d'affinités

Un groupe d'affinités pour la solution est créé en se connectant au portail Azure, en faisant défiler jusqu'à Paramètres et en créant un groupe d'affinités. Des ressources allouées créées ultérieurement seront attribuées à ce groupe d'affinités.

### Réseaux

Un réseau est créé et un sous-réseau est créé à l'intérieur de ce dernier. Nous avons choisi un réseau 10.10.10.0/24 avec un seul sous-réseau /24 dedans.

### Machines virtuelles

La première machine virtuelle Ubuntu 13.10 est créée à l’aide d’une image de la galerie Ubuntu prise en charge, appelée `hadb01`. Un service cloud est créé au cours du processus. Il se nomme hadb. Nous l'avons appelé ainsi pour illustrer la nature partagée et l'équilibre de charge du service lorsque nous lui ajouterons des ressources supplémentaires. La création de `hadb01` est sans incident. Elle est réalisée à l’aide du portail. Un point de terminaison est automatiquement créé pour SSH et notre réseau créé est sélectionné. Nous choisissons également de créer un groupe à haute disponibilité pour les machines virtuelles.

Une fois la première machine virtuelle créée (techniquement, lors de la création du service cloud), nous créons la deuxième machine virtuelle, `hadb02`. Pour celle-ci, nous utilisons également la machine virtuelle Ubuntu 13.10 de la galerie à l’aide du portail, mais nous choisissons d’utiliser un service cloud existant, `hadb.cloudapp.net`, au lieu d’en créer un. Le réseau et le groupe à haute disponibilité doivent être automatiquement sélectionnés. Un point de terminaison SSH est également créé.

Une fois les deux machines virtuelles créées, nous notons le port SSH pour `hadb01` (TCP 22) et `hadb02` (attribués automatiquement par Azure).

### Stockage associé

Nous associons un nouveau disque aux deux machines virtuelles et créons de nouveaux disques de 5 Go au cours du processus. Les disques sont hébergés dans le conteneur de disques durs virtuels utilisé pour nos disques de système d'exploitation principaux. Une fois les disques créés et associés, il est inutile de redémarrer Linux, car le noyau voit le nouvel appareil (généralement `/dev/sdc`, vous pouvez vérifier `dmesg` pour le résultat).

Sur chaque machine virtuelle, nous poursuivons en créant une nouvelle partition à l’aide de `cfdisk` (partition Linux principale) et écrivons la nouvelle table de partition. **Ne créez pas de système de fichiers sur cette partition**.

## Configuration du cluster

Sur les deux machines virtuelles Ubuntu, nous devons utiliser APT pour installer Corosync, Pacemaker et DRBD. Utilisation de `apt-get` :

    sudo apt-get install corosync pacemaker drbd8-utils.

**N'installez pas MySQL pour l'instant**. Les scripts d’installation Debian et Ubuntu initialisent un répertoire de données MySQL sur `/var/lib/mysql`, mais le répertoire étant remplacé par un système de fichiers DRBD, nous devons faire cela ultérieurement.

À présent, nous devons également vérifier (à l’aide de `/sbin/ifconfig`) que les deux machines virtuelles utilisent des adresses du sous-réseau 10.10.10.0/24 et qu’elles peuvent s’envoyer des commandes ping en utilisant leur nom. Si vous le souhaitez, vous pouvez également utiliser `ssh-keygen` et `ssh-copy-id` pour vérifier que les deux machines virtuelles peuvent communiquer via SSH sans nécessiter de mot de passe.

### Configuration de DRBD

Nous allons créer une ressource DRBD utilisant la partition `/dev/sdc1`sous-jacente pour produire une ressource `/dev/drbd1` capable d’être formatée à l’aide d’ext3 et utilisée dans les nœuds principaux et secondaires. Pour ce faire, ouvrez `/etc/drbd.d/r0.res` et copiez la définition de ressource suivante. Faites cela dans les deux machines virtuelles :

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Ensuite, lancez la ressource à l’aide de `drbdadm` dans les deux machines virtuelles :

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

Enfin, sur la machine virtuelle principale (`hadb01`) forcez la propriété (principale) de la ressource DRBD :

    sudo drbdadm primary --force r0

Si vous examinez le contenu de /proc/drbd (`sudo cat /proc/drbd`) sur les deux machines virtuelles, vous devez voir `Primary/Secondary` sur `hadb01` et `Secondary/Primary` sur `hadb02`, ce qui est cohérent avec la solution jusqu’à présent. Le disque de 5 Go sera synchronisé sur le réseau 10.10.10.0/24 gratuitement pour les clients.

Une fois le disque synchronisé, vous pouvez créer le système de fichiers sur `hadb01`. À des fins de test, nous avons utilisé ext2, mais l'instruction suivante créera un système de fichiers ext3 :

    mkfs.ext3 /dev/drbd1

### Installation de la ressource DRBD

Sur `hadb01`, nous sommes maintenant prêts à installer des ressources DRBD. Le système Debian et ses dérivés utilisent `/var/lib/mysql` en tant que répertoire de données MySQL. Puisque nous n'avons pas installé MySQL, nous allons créer le répertoire et monter la ressource DRBD. Sur `hadb01` :

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## Configuration de MySQL

Vous êtes maintenant prêt à installer MySQL sur `hadb01` :

    sudo apt-get install mysql-server

Pour `hadb02`, vous avez deux options. Vous pouvez installer mysql-server maintenant, ce qui créera /var/lib/mysql et y mettra un nouveau répertoire de données, puis passer à la suppression du contenu. Sur `hadb02` :

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

La deuxième option est de basculer vers `hadb02` et d’y installer mysql-server (les scripts d’installation remarqueront l’installation existante et n’y toucheront pas).

Sur `hadb01` :

    sudo drbdadm secondary –force r0

Sur `hadb02` :

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Si vous ne prévoyez pas de faire basculer DRBD maintenant, la première option, bien que sans doute moins élégante, est la plus simple. Une fois la configuration terminée, vous pouvez commencer à travailler sur votre base de données MySQL. Sur `hadb02` (ou sur le serveur actif selon DRBD) :

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.* TO root;

**Avertissement** : cette dernière instruction désactive de manière efficace l’authentification de l’utilisateur racine dans ce tableau. Elle doit être remplacée par vos instructions GRANT de production et n'est incluse qu'à titre d'exemple.

Vous devez également activer la mise en réseau pour MySQL si vous souhaitez effectuer des requêtes depuis l'extérieur des machines virtuelles, ce qui est l'objet de ce guide. Sur les deux machines virtuelles, ouvrez `/etc/mysql/my.cnf` et accédez à `bind-address` pour remplacer 127.0.0.1 par 0.0.0.0. Après avoir enregistré le fichier, émettez une commande `sudo service mysql restart` sur votre machine virtuelle principale actuelle.

### Création du jeu d'équilibrage de la charge MySQL

Retournons au portail Azure et accédons à la machine virtuelle `hadb01`, puis aux points de terminaison. Créons un point de terminaison, puis choisissons MySQL (TCP 3306) dans la liste déroulante et cochons la case *Créer un jeu d'équilibrage de la charge*. Nous appellerons notre point de terminaison à charge équilibrée `lb-mysql`. Laissez la plupart des options telles quelles, hormis le délai, qu'il faut réduire à 5 (secondes, minimum).

Une fois le point de terminaison créé, accédez à `hadb02`, Points de terminaison, et créez un nouveau point de terminaison. Cependant, choisissez `lb-mysql`, puis sélectionnez MySQL dans le menu déroulant. Vous pouvez aussi utiliser l'interface de ligne de commande Azure pour cette étape.

Nous avons désormais tout le nécessaire pour un fonctionnement manuel du cluster.

### Test du jeu d'équilibrage de la charge

Des tests peuvent être effectués à partir d'une machine externe à l'aide de n'importe quel client MySQL ou application (par exemple, phpMyAdmin exécuté en tant que site web Azure). Dans le cas présent, nous utilisons l'outil en ligne de commande de MySQL sur une autre boîte Linux :

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### Basculement manuel

Vous pouvez simuler des basculements en arrêtant MySQL, en commutant la machine virtuelle principale de DRBD et en redémarrant MySQL.

Sur hadb01 :

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Puis, sur hadb02 :

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Après un basculement manuel, vous pouvez répéter votre requête à distance. Elle devrait fonctionner parfaitement.

## Configuration de Corosync

Corosync est l'infrastructure de clusters sous-jacente nécessaire au fonctionnement de Pacemaker. Pour les utilisateurs de Heartbeat v1 et v2 (ainsi que d'autres méthodologies comme Ultramonkey), Corosync est une division des fonctionnalités CRM, tandis que Pacemaker reste plus proche de Heartbeat du point de vue des fonctionnalités.

La principale contrainte de Corosync sur Azure est qu'il préfère les communications multidiffusion sur diffusion aux communications monodiffusion, alors que les réseaux Microsoft Azure ne prennent en charge que la monodiffusion.

Heureusement, Corosync dispose d'un mode de monodiffusion fonctionnel. La seule contrainte réelle est que, puisque tous les nœuds communiquent entre eux de façon *automagique*, vous devez les définir dans vos fichiers de configuration en incluant leurs adresses IP. Nous pouvons utiliser les fichiers d’exemple Corosync pour la monodiffusion et ne modifier que l’adresse de liaison, les listes de nœuds et le répertoire de journalisation (Ubuntu utilise `/var/log/corosync` alors que les fichiers d’exemple utilisent `/var/log/cluster`) en activant les outils de quorum.

**Remarquez la directive `transport: udpu` ci-dessous et les adresses IP des nœuds, définies manuellement**.

Sur  `/etc/corosync/corosync.conf` pour les deux nœuds :

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Nous copions ce fichier de configuration dans les deux machines virtuelles et démarrons Corosync dans les deux nœuds :

    sudo service start corosync

Peu après le démarrage du service, le cluster doit être établi dans l'anneau actuel et un quorum doit être constitué. Il est possible de vérifier cette fonctionnalité en consultant les journaux ou :

    sudo corosync-quorumtool –l

Une sortie similaire à l'image ci-dessous devrait suivre :

![corosync-quorumtool -l sample output](media/virtual-machines-linux-mysql-cluster/image001.png)

## Configuration de Pacemaker

Pacemaker utilise le cluster pour surveiller les ressources, définir le moment où les machines virtuelles principales s'arrêtent et les faire basculer vers les machines secondaires. Les ressources peuvent notamment être définies à partir d'un ensemble de scripts disponibles ou à partir de script LSB (de type init).

Nous voulons que Pacemaker « s'accapare » de la ressource DRBD, du point de montage et du service MySQL. Si Pacemaker peut activer et désactiver DRBD, montez/démontez-le et démarrez/arrêtez MySQL dans le bon ordre en cas de problème avec la machine virtuelle principale. La configuration est terminée.

Lors de la première installation de Pacemaker, votre configuration doit être simple. Exemple :

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Vérifiez-la en exécutant `sudo crm configure show`. Maintenant, créez un fichier (par exemple, `/tmp/cluster.conf`) avec les ressources suivantes :

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

Ensuite, chargez-le dans la configuration (il n'est utile de le faire que dans un seul nœud) :

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Veillez également à ce que Pacemaker se lance au démarrage dans les deux nœuds :

    sudo update-rc.d pacemaker defaults

Après quelques secondes, à l’aide de `sudo crm_mon –L`, vérifiez que l’un de vos nœuds devient le maître du cluster et qu’il exécute toutes les ressources. Vous pouvez utiliser mount et ps pour vérifier que les ressources sont en cours d'exécution.

La capture d’écran suivante montre `crm_mon` avec un seul nœud arrêté (quittez avec Ctrl-C).

![crm_mon node stopped](media/virtual-machines-linux-mysql-cluster/image002.png)

Cette capture d'écran montre les deux nœuds, avec un maître et un esclave :

![crm_mon operational master/slave](media/virtual-machines-linux-mysql-cluster/image003.png)

## Test

Nous sommes prêts pour une simulation de basculement automatique. Il existe deux façons de procéder : de manière douce et de manière dure. La méthode douce consiste à utiliser la fonction d’arrêt du cluster : ``crm_standby -U `uname -n` -v on``. Si on utilise cette fonction sur le maître, l'esclave prend le relais. N'oubliez pas de redésactiver cette fonction (sinon, crm_mon vous indiquera qu'un nœud est en veille).

La méthode dure consiste à arrêter la machine virtuelle principale (hadb01) via le portail ou à changer le niveau d'exécution de la machine virtuelle (halt, shutdown) afin d'aider Corosync et Pacemaker en signalant que le maître va s'arrêter. Il est possible de tester ceci (c'est utile pour la maintenance), mais aussi de forcer le scénario en se contentant de geler la machine virtuelle.

## STONITH

Il doit être possible d’effectuer un arrêt de la machine virtuelle grâce aux outils en ligne de commande Azure pour Linux à la place d’un script STONITH contrôlant un périphérique physique. Vous pouvez utiliser `/usr/lib/stonith/plugins/external/ssh` comme base et activer STONITH dans la configuration du cluster. L'interface de ligne de commande Azure doit être installée globalement et les paramètres/le profil de publication doivent être chargés pour l'utilisateur du cluster.

Un exemple de code pour la ressource est disponible sur [GitHub](https://github.com/bureado/aztonith). Il est nécessaire de changer la configuration du cluster en ajoutant le code suivant à `sudo crm configure` :

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Remarque :** le script n’effectue pas de vérification en amont/en aval. La ressource SSH d'origine avait 15 vérifications ping, mais le temps de récupération d'une machine virtuelle Azure peut être variable.

## Limites

Les limites suivantes s'appliquent :

- Le script de ressource DRBD linbit qui gère DRBD comme ressource dans Pacemaker utilise `drbdadm down` lors de l’arrêt d’un nœud, même si celui-ci est seulement passé en veille. Cette situation n'est pas idéale, car l'esclave ne synchronisera pas la ressource DRBD lorsque le maître recevra des écritures. Si le maître n'échoue pas par commande, l'esclave peut prendre un état de système de fichiers antérieur. Cela peut être résolu de deux façons :
  - En appliquant un `drbdadm up r0` dans tous les nœuds du cluster grâce à une surveillance locale (non groupée), ou
  - en modifiant le script DRBD linbit, en veillant à ce que `down` ne soit pas appelé, dans `/usr/lib/ocf/resource.d/linbit/drbd`.
- L'équilibrage de charge a besoin d'au moins 5 secondes pour répondre. Les applications doivent donc être conscientes des clusters et plus tolérantes en matière de délai. D'autres architectures peuvent également aider, par exemple les files d'attente intégrées, les intergiciels de requête, etc.
- Un réglage de MySQL est nécessaire pour veiller à ce que l'écriture soit effectuée à un rythme raisonnable et que les caches soient vidés le plus souvent possible afin de limiter la perte de mémoire.
- Les performances d'écriture dépendront de l'interconnexion des machines virtuelles dans le commutateur virtuel, car il s'agit du mécanisme utilisé par DRBD pour répliquer le périphérique.
 

<!---HONumber=58_postMigration-->