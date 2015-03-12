<properties 
	pageTitle="Utilisation de l'extension Docker VM pour Linux sur Azure" 
	description="Décrit Docker et les extensions Azure Virtual Machines, et illustre la création par programme de machines virtuelles sur Azure qui sont des hôtes Docker à partir de la ligne de commande en utilisant l'interface de commande azure-cli." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="10/21/2014" 
	ms.author="rasquill"/>
# Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)
Cette rubrique décrit la création d'une machine virtuelle avec l'extension Docker VM à partir de l'interface xplat-cli sur n'importe quelle plateforme. [Docker](https://www.docker.com/) fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux](http://en.wikipedia.org/wiki/LXC) plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Vous pouvez utiliser l'extension Docker VM sur [l'agent Linux Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) afin de créer une machine virtuelle Docker hébergeant un nombre indéfini de conteneurs pour vos applications sur Azure. Pour une discussion sur les conteneurs et leurs avantages, consultez le [Tableau blanc Docker de haut niveau](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

+ [Utilisation de l'extension Docker VM avec Azure]
+ [Extensions de machine virtuelle pour Linux et Windows] 
+ [Conteneurs et ressources de gestion de conteneurs pour Azure]
+ [Étapes suivantes]



## <a id='How to use the Docker VM Extension with Azure'>Utilisation de l'extension Docker VM avec Azure</a>
Pour utiliser l'extension Docker VM avec Azure, vous devez installer une version de [l'interface de ligne de commande interplateforme Azure](https://github.com/Azure/azure-sdk-tools-xplat) (**xplat-cli** dans cette rubrique) supérieure à 0.8.6 (à la date de rédaction de ce document, la version la plus récente est 0.8.10). Vous pouvez installer cette interface sur Mac, Linux et Windows. 

> [AZURE.NOTE] Bien que vous puissiez installer l'interface xplat-cli sur Microsoft Windows, Docker a été compilé avec des dépendances de noyau propres à Linux. Par conséquent, pour utiliser Windows comme client Docker, vous devez héberger une distribution Linux complète comme machine virtuelle dans Hyper-V ou un autre hyperviseur. Vous pouvez alors utiliser l'interface et les commandes Docker de ce document et celles de Docker. Docker comporte un programme de configuration pour Windows [Boot2Docker](https://docs.docker.com/installation/windows/) que vous pouvez utiliser pour automatiser cette configuration.

Le processus d'utilisation de Docker sur Azure est relativement simple :

+ Installez les outils en ligne de commande xplat-cli et leurs dépendances sur l'ordinateur à partir duquel vous souhaitez contrôler Azure (sur Windows, il s'agira d'une distribution Linux qui s'exécute comme une machine virtuelle).
+ Utilisez les commandes Docker de l'interface xplat-cli pour créer un hôte Docker VM dans Azure.
+ Utilisez les commandes Docker locales pour gérer vos conteneurs dans votre machine virtuelle Docker sous Azure.

> [AZURE.NOTE] L'interface de ligne de commande xplat-cli est actuellement la seule méthode permettant de créer une machine virtuelle contrôlée par Docker sur Azure en vue d'héberger des conteneurs Docker. 

### Installation de l'interface de ligne de commande xplat-cli
Pour en savoir plus sur l'installation et la configuration de l'interface de ligne de commande interplateforme Azure, consultez la page [Installation de l'interface de ligne de commande interplateforme Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/#install). Pour vérifier l'installation, tapez `azure` à l'invite de commandes. Après quelques secondes, l'illustration ASCII xplat-cli doit s'afficher. Elle répertorie les commandes de base disponibles. Si l'installation s'est déroulée correctement, vous pouvez taper `azure help vm` et voir que l'une des commandes répertoriées est " docker ".

> [AZURE.NOTE] Si vous utilisez une installation Ubuntu 14.04 LTS, cette image comporte une installation de nœud légèrement différente qui peut nécessiter quelques opérations supplémentaires. Vous trouverez, dans la section **Installation à l'aide d'un PPA** de cet article, [une solution](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-an-ubuntu-14-04-server) qui donne d'excellents résultats. Cette section vous explique comment installer directement la version la plus récente de Node.js, ce qui semble bien fonctionner sur une distribution Ubuntu 14.04 LTS. 

### Connexion de l'interface xplat-cli à votre compte Azure
Avant de pouvoir utiliser l'interface xplat-cli, vous devez y associer les informations d'identification de votre compte Azure sur votre plateforme. La section [Connexion à votre abonnement Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/#configure) explique comment télécharger et importer votre fichier **.publishsettings** ou associer votre ligne de commande xplat-cli à un ID d'organisation. 

> [AZURE.NOTE] Le comportement diffère quelque peu selon que vous utilisez l'une ou l'autre méthode d'authentification. Veuillez donc lire attentivement le document ci-dessus pour bien comprendre les différentes fonctionnalités. 

### Installation de Docker et utilisation de l'extension Docker VM pour Azure
Suivez les [instructions d'installation de Docker](https://docs.docker.com/installation/#installation) pour installer Docker sur votre ordinateur. Pour la plupart des distributions et systèmes d'exploitation, cela signifie taper la commande `apt-get install docker.io`. Vérifiez que la version de Docker est supérieure ou égale à 1.0.

Pour utiliser Docker avec une machine virtuelle Azure, l'[Agent de machine virtuelle Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) doit être installé dans l'image Linux utilisée pour la machine virtuelle. Actuellement, seuls deux types d'image le permettent :

+ une image Ubuntu de la galerie d'images Azure ou 

+ une image Linux personnalisée que vous avez créée avec l'Agent de machine virtuelle Linux Azure installé et configuré. Consultez la rubrique [Agent de machine virtuelle Linux Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-agent-user-guide/) pour en savoir plus sur la création d'une machine virtuelle Linux personnalisée avec l'Agent de machine virtuelle Azure.

### Utilisation de la galerie d'images Azure

Dans une session Bash ou Terminal, utilisez la commande xplat-cli suivante pour localiser l'image Ubuntu la plus récente dans la galerie de machines virtuelles ; tapez :

`azure vm image list | grep Ubuntu-14_04`

Puis sélectionnez un des noms d'images (p. ex. `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-fr-fr-30GB`) ; utilisez la commande suivante pour créer une machine virtuelle en utilisant cette image. 

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04-LTS-amd64-server-20140724-fr-fr-30GB" <username> <password>
``` 

où :

+ *&lt;vm-cloudservice name&gt;* est le nom de la machine virtuelle qui deviendra l'ordinateur hôte du conteneur Docker dans Azure

+  *&lt;username&gt;* est le nom de l'utilisateur racine par défaut de la machine virtuelle

+ *&lt;password&gt;* est le mot de passe du compte *username* remplissant les conditions de complexité Azure 
 
> [AZURE.NOTE] Un mot de passe doit comporter au moins 8 caractères, dont une lettre minuscule et une lettre majuscule, un chiffre et un caractère spécial parmi les suivants : `!@#$%^&+=`. Le point à la fin de la phrase précédente n'est PAS un caractère spécial. 

Si la commande a été exécutée correctement, vous devriez normalement obtenir un résultat comparable à ce qui est illustré ci-dessous, suivant les arguments et options spécifiques que vous avez utilisés :

![](./media/virtual-machines-docker/dockercreateresults.png)

> [AZURE.NOTE] La création d'une machine virtuelle peut prendre quelques minutes. Cependant, une fois l'approvisionnement terminé, le démon Docker (service Docker) démarre et vous pouvez vous connecter à l'hôte du conteneur Docker.

Pour tester la machine virtuelle Docker que vous avez créée dans Azure, tapez

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:4243 info`

où *<vm-name-you-used>* est le nom de la machine virtuelle utilisée dans votre appel vers `azure vm docker create`. Ce que vous devez voir est similaire à ce qui suit ; cela indique que votre machine virtuelle hôte Docker est en service et attend vos commandes.

![](./media/virtual-machines-docker/connectingtodockerhost.png)

### Authentification de la machine virtuelle hôte Docker
Outre la création de la machine virtuelle Docker, la commande `azure vm docker create` crée automatiquement les certificats nécessaires pour autoriser votre ordinateur client Docker à se connecter à l'hôte de conteneur Azure à l'aide du protocole HTTPS. Les certificats sont stockés sur les ordinateurs clients et hôtes, suivant le cas. Lors des exécutions suivantes, les certificats existants sont réutilisés et partagés avec le nouvel hôte.

Par défaut, les certificats sont placés dans `~/.docker`, et Docker est configuré pour s'exécuter sur le port **4243**. Si vous souhaitez utiliser un autre port ou répertoire, vous pouvez utiliser l'une des options de ligne de commande `azure vm docker create` suivantes afin de configurer votre machine virtuelle hôte de conteneur Docker, de telle sorte qu'elle utilise un port ou des certificats différents pour la connexion des clients :

```
-dp, --docker-port [port]              Port to use for docker [4243]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Le démon Docker sur l'hôte est configuré pour écouter et authentifier les connexions client sur le port spécifié à l'aide des certificats générés par la commande `azure vm docker create`. L'ordinateur client doit posséder ces certificats pour avoir accès à l'hôte Docker. 

> [AZURE.NOTE] Un hôte en réseau qui s'exécute sans ces certificats est à la merci de toute personne capable de se connecter à l'ordinateur. Avant de modifier la configuration par défaut, vous devez bien comprendre les risques auxquels sont exposés vos ordinateurs et applications.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Vous êtes prêt à consulter le [DGuide d'utilisation Docker] et à utiliser votre machine virtuelle Docker. Pour créer une machine virtuelle Docker dans le nouveau portail, consultez la page [Utilisation de l'extension Docker VM avec le portail].

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Étapes suivantes]: #next-steps

[Utilisation de l'extension Docker VM avec Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Extensions de machine virtuelle pour Linux et Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Conteneurs et ressources de gestion de conteneurs pour Azure]: #Container-and-Container-Management-Resources-for-Azure

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial/
[Lien 2 vers une autre rubrique de documentation azure.microsoft.com]: ../web-sites-custom-domain-name/
[Lien 3 vers une autre rubrique de documentation azure.microsoft.com]: ../storage-whatis-account/
[Utilisation de l'extension Docker VM avec le portail]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guide d'utilisation de Docker]: https://docs.docker.com/userguide/




<!--HONumber=42-->
