---
title: Déploiement d’un cluster Deis à 3 nœuds | Microsoft Docs
description: Cet article explique comment créer un cluster Deis à 3 nœuds sur Azure à l’aide d’un modèle Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: HaishiBai
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/24/2015
ms.author: hbai

---
# Déployer un cluster Deis à 3 nœuds
Cet article vous détaille l’approvisionnement d’un cluster [Deis](http://deis.io/) sur Azure. Il aborde toutes les étapes, de la création des certificats requis au déploiement et à la mise à l’échelle d’un exemple d’application **Go** sur le cluster qui vient d’être approvisionné.

Le diagramme suivant représente l’architecture du système déployé. Un administrateur système gère le cluster à l’aide d’outils Deis, comme **deis** et **deisctl**. Les connexions sont établies via un équilibreur de charge Azure, qui transfère les connexions à l’un des nœuds membres du cluster. Les clients accèdent aux applications déployées via l’équilibreur de charge, également. Dans ce cas, l’équilibreur de charge envoie le trafic à un maillage de routeur Deis, qui achemine le trafic vers des conteneurs Docker hébergés sur le cluster.

  ![Diagramme d’architecture du cluster Deis déployé](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

Pour exécuter les étapes suivantes, vous aurez besoin des éléments suivants :

* Un abonnement Azure actif. Si vous n’en avez pas, vous pouvez obtenir une version d’essai sur [azure.com](https://azure.microsoft.com/).
* Un ID professionnel ou scolaire afin de pouvoir utiliser des groupes de ressources Azure. Si vous avez un compte personnel et que vous vous connectez avec un ID Microsoft, vous devez [créer un ID de travail à partir de votre ID personnel](virtual-machines-windows-create-aad-work-id.md).
* Il vous faut également l’outil [Azure PowerShell](../powershell-install-configure.md) ou la [CLI Azure pour Mac, Linux et Windows](../xplat-cli-install.md) de votre système d’exploitation client.
* [OpenSSL](https://www.openssl.org/). OpenSSL est utilisé pour générer les certificats nécessaires.
* Un client Git, comme [Git Bash](https://git-scm.com/).
* Vous devez également configurer un serveur DNS pour pouvoir tester l’exemple d’application. Vous pouvez utiliser les serveurs ou services DNS qui prennent en charge les enregistrements DNS de caractères génériques A Record.
* Un ordinateur pour exécuter les outils clients Deis. Vous pouvez utiliser un ordinateur local ou une machine virtuelle. Vous pouvez exécuter ces outils sur n’importe quelle distribution Linux. Toutefois, les instructions suivantes concernent Ubuntu.

## Approvisionner le cluster
Dans cette section, vous allez utiliser un modèle [Azure Resource Manager](../resource-group-overview.md) à partir du référentiel open source [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Tout d’abord, copiez le modèle. Ensuite, créez une paire de clés SSH à des fins d’authentification. Ensuite, configurez le nouvel identifiant de votre cluster. Enfin, utilisez le script Shell ou le script PowerShell pour approvisionner le cluster.

1. Clonez le référentiel : [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
   
        git clone https://github.com/Azure/azure-quickstart-templates
2. Accédez au dossier de modèles :
   
        cd azure-quickstart-templates\deis-cluster-coreos
3. Créez une paire de clés SSH à l’aide de l’outil ssh-keygen :
   
        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"
4. Générez un certificat à l’aide de la clé privée ci-dessus :
   
        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]
5. Accédez à l’élément [https://discovery.etcd.io/new](https://discovery.etcd.io/new) pour générer un nouveau jeton de cluster, qui ressemblera à ce qui suit :
   
        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
   <br /> Chaque cluster CoreOS doit bénéficier d’un jeton unique, fourni par ce service gratuit. Pour plus d’informations, voir la [documentation CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/).
6. Modifiez le fichier **cloud-config.yaml** afin de remplacer le jeton **discovery** existant par le nouveau jeton :
   
        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...
7. Modifiez le fichier **azuredeploy-parameters.json** : ouvrez le certificat que vous avez créé à l’étape 4 dans un éditeur de texte. Copiez le texte figurant entre les éléments `----BEGIN CERTIFICATE-----` et `-----END CERTIFICATE-----`, et collez-le dans le paramètre **sshKeyData** (vous devrez supprimer tous les caractères de saut de ligne).
8. Modifiez le paramètre **newStorageAccountName**. Il s’agit du compte de stockage associé aux disques du système d’exploitation de la machine virtuelle. Le nom de ce compte doit être unique au niveau global.
9. Modifiez le paramètre **publicDomainName**. Il fera partie du nom DNS associé à l’adresse IP publique de l’équilibreur de charge. Le FQDN présente la valeur suivante : *[valeur de ce paramètre]*.*[région]*.cloudapp.azure.com. Par exemple, si vous spécifiez le nom « deishbai32 » et que le groupe de ressources est déployé dans la région ouest des États-Unis, le FQDN final de votre équilibreur de charge sera le suivant : deishbai32.westus.cloudapp.azure.com.
10. Enregistrez le fichier de paramètres. Ensuite, vous pouvez approvisionner le cluster au moyen de Microsoft Azure PowerShell :
    
        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml
    
    ou de la CLI Azure :
    
        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  
11. Une fois que le groupe de ressources est approvisionné, vous pouvez voir toutes les ressources dans le groupe sur le portail Azure Classic. Comme le montre la capture d’écran suivante, le groupe de ressources contient un réseau virtuel incluant trois machines virtuelles, associées au même groupe à haute disponibilité. Le groupe contient également un équilibreur de charge, qui est associé à une adresse IP publique.
    
    ![Groupe de ressources provisionné sur le portail Azure Classic](media/virtual-machines-linux-deis-cluster/resource-group.png)

## Installation du client
Vous devez utiliser l’outil **deisctl** pour contrôler votre cluster Deis. Bien que cet outil soit automatiquement installé sur tous les nœuds du cluster, nous vous recommandons de l’utiliser sur un ordinateur d’administration distinct. En outre, comme tous les nœuds sont configurés avec des adresses IP privées uniquement, vous devrez utiliser un tunnel SSL via l’équilibreur de charge, qui présente une adresse IP publique, pour la connexion à tous les nœuds. Voici les étapes à suivre pour configurer l’outil deisctl sur une machine virtuelle ou physique distincte dotée d’Ubuntu.

1. Installez deisctl:mkdir deis.
   
        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl
2. Ajoutez votre clé privée à l’agent ssh :
   
        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]
3. Configurez deisctl :
   
        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

Le modèle définit les règles NAT entrantes qui mappent la valeur 2223 à l’instance 1, la valeur 2224, à l’instance 2 et la valeur 2225, à l’instance 3. Cela assure la redondance liée à l’utilisation de l’outil deisctl. Vous pouvez examiner ces règles sur le portail Azure Classic :

![Règles NAT sur l’équilibreur de charge](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [!NOTE]
> Actuellement, le modèle prend uniquement en charge les clusters à 3 nœuds. En effet, la définition des règles NAT des modèles Azure Resource Manager présente une limitation : elle ne prend pas en charge la syntaxe de boucle.
> 
> 

## Installer et démarrer la plateforme Deis
Vous pouvez désormais utiliser l’outil deisctl pour installer et démarrer la plate-forme Deis :

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [!NOTE]
> Le démarrage de la plate-forme peut prendre un certain temps (jusqu’à 10 minutes, parfois). En particulier, le démarrage du service de générateur peut être long. De plus, vous devrez peut-être vous y prendre à plusieurs fois. Si l’opération se fige, appuyez sur `ctrl+c` pour arrêter l’exécution de la commande et recommencer.
> 
> 

Vous pouvez utiliser la commande `deisctl list` pour vérifier si tous les services sont exécutés :

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Félicitations ! Votre nouveau cluster Deis est opérationnel sur Azure. À présent, nous allons déployer un exemple d’application Go pour voir le cluster en action.

## Déployer et mettre à l’échelle une application Hello World
Les étapes suivantes indiquent comment déployer une application Go « Hellow World » sur le cluster. Ces étapes sont basées sur la [documentation Deis](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Pour que le maillage de routage fonctionne correctement, vous devrez disposer d’un enregistrement « A record » pour votre domaine, qui pointe sur l’adresse IP publique de l’équilibreur de charge. La capture d’écran suivante illustre l’enregistrement « A record » associé à un exemple d’enregistrement de domaine sur GoDaddy :
   
    ![Enregistrement « A Record » GoDaddy](media/virtual-machines-linux-deis-cluster/go-daddy.png)
   
   <p />
2. Installez l’outil deis :
   
        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
3. Créez une clé SSH, puis ajoutez la clé publique sur GitHub (bien sûr, vous pouvez également utiliser des clés existantes). Pour créer une paire de clés SSH, procédez comme suit :
   
        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)
4. Ajoute l’élément id\_rsa.pub, ou la clé publique de votre choix, à GitHub. Pour cela, utilisez le bouton permettant d’ajouter une clé SSH sur l’écran de configuration des clés SSH :
   
   ![Clé Github](media/virtual-machines-linux-deis-cluster/github-key.png)
   
   <p />
5. Enregistrez un nouvel utilisateur :
   
        deis register http://deis.[your domain]
   <p />
6. Ajoutez la clé SSH :
   
        deis keys:add [path to your SSH public key]
   <p />      
7. Créez une application.
   
        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
   <p />
8. L’action Git de type push déclenche la création et le déploiement d’images Docker, qui peuvent prendre quelques minutes. D’après mon expérience, il arrive parfois que l’étape 10 (insertion d’images dans le référentiel privé) se bloque. Dans ce cas, vous pouvez arrêter le processus, saisir la commande `deis apps:destroy –a <application name>` pour supprimer l’application et essayer à nouveau. La commande `deis apps:list` peut vous aider à rechercher le nom de votre application. Si tout fonctionne, des éléments similaires à ce qui suit doivent figurer à la fin des sorties de commandes :
   
        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
   <p />
9. Vérifiez que l’application fonctionne :
   
        curl -S http://[your application name].[your domain]
   Ce qui suit doit s’afficher :
   
        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
   <p />
10. Mettez l’application à l’échelle pour 3 instances :
    
        deis scale cmd=3
    <p />
11. Si vous le souhaitez, vous pouvez utiliser la commande « deis info » pour examiner les détails de votre application. Les sorties suivantes proviennent du déploiement de mon application :
    
        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }
    
        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)
    
        === lambda-underdog Domains
        No domains

## Étapes suivantes
Cet article vous a présenté toutes les étapes d’approvisionnement d’un nouveau cluster Deis sur Azure, au moyen d’un modèle Azure Resource Manager. Le modèle prend en charge la redondance des outils de connexion, ainsi que l’équilibrage de charge des applications déployées. Le modèle évite également d’utiliser des adresses IP publiques sur les nœuds membres, ce qui permet de préserver des ressources d’adresses IP publiques précieuses et fournit un environnement plus sécurisé pour les applications hôtes. Pour en savoir plus, consultez les articles suivants :

[Présentation d’Azure Resource Manager][resource-group-overview] [Utilisation de la CLI Azure][azure-command-line-tools] [Utilisation d’Azure PowerShell avec Azure Resource Manager][powershell-azure-resource-manager]

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

<!---HONumber=AcomDC_0824_2016-->