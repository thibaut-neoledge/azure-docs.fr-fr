---
title: "Utilisation de l’extension Docker VM pour Linux | Microsoft Docs"
description: "Décrit Docker et les extensions Azure Virtual Machines, et explique comment créer des machines virtuelles Azure en tant qu’hôtes Docker à l’aide de l’interface de ligne de commande Azure dans le modèle de déploiement classique."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: a1742808be5df2a57e53bb6918f89df4953d0694


---
# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>Utilisation de l’extension Docker VM avec le portail Azure Classic
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

[Docker](https://www.docker.com/) fait partie des méthodes de virtualisation les plus prisées. Cet outil utilise des [conteneurs Linux](http://en.wikipedia.org/wiki/LXC) plutôt que des machines virtuelles pour isoler les données et le traitement sur des ressources partagées. Vous pouvez utiliser l’extension Docker VM sur l’[agent Linux Azure] afin de créer une machine virtuelle Docker hébergeant un nombre indéfini de conteneurs pour vos applications sur Azure.

> [!NOTE]
> Cette rubrique décrit comment créer une machine virtuelle Docker à partir du portail Azure Classic. Pour savoir comment créer une machine virtuelle Docker dans la ligne de commande, consultez la page [Utilisation de l’extension Docker VM à partir de l’interface interplateforme Azure (xplat-cli)]. Pour une discussion sur les conteneurs et leurs avantages, consultez le [Tableau blanc Docker de haut niveau](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Création d'une machine virtuelle à partir de la galerie d'images
La première étape nécessite une machine virtuelle Azure à partir d'une image Linux qui prend en charge l'extension Docker VM, en utilisant une image Ubuntu 14.04 LTS de la galerie d'images comme exemple d'image de serveur et Ubuntu 14.04 Desktop comme client. Dans le portail, cliquez sur **+ Nouveau** dans le coin inférieur gauche pour créer une instance de machine virtuelle, puis sélectionnez une image Ubuntu 14.04 LTS parmi les choix proposés ou la galerie d'images complète (voir ci-dessous).

> [!NOTE]
> Actuellement, seules les images Ubuntu 14.04 LTS postérieures à juillet 2014 prennent en charge l’extension Docker VM.
> 
> 

![Create a new Ubuntu Image](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Création de certificats Docker
Après la création de la machine virtuelle, vérifiez que Docker est installé sur votre ordinateur client (pour plus d'informations, voir [Instructions d'installation de Docker](https://docs.docker.com/installation/#installation).)

Créez le certificat et les fichiers de clés pour les communications Docker en respectant les instructions fournies à la page [Exécution de Docker avec https] ; placez-les ensuite dans le répertoire **`~/.docker`** de votre ordinateur client.

> [!NOTE]
> L’extension Docker VM dans le portail nécessite actuellement des informations d’identification codées en base64.
> 
> 

Dans la ligne de commande, utilisez **`base64`** ou un autre outil d’encodage de votre choix pour créer des rubriques codées en base64. Avec un jeu simple de certificats et de fichiers de clés, le code peut être similaire au code suivant :

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Ajout de l'extension Docker VM
Pour ajouter l'extension Docker VM, localisez l'instance de machine virtuelle que vous avez créée, accédez à **Extensions** , puis cliquez pour afficher Extensions de machine virtuelle (voir ci-dessous).

> [!NOTE]
> Cette fonctionnalité est prise en charge dans la version préliminaire du portail uniquement : https://portal.azure.com/
> 
> 

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Ajout d’une extension
Cliquez sur **+ Ajouter** pour afficher les extensions de machine virtuelle que vous pouvez ajouter à cette machine virtuelle.

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Sélection de l’extension Docker VM
Choisissez l'extension Docker VM : elle affiche la description de Docker et des liens importants. Cliquez ensuite sur **Créer** au bas de l'écran pour commencer l'installation.

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Ajout de vos certificats et de vos fichiers de clés :
Dans les champs du formulaire, entrez les versions codées en base64 de votre certificat CA, le certificat et la clé de votre serveur (voir l’illustration ci-dessous).

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Notez que (comme dans l’illustration précédente), 2376 est indiqué par défaut. Vous pouvez entrer ici n’importe quel point de terminaison, mais l’étape suivante consiste à ouvrir le point de terminaison correspondant. Si vous modifiez le point de terminaison par défaut, n'oubliez pas d'ouvrir le point de terminaison correct à l'étape suivante.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Ajout du point de terminaison des communications Docker VM
Lorsque vous affichez le groupe de ressources que vous avez créé, sélectionnez le groupe de sécurité réseau associé à votre machine virtuelle, puis cliquez sur **Règles de sécurité entrantes** pour afficher les règles comme indiqué ici.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

Cliquez sur **+ Ajouter** pour ajouter une règle ; dans le cas par défaut, entrez un nom pour le point de terminaison (dans cet exemple **Docker**) et 2376 « Étendue du port de destination ». Définissez la valeur du protocole sur **TCP** et cliquez sur **OK** pour créer la règle.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Tester le client Docker et l’hôte Azure Docker
Localisez et copiez le nom de domaine de votre machine virtuelle. Dans la ligne de commande de votre ordinateur client, tapez `docker --tls -H tcp://`*dockerextension*`.cloudapp.net:2376 info` (où *dockerextension* est remplacé par le sous-domaine de votre machine virtuelle).

Le résultat affiché doit avoir l’aspect suivant :

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
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

Lorsque vous avez terminé les étapes ci-dessus, vous disposez maintenant d'un hôte Docker parfaitement fonctionnel exécuté sur une machine virtuelle Azure, configuré pour une connexion à distance à partir d'autres clients.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
Vous êtes prêt à consulter le [Guide d'utilisation Docker] et à utiliser votre machine virtuelle Docker. Si vous souhaitez automatiser la création d’hôtes Docker sur des machines virtuelles Azure via l’interface de ligne de commande, consultez la page [Utilisation de l’extension Docker VM à partir de l’interface interplateforme Azure (xplat-cli)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

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
[Utilisation de l’extension Docker VM à partir de l’interface interplateforme Azure (xplat-cli)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[agent Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[Exécution de Docker avec https]: http://docs.docker.com/articles/https/
[Guide d'utilisation Docker]: https://docs.docker.com/userguide/



<!--HONumber=Dec16_HO1-->


