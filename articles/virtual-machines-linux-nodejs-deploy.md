---
title: "Déployer une application Node.js sur des machines virtuelles Linux dans Azure"
description: "Apprenez à déployer une application Node.js sur des machines virtuelles Linux dans Azure."
services: 
documentationcenter: nodejs
author: stepro
manager: dmitryr
editor: 
ms.assetid: 857a812d-c73e-4af7-a985-2d0baf8b6f71
ms.service: multiple
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2016
ms.author: stephpr
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 0de0314902805a2bdb37ce3c6f79ec221f3aed31


---
# <a name="deploy-a-nodejs-application-to-linux-virtual-machines-in-azure"></a>Déployer une application Node.js sur des machines virtuelles Linux dans Azure
Ce didacticiel montre comment prendre une application Node.js et la déployer sur des machines virtuelles Linux s’exécutant dans Azure. Les instructions de ce didacticiel s’appliquent à tous les systèmes d’exploitation pouvant exécuter Node.js.

Vous découvrirez comment effectuer les actions suivantes :

* Répliquer et cloner un référentiel GitHub contenant une application TODO simple.
* Créer et configurer deux machines virtuelles Linux dans Azure pour exécuter l’application.
* Itérer au sein de l’application en transmettant des mises à jour à la machine virtuelle frontale web.

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d’un compte GitHub et d’un compte Microsoft Azure, ainsi que de la possibilité d’utiliser Git à partir d’une machine de développement.
> 
> Si vous n’avez pas de compte GitHub, vous pouvez vous inscrire [ici](https://github.com/join).
> 
> Si vous n’avez pas de compte [Microsoft Azure](https://azure.microsoft.com/), vous pouvez vous inscrire à un essai GRATUIT [ici](https://azure.microsoft.com/pricing/free-trial/). Vous êtes également amené à suivre la procédure d’inscription pour obtenir un [compte Microsoft](http://account.microsoft.com) si vous n’en avez pas déjà un. Par ailleurs, si vous êtes abonné à Visual Studio, vous pouvez [activer vos avantages MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
> 
> Si vous n’avez pas git sur votre machine de développement et que vous utilisez un ordinateur Macintosh ou Windows, installez git à partir d’ [ici](http://www.git-scm.com). Si vous utilisez Linux, installez git en utilisant le mécanisme le plus approprié pour vous, comme `sudo apt-get install git`.
> 
> 

## <a name="forking-and-cloning-the-todo-application"></a>Réplication et clonage de l’application TODO
L’application TODO utilisée par ce didacticiel implémente une simple machine frontale web sur une instance MongoDB qui assure le suivi d’une liste de tâches. Après la connexion à GitHub, accédez [ici](https://github.com/stepro/node-todo) pour trouver l’application et la répliquer à l’aide du lien situé dans le coin supérieur droit. Cette action doit créer un référentiel dans votre compte nommé *nom_compte*/node-todo.

À présent sur votre machine de développement, clonez ce référentiel :

    git clone https://github.com/accountname/node-todo.git

Nous allons utiliser ce clone local du référentiel un peu plus tard quand nous apporterons des modifications au code source.

## <a name="creating-and-configuring-the-linux-virtual-machines"></a>Création et configuration des machines virtuelles Linux
Azure prend totalement en charge le calcul brut à l’aide de machines virtuelles Linux. Cette partie du didacticiel vous montre comment faire tourner facilement deux machines virtuelles Linux et y déployer l’application TODO, en exécutant la machine frontale web sur l’une et l’instance MongoDB sur l’autre.

### <a name="creating-virtual-machines"></a>Création de machines virtuelles
Le moyen le plus simple pour créer une machine virtuelle dans Azure consiste à utiliser le portail Azure. Cliquez [ici](https://portal.azure.com) pour vous connecter et lancer le portail Azure dans votre navigateur web. Une fois le portail Azure chargé, procédez comme suit :

* Cliquez sur le lien + Nouveau.
* Choisissez la catégorie Compute, puis Ubuntu Server 14.04 LTS.
* Sélectionnez le modèle de déploiement Resource Manager et cliquez sur Créer.
* Renseignez les informations de base en respectant les recommandations suivantes :
  * Spécifiez un nom que vous pourrez facilement identifier plus tard.
  * Pour ce didacticiel, choisissez l’authentification par mot de passe.
  * Créez un groupe de ressources portant un nom identifiable.
* Concernant la taille de la machine virtuelle, A1 Standard est un choix adapté à ce didacticiel.
* Pour les autres paramètres, assurez-vous que le type de disque est Standard et acceptez toutes les valeurs par défaut restantes.
* Lancez la création dans la page de synthèse.

Effectuez la procédure ci-dessus pour créer deux machines virtuelles Linux, une pour la machine frontale web et l’autre pour l’instance MongoDB. La création des machines virtuelles prend entre 5 et 10 minutes.

### <a name="assigning-a-dns-entry-for-virtual-machines"></a>Affectation d’une entrée DNS pour les machines virtuelles
Les machines virtuelles créées dans Azure sont par défaut uniquement accessibles par le biais d’une adresse IP publique comme 1.2.3.4. Facilitons l’identification des machines en leur attribuant des entrées DNS.

Une fois que le portail indique que les machines virtuelles ont été créées, cliquez sur le lien Machines virtuelles dans la barre de navigation gauche et recherchez vos machines. Pour chaque machine :

* Recherchez l’onglet Bases et cliquez sur l’adresse IP publique.
* Dans la configuration de l’adresse IP publique, attribuez une étiquette de nom DNS et enregistrez.

Le portail vérifie que le nom que vous spécifiez est disponible. Après avoir enregistré la configuration, vos machines virtuelles portent des noms d’hôte qui ressemblent à `machinename.region.cloudapp.azure.com`.

### <a name="connecting-to-the-virtual-machines"></a>Connexion aux machines virtuelles
Lors de l’approvisionnement de vos machines virtuelles, une pré-configuration a été effectuée pour autoriser les connexions à distance via SSH. Il s’agit du mécanisme que nous allons utiliser pour configurer les machines virtuelles. Si vous utilisez Windows pour votre développement, vous devez obtenir un client SSH si vous n’en avez pas déjà un. Il est courant de choisir PuTTY, téléchargeable à partir d’ [ici](http://www.chiark.greenend.org.uk/~sgtatham/putty/). Les systèmes d’exploitation Macintosh et Linux sont fournis avec une version du protocole SSH préinstallée.

### <a name="configuring-the-web-frontend-virtual-machine"></a>Configuration de la machine virtuelle frontale web
Protocole SSH vers la machine frontale web que vous avez créée à l’aide de PuTTY, de la ligne de commande ssh ou de votre autre outil SSH favori. S’affiche alors un message de bienvenue, suivi d’une invite de commandes.

Tout d’abord, vérifiez que git est installé ainsi que le nœud :

    sudo apt-get install -y git
    curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
    sudo apt-get install -y nodejs

Étant donné que la machine frontale web de l’application dépend de certains modules Node.js natifs, nous devons également installer l’ensemble de base des outils de génération :

    sudo apt-get install -y build-essential

Pour terminer, installons une application Node.js appelée *forever*, qui facilite l’exécution des applications serveur Node.js :

    sudo npm install -g forever

Celles-ci correspondent à toutes les dépendances nécessaires sur cette machine virtuelle pour pouvoir exécuter la machine frontale web de l’application, nous devons donc nous en occuper. Pour cela, nous allons tout d’abord créer un clone nu du référentiel GitHub que vous avez précédemment répliqué pour vous permettre de facilement publier des mises à jour vers la machine virtuelle (nous aborderons ce scénario de mise à jour plus tard), puis cloner le clone nu pour fournir une version du référentiel effectivement exécutable.

À partir du répertoire d’accueil (~), exécutez les commandes suivantes (en remplaçant *accountname* par votre nom de compte d’utilisateur GitHub) :

    git clone --bare https://github.com/accountname/node-todo.git
    git clone node-todo.git

Maintenant, entrez le répertoire node-todo et exécutez ces commandes :

    npm install
    forever start server.js

La machine frontale web de l’application est à présent en cours d’exécution, mais il reste une dernière étape avant de pouvoir accéder à l’application à partir d’un navigateur web. La machine virtuelle que vous avez créée est protégée par une ressource Azure appelée *groupe de sécurité réseau*, lequel a été créé quand vous avez approvisionné la machine virtuelle. Actuellement, cette ressource autorise uniquement le routage des demandes externes adressées au port 22 vers la machine virtuelle, ce qui permet la communication SSH avec la machine, mais rien d’autre. Ainsi, pour afficher l’application TODO qui est configurée pour s’exécuter sur le port 8080, ce port a également besoin d’être ouvert.

Revenez au portail Azure et procédez comme suit :

* Cliquez sur Groupes de ressources dans la barre de navigation gauche.
* Sélectionnez le groupe de ressources qui contient votre machine virtuelle.
* Dans la liste obtenue des ressources, sélectionnez le groupe de sécurité réseau (celui avec une icône de bouclier).
* Dans les propriétés, choisissez Règles de sécurité entrante.
* Dans la barre d’outils, cliquez sur Ajouter.
* Fournissez un nom comme default-allow-todo.
* Définissez le protocole sur TCP.
* Définissez la plage de ports de destination sur 8080.
* Cliquez sur OK et attendez que la règle de sécurité soit créée.

Après avoir créé cette règle de sécurité, l’application TODO est visible publiquement sur Internet et vous pouvez y accéder, par exemple en utilisant une URL telle que :

    http://machinename.region.cloudapp.azure.com:8080

Vous pouvez remarquer que bien que nous n’ayons pas encore configuré la machine virtuelle MongoDB, l’application TODO semble tout à fait fonctionnelle. Cela est dû au fait que le référentiel source est codé en dur pour utiliser une instance MongoDB pré-déployée. Une fois que nous avons configuré la machine virtuelle MongoDB, nous revenons en arrière et modifions le code source pour utiliser plutôt notre instance MongoDB privée.

### <a name="configuring-the-mongodb-virtual-machine"></a>Configuration de la machine virtuelle MongoDB
Protocole SSH vers la deuxième machine que vous avez créée à l’aide de PuTTY, de la ligne de commande ssh ou de votre autre outil SSH favori. Après l’affichage du message de bienvenue et de l’invite de commandes, installez MongoDB (ces instructions sont tirées d’ [ici](https://docs.mongodb.org/master/tutorial/install-mongodb-on-ubuntu/)) :

    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
    echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

Par défaut, MongoDB est configuré pour être uniquement accessible localement. Pour ce didacticiel, nous allons configurer MongoDB pour qu’il soit accessible à partir de la machine virtuelle de l’application. Dans un contexte sudo, ouvrez le fichier /etc/mongod.conf et recherchez la section `# network interfaces` . Remplacez la valeur de configuration `net.bindIp` en `0.0.0.0`.

> [!NOTE]
> Cette configuration sert uniquement dans le cadre de ce didacticiel. Il ne s’agit **pas** d’une pratique de sécurité recommandée et elle ne doit pas être utilisée dans les environnements de production.
> 
> 

Maintenant, vérifiez que le service MongoDB a démarré :

    sudo service mongod restart

MongoDB fonctionne sur le port 27017 par défaut. Ainsi, de la même façon que nous avions besoin d’ouvrir le port 8080 sur la machine virtuelle frontale web, nous devons ouvrir le port 27017 sur la machine virtuelle MongoDB.

Revenez au portail Azure et procédez comme suit :

* Cliquez sur Groupes de ressources dans la barre de navigation gauche.
* Sélectionnez le groupe de ressources qui contient la machine virtuelle MongoDB.
* Dans la liste obtenue des ressources, sélectionnez le groupe de sécurité réseau (celui avec une icône de bouclier) qui porte le même nom que celui que vous avez donné à la machine virtuelle MongoDB.
* Dans les propriétés, choisissez Règles de sécurité entrante.
* Dans la barre d’outils, cliquez sur Ajouter.
* Fournissez un nom comme default-allow-mongo.
* Définissez le protocole sur TCP.
* Définissez la plage de ports de destination sur 27017.
* Cliquez sur OK et attendez que la règle de sécurité soit créée.

## <a name="iterating-on-the-todo-application"></a>Itération au sein de l’application TODO
Jusqu’à maintenant, nous avons approvisionné les deux machines virtuelles Linux : celle qui exécute la machine frontale web et celle qui exécute une instance MongoDB. Mais il y a un problème : la machine frontale web n’utilise pas encore l’instance MongoDB approvisionnée. Nous allons corriger cela en mettant à jour le code frontal web pour utiliser une variable d’environnement au lieu d’une instance codée en dur.

### <a name="changing-the-todo-application"></a>Modification de l’application TODO
Sur votre machine de développement où vous avez cloné le référentiel node-todo au départ, ouvrez le fichier `node-todo/config/database.js` dans votre éditeur favori et modifiez la valeur de l’URL en remplaçant la valeur codée en dur comme `mongodb://...` par `process.env.MONGODB`.

Validez vos modifications et transmettez au maître GitHub :

    git commit -am "Get MongoDB instance from env"
    git push origin master

Malheureusement, cela ne permet pas de publier la modification apportée à la machine virtuelle frontale web. Nous allons apporter d’autres modifications à cette machine virtuelle pour activer un mécanisme simple mais efficace de publication des mises à jour pour que vous puissiez observer rapidement l’effet des modifications dans l’environnement live.

### <a name="configuring-the-web-frontend-virtual-machine"></a>Configuration de la machine virtuelle frontale web
Rappelez-vous que nous avons précédemment créé un clone nu du référentiel node-todo sur la machine virtuelle frontale web. Il s’avère que cette action a créé un Git distant auquel les modifications peuvent être transmises. Mais la simple transmission à ce Git distant ne donne pas tout à fait le modèle d’itération rapide que recherchent les développeurs qui travaillent sur leur code.

Nous aimerions pouvoir vous assurer que, quand une transmission au référentiel distant est effectuée sur la machine virtuelle, l’application TODO en cours d’exécution est automatiquement mise à jour. Heureusement, c’est ce que Git permet facilement.

Git expose plusieurs hooks appelés à des moments particuliers pour réagir à des actions effectuées sur le référentiel. Celles-ci sont spécifiées à l’aide de scripts Shell dans le dossier `hooks` du référentiel. Le hook le plus approprié au scénario de mise à jour automatique est l’événement `post-update` .

Dans une session SSH vers la machine virtuelle frontend web, remplacez le répertoire `~/node-todo.git/hooks` et ajoutez le contenu suivant à un fichier nommé `post-update` (en remplaçant `machinename` et `region` par les informations de votre machine virtuelle MongoDB) :

    #!/bin/bash

    forever stopall
    unset 'GIT_DIR'
    export MONGODB="mongodb://machinename.region.cloudapp.azure.com:27017/tododb"
    cd ~/node-todo && git fetch origin && git pull origin master && npm install && forever start ~/node-todo/server.js
    exec git update-server-info

Vérifiez que ce fichier est exécutable en exécutant la commande suivante :

    chmod 755 post-update

Ce script permet de veiller à ce que l’application serveur actuelle soit arrêtée, le code dans le référentiel cloné soit mis à jour vers la dernière version, toutes les dépendances mises à jour soient satisfaites et le serveur soit redémarré. Il permet également de veiller à ce que l’environnement soit configuré en vue de la réception de notre première mise à jour de l’application pour obtenir l’instance MongoDB à partir d’une variable d’environnement.

### <a name="configuring-your-development-machine"></a>Configuration de votre machine de développement
Maintenant nous allons effectuer un hook entre votre machine de développement et la machine virtuelle frontale web. Cette opération est aussi simple que l’ajout du référentiel nu sur la machine virtuelle en tant que machine distante. Pour cela, exécutez la commande suivante (en remplaçant *user* par le nom de connexion de la machine virtuelle frontend web et *machinename* et *region* selon vos besoins) :

    git remote add azure user@machinename.region.cloudapp.azure.com:node-todo.git

Voilà tout ce qui est nécessaire pour activer la transmission ou publication des modifications vers la machine virtuelle frontale web.

### <a name="publishing-updates"></a>Publication des mises à jour
Publions la seule modification apportée jusqu’ici pour que l’application utilise notre propre instance MongoDB :

    git push azure master

La sortie doit ressembler à celle-ci :

    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (3/3), done.
    Writing objects: 100% (4/4), 406 bytes | 0 bytes/s, done.
    Total 4 (delta 1), reused 0 (delta 0)
    remote: info:    Forever stopped processes:
    remote: data:        uid  command         script    forever pid  id logfile                         uptime
    remote: data:    [0] 0Lyh /usr/bin/nodejs server.js 9064    9301    /home/username/.forever/0Lyh.log 0:0:3:17.487
    remote: From /home/username/node-todo
    remote:    5f31fd7..5bc7be5  master     -> origin/master
    remote: From /home/username/node-todo
    remote:  * branch            master     -> FETCH_HEAD
    remote: Updating 5f31fd7..5bc7be5
    remote: Fast-forward
    remote:  config/database.js | 2 +-
    remote:  1 file changed, 1 insertion(+), 1 deletion(-)
    remote: npm WARN package.json node-todo@0.0.0 No repository field.
    remote: npm WARN package.json node-todo@0.0.0 No license field.
    remote: warn:    --minUptime not set. Defaulting to: 1000ms
    remote: warn:    --spinSleepTime not set. Your script will exit if it does not stay up for at least 1000ms
    remote: info:    Forever processing file: /home/username/node-todo/server.js
    To username@machinename.region.cloudapp.azure.com:node-todo.git
    5f31fd7..5bc7be5  master -> master

Une fois cette commande terminée, essayez d’actualiser l’application dans un navigateur web. Vous devriez être en mesure de voir que la liste TODO présentée ici est vide et n’est plus liée à l’instance MongoDB déployée partagée.

Pour terminer ce didacticiel, apportons une autre modification plus visible. Sur votre machine de développement, ouvrez le fichier node-todo/public/index.html à l’aide de votre éditeur favori. Recherchez l’en-tête jumbotron et remplacez le titre « I'm a Todo-aholic » par « I'm a Todo-aholic on Azure! ».

Maintenant procédons à la validation :

    git commit -am "Azurify the title"

Cette fois, publions la modification sur Azure avant de la retransmettre au référentiel GitHub :

    git push azure master

Une fois cette commande exécutée, actualisez la page web pour que les modifications apparaissent. Puisqu’elles sont correctes, retransmettez la modification à la machine distante d’origine : 

    git push origin master

## <a name="next-steps"></a>Étapes suivantes
Cet article montre comment prendre une application Node.js et la déployer sur des machines virtuelles Linux s’exécutant dans Azure. Pour en savoir plus sur les machines virtuelles Linux dans Azure, consultez [Présentation de Linux sur Azure](/documentation/articles/virtual-machines-linux-introduction/).

Pour plus d’informations sur le développement d’applications Node.js dans Azure, consultez le [Centre de développement Node.js](/develop/nodejs/).




<!--HONumber=Feb17_HO3-->


