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
<!--The next line, with one pound sign at the beginning, is the page title--> 
# Utilisation de l'extension Docker VM avec le portail Azure

[Docker](https://www.docker.com/) fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux](http://en.wikipedia.org/wiki/LXC) plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Vous pouvez utiliser l'extension Docker VM sur [l'agent Linux Azure] afin de créer une machine virtuelle Docker hébergeant un nombre indéfini de conteneurs pour vos applications sur Azure. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->
Dans cette section

+ [Création d'une machine virtuelle à partir de la galerie d'images]
+ [Création de certificats Docker]
+ [Ajout de l'extension Docker VM]
+ [Test du client Docker et de l'hôte Azure Docker]
+ [Étapes suivantes]

> [AZURE.NOTE] Cette rubrique décrit la création d'une machine virtuelle Docker VM dans le portail Azure. Pour savoir comment créer une machine virtuelle Docker dans la ligne de commande, consultez la page [Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)]. Pour une discussion sur les conteneurs et leurs avantages, consultez le [Tableau blanc Docker de haut niveau](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard). 

## <a id='createvm'>Création d'une machine virtuelle à partir de la galerie d'images</a>
La première étape nécessite une machine virtuelle Azure à partir d'une image Linux qui prend en charge l'extension Docker VM, en utilisant une image Ubuntu 14.04 LTS de la galerie d'images comme exemple d'image de serveur et Ubuntu 14.04 Desktop comme client. Dans le portail, cliquez sur **+ Nouveau** dans le coin inférieur gauche pour créer une instance de machine virtuelle, puis sélectionnez une image Ubuntu 14.04 LTS parmi les choix proposés ou la galerie d'images complète (voir ci-dessous). 

> [AZURE.NOTE] Actuellement, seules les images Ubuntu 14.04 LTS postérieures à juillet 2014 prennent en charge l'extension Docker VM.

![Create a new Ubuntu Image](./media/virtual-machines-docker-with-portal/ChooseUbuntu.png)

## <a id'dockercerts'>Création de certificats Docker</a>

Après la création de la machine virtuelle, vérifiez que Docker est installé sur votre ordinateur client (pour plus d'informations, voir [Instructions d'installation de Docker](https://docs.docker.com/installation/#installation).) 

Créez le certificat et les fichiers de clés pour les communications Docker en respectant les instructions fournies à la page [Exécution de Docker avec https] ; placez-les ensuite dans le répertoire **`~/.docker`** de votre ordinateur client. 

> [AZURE.NOTE] L'extension Docker VM dans le portail nécessite actuellement des informations d'identification codées en base64.

Dans la ligne de commande, utilisez **`base64`** ou un autre outil d'encodage de votre choix pour créer des rubriques codées en base64. Avec un jeu simple de certificats et de fichiers de clés, le code peut être similaire au code suivant :

```
 ~/.docker$ l
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ l
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a id'adddockerextension'>Ajout de l'extension Docker VM</a>
Pour ajouter l'extension Docker VM, localisez l'instance de machine virtuelle que vous avez créée, accédez à **Extensions**, puis cliquez pour afficher Extensions de machine virtuelle (voir ci-dessous).

![](./media/virtual-machines-docker-with-portal/ClickExtensions.png)
### Ajout d'une extension
Cliquez sur **+ Ajouter** pour afficher les extensions de machine virtuelle que vous pouvez ajouter à cette machine virtuelle. 

![](./media/virtual-machines-docker-with-portal/ClickAdd.png)
### Sélection de l'extension Docker VM
Choisissez l'extension Docker VM : elle affiche la description de Docker et des liens importants. Cliquez ensuite sur **Créer** au bas de l'écran pour commencer l'installation.

![](./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png)

![](./media/virtual-machines-docker-with-portal/CreateButtonFocus.png)
### Ajout de vos certificats et de vos fichiers de clés :

Dans les champs du formulaire, entrez les versions codées en base64 de votre certificat CA, le certificat et la clé de votre serveur (voir l'illustration ci-dessous).

![](./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png)

> [AZURE.NOTE] Notez que (comme dans l'illustration précédente), 4243 est indiqué par défaut. Vous pouvez entrer ici n'importe quel point de terminaison, mais l'étape suivante consiste à ouvrir le point de terminaison correspondant. Si vous modifiez le point de terminaison par défaut, n'oubliez pas d'ouvrir le point de terminaison correct à l'étape suivante.

## Ajout du point de terminaison des communications Docker VM
Lorsque vous affichez votre machine virtuelle dans le groupe de ressources que vous avez créé, faites défiler l'écran vers le bas et cliquez sur **Point de terminaison** pour afficher les points de terminaison de la machine virtuelle (voir l'illustration).

![](./media/virtual-machines-docker-with-portal/AddingEndpoint.png)

Cliquez sur **+ Ajouter** pour ajouter un point de terminaison ; dans le cas par défaut, entrez un nom pour le point de terminaison (dans cet exemple **docker**) et 4243 pour les ports privés et publics. Laissez le protocole **TCP** et cliquez sur **OK** pour créer le point de terminaison.

![](./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png)


## <a id='testclientandserver'>Test du client Docker et de l'hôte Azure Docker</a>
Localisez et copiez le nom de domaine de votre machine virtuelle. Dans la ligne de commande de votre ordinateur client, tapez `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info` (où *dockerextension* est remplacé par le sous-domaine de votre machine virtuelle). 

Le résultat affiché doit avoir l'aspect suivant :

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:4243 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Vous êtes prêt à consulter le [Guide d'utilisation Docker] et à utiliser votre machine virtuelle Docker. Si vous voulez commencer par créer rapidement et de manière répétée des machines virtuelles Docker sur Azure, consultez la rubrique [Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)]

<!--Anchors-->
[Création d'une machine virtuelle à partir de la galerie d'images]: #createvm
[Création de certificats Docker]: #dockercerts
[Ajout de l'extension Docker VM]: #adddockerextension
[Test du client Docker et de l'hôte Azure Docker]: #testclientandserver
[Étapes suivantes]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Utilisation de l'extension Docker VM à partir de l'interface interplateforme Azure (xplat-cli)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[l'agent Linux Azure]: ../virtual-machines-linux-agent-user-guide/
[Lien 3 vers une autre rubrique de documentation azure.microsoft.com]: ../storage-whatis-account/

[Exécution de Docker avec https]: http://docs.docker.com/articles/https/
[Guide d'utilisation Docker]: https://docs.docker.com/userguide/



<!--HONumber=42-->
