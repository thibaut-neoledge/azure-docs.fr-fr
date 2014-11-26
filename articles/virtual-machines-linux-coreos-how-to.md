<properties title="Utilisation de&nbsp;CoreOS sur&nbsp;Azure" pageTitle="Utilisation de&nbsp;CoreOS sur&nbsp;Azure" description="D&eacute;crit&nbsp;CoreOS, la cr&eacute;ation d'une machine virtuelle&nbsp;CoreOS sur&nbsp;Azure et son utilisation de base." metaKeywords="linux, virtual machines, vm, azure, CoreOS, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/27/2014" ms.author="rasquill" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->
<!--Properties section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20properties%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20values.aspx for details. -->
<!-- Tags section (above): this is required in all topics. Please fill it out! See http://sharepoint/sites/azurecontentguidance/wiki/Pages/Markdown%20tagging%20-%20add%20a%20tags%20section%20to%20your%20markdown%20file%20to%20specify%20metadata%20for%20reporting.aspx for details. -->
<!--The next line, with one pound sign at the beginning, is the page title-->

# Utilisation de CoreOS sur Microsoft Azure

Cette rubrique décrit [CoreOS][CoreOS] et explique la création d'un cluster de trois machines virtuelles CoreOS sur Azure pour commencer rapidement à comprendre CoreOS. Elle utilise les éléments de base des déploiements CoreOS et des exemples tirés de [CoreOS avec Azure][CoreOS avec Azure], du [Didacticiel CoreOS de Tim Park][Didacticiel CoreOS de Tim Park] et du [Didacticiel CoreOS de Patrick Chanezon][Didacticiel CoreOS de Patrick Chanezon] pour démontrer les conditions minimales permettant de comprendre la structure de base d'un déploiement CoreOS et d'obtenir un cluster de trois machines virtuelles fonctionnant sans problème.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

Cette rubrique contient les sections suivantes :

-   [CoreOS, clusters et conteneurs Linux][CoreOS, clusters et conteneurs Linux]
-   [Sécurité][Sécurité]
-   [Utilisation de CoreOS sur Azure][Utilisation de CoreOS sur Azure]
-   [Étapes suivantes][Étapes suivantes]

## <span id="intro"></span>CoreOS, clusters et conteneurs Linux</a>

CoreOS est une version légère de Linux conçue pour créer rapidement des clusters potentiellement volumineux de machines virtuelles qui utilisent des conteneurs Linux comme seul mécanisme d'empaquetage, y compris les conteneurs [Docker][Docker]. CoreOS assure :

-   une automatisation très poussée ;
-   le déploiement facile et cohérent d'applications ;
-   l'évolutivité aux niveaux application et système.

À haut niveau, les fonctionnalités CoreOS qui prennent en charge ces caractéristiques sont :

1.  Système en un seul package : CoreOS exécute uniquement des images de conteneurs Linux qui s'exécutent dans des conteneurs Linux pour améliorer la vitesse, la cohérence et la facilité de déploiement.
2.  Mises à jour des systèmes d'exploitation effectuées atomiquement de façon à ce qu'ils soient mis à jour comme une seule entité et à pouvoir les rétablir facilement dans un état connu.
3.  Démons (services) intégrés [etcd][etcd] et [fleet][fleet] pour les communications et la gestion dynamiques des machines virtuelles et des clusters.

Il s'agit d'une description très générale de CoreOS et de ses fonctionnalités. Pour plus d'informations sur CoreOS, consultez la [Présentation de CoreOS][Présentation de CoreOS].

## <span id="security"></span>Sécurité</a>

Actuellement, CoreOS suppose que les utilisateurs qui peuvent utiliser SSH pour se connecter au cluster sont autorisés à le gérer. Résultat : sans modification, les clusters CoreOS sont parfaits pour les environnements de test et de développement, mais vous devez appliquer des mesures de sécurité dans les environnements de production.

## <span id="usingcoreos"></span>Utilisation de CoreOS sur Azure</a>

Cette section décrit la création d'un service cloud Azure comportant trois machines virtuelles CoreOS en utilisant l'interface [xplat-cli][xplat-cli]. Procédure de base

1.  Créez les certificats et les clés SSH afin de sécuriser les communications avec la machine virtuelle CoreOS.
2.  Obtenez l'ID etcd de votre cluster pour les intercommunications.
3.  Créez un fichier cloud-config au format [YAML][YAML].
4.  Utilisez l'interface xplat-cli pour créer un service cloud Azure et trois machines virtuelles CoreOS.
5.  Testez votre cluster CoreOS à partir d'une machine virtuelle Azure.
6.  Testez votre cluster CoreOS à partir de localhost.

### Création de clés publiques et privées pour les communications

Suivez les instructions fournies dans la rubrique [Utilisation de SSH avec Linux sur Azure][Utilisation de SSH avec Linux sur Azure] pour créer une clé SSH privée et publique. (La procédure de base figure dans les instructions ci-dessous). Vous allez utiliser ces clés pour vous connecter aux machines virtuelles du cluster et vérifier qu'elles fonctionnent et communiquent entre elles.

> [WACOM.NOTE] Cette rubrique suppose que vous ne disposez pas de ces clés et vous impose de créer les fichiers **`myPrivateKey.pem`** et **`myCert.pem`** pour des raisons de clarté. Si vous avez déjà une paire clé publique/clé privée enregistrée dans **`~/.ssh/id_rsa`**, il vous suffit de taper `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` pour obtenir le fichier .pem nécessaire pour le téléchargement vers Azure.

1.  Dans un répertoire de travail, tapez `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` pour créer la clé privée et le certificat X.509 associé.

2.  Pour indiquer que le propriétaire de la clé privée peut lire ou écrire le fichier, tapez `chmod 600 myPrivateKey.key`.

Les fichiers **`myPrivateKey.key`** et **`myCert.pem`** doivent alors se trouver dans votre répertoire de travail.

### Obtention de l'ID etcd de votre cluster

Le démon **etcd** de CoreOS nécessite un ID de découverte pour interroger automatiquement tous les nœuds dans le cluster. Pour récupérer votre ID de découverte et l'enregistrer dans un fichier **etcdid**, tapez la commande suivante :

    curl https://discovery.etcd.io/new | grep ^http.* > etcdid

### Création d'un fichier cloud-config

Toujours dans le même répertoire de travail, créez avec l'éditeur de texte de votre choix un fichier contenant le texte ci-dessous et enregistrez-le sous le nom **`cloud-config.yaml`**. Vous pouvez l'enregistrer sous n'importe quel nom, mais lorsque vous créerez des machines virtuelles à l'étape suivante, vous devrez référencer le nom de ce fichier dans l'option **--custom-data** de la commande **azure create vm**.

> [WACOM.NOTE] N'oubliez pas de taper `cat etcdid` pour obtenir l'ID de découverte dans le fichier `etcdid` créé précédemment. Remplacez **`<token>`** dans le fichier **cloud-config.yaml** par le nombre généré à partir de votre fichier `etcdid`. Si vous ne pouvez pas finalement valider votre cluster, vous avez peut-être oublié ces étapes !

    #cloud-config

    coreos:
      etcd:
        # generate a new token for each unique cluster from https://discovery.etcd.io/new
        discovery: https://discovery.etcd.io/<token>
        # deployments across multiple cloud services will need to use $public_ipv4
        addr: $private_ipv4:4001
        peer-addr: $private_ipv4:7001
      units:
        - name: etcd.service
          command: start
        - name: fleet.service
          command: start

Pour en savoir plus sur le fichier cloud-config, consultez la rubrique [Utilisation de Cloud-Config][Utilisation de Cloud-Config] dans la documentation CoreOS.

### Utilisation de l'interface xplat-cli pour créer une machine virtuelle CoreOS

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1.  Installez l'interface [xplat-cli][xplat-cli] si ce n'est pas déjà fait et connectez-vous en utilisant un ID scolaire ou professionnel, ou téléchargez un fichier .publishsettings et importez-le dans votre compte.
2.  Localisez votre image CoreOS. Il n'existe actuellement qu'une seule image (**`2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0`**), mais pour localiser à tout moment les images disponibles, tapez `azure vm image list | grep .*CoreOS.*` pour afficher un ou plusieurs résultats similaires à ceci :
    `data:    2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0              Public    Linux`
3.  Créez un service cloud pour votre cluster de base : tapez 
    `azure service create <cloud-service-name>` où *cloud-service-name* est le nom de votre service cloud CoreOS. Cet exemple utilise le nom **`coreos-cluster`** ; vous devrez réutiliser le nom que vous avez choisi pour créer les instances CoreOS de vos machines virtuelles dans le service cloud.

Remarque : si vous examinez votre travail jusqu'à présent dans le [nouveau portail][nouveau portail], vous constatez que le nom de votre service cloud est à la fois celui d'un groupe de ressources et d'un domaine (voir l'image suivante) :

![][0]

1.  Connectez-vous à votre service cloud et créez une machine virtuelle CoreOS dans ce service au moyen de la commande **azure vm create**. Vous transmettez l'emplacement de votre certificat X.509 avec l'option **--ssh-cert**. Créez l'image de votre première machine virtuelle : tapez ce qui suit en n'oubliant pas de remplacer **coreos-cluster** par le nom du service cloud que vous avez créé :

<!-- -->

    azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location='West US' 2b171e93f07c4903bcad35bda10acf22__CoreOS-Alpha-475.1.0 core

1.  Créez le deuxième nœud en répétant la commande de l'étape 4 et en remplaçant la valeur **--vm-name** par **node-2** et la valeur du port **--ssh** par 2022.

2.  Créez le troisième nœud en répétant la commande de l'étape 4 et en remplaçant la valeur **--vm-name** par **node-3** et la valeur du port **--ssh** par 3022.

Vous constatez dans la copie d'écran ci-dessous comment le cluster CoreOS apparaît dans le nouveau portail.

![][1]

### Test de votre cluster CoreOS à partir d'une machine virtuelle Azure

Pour tester votre cluster, vérifiez que vous vous trouvez dans votre répertoire de travail. Connectez-vous ensuite à **node-1** en utilisant **ssh** et en tapant ce qui suit pour transmettre la clé privée :

`ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key`

Lorsque vous êtes connecté, tapez `sudo fleetctl list-machines` pour savoir si le cluster a déjà identifié toutes les machines virtuelles dans le cluster. Vous devez recevoir une réponse ayant l'aspect suivant :

    core@node-1 ~ $ sudo fleetctl list-machines
    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

### Test de votre cluster CoreOS à partir de localhost

Enfin, installez **fleet** pour tester votre cluster CoreOS à partir de votre client local Linux. **fleet** nécessite **golang** ; vous devrez donc peut-être l'installer d'abord en tapant la commande suivante :

`sudo apt-get install golang`

Clonez ensuite le référentiel **fleet** à partir de github. Tapez :

`git clone https://github.com/coreos/fleet.git`

Compilez **fleet** en tapant

`./build`

Placez enfin **fleet** pour faciliter l'utilisation (en fonction de votre configuration, vous devrez peut-être exécuter la commande **sudo**) :

`cp bin/fleetctl /usr/local/bin`

Vérifiez que **fleet** dispose de l'accès à votre `myPrivateKey.key` dans le répertoire de travail. Tapez :

`ssh-add ./myPrivateKey.key`

> [WACOM.NOTE] Si vous utilisez déjà la clé **~/.ss h/id\_rsa**, ajoutez-la avec `ssh-add ~/.ssh/id_rsa`.

Vous êtes maintenant prêt à effectuer un test à distance en utilisant la même commande **fleetctl** que vous avez utilisée sur **node-1**, mais en transmettant certains arguments de connexion à distance :

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Les résultats doivent être exactement identiques :

    MACHINE     IP      METADATA
    442e6cfb... 100.71.168.115  -
    a05e2d7c... 100.71.168.87   -
    f7de6717... 100.71.188.96   -

## Étapes suivantes

Vous devez avoir un cluster CoreOS exécutant trois nœuds sur Azure. Dès lors, vous pouvez apprendre à créer des clusters plus complexes et à créer d'autres applications intéressantes en lisant le [Didacticiel CoreOS de Tim Park][Didacticiel CoreOS de Tim Park], le [Didacticiel CoreOS de Patrick Chanezon][Didacticiel CoreOS de Patrick Chanezon], la documentation [Docker][Docker] et la [Présentation de CoreOS][Présentation de CoreOS].

<!--Anchors-->
<!--Image references-->
<!--Link references-->

  [CoreOS]: https://coreos.com/
  [CoreOS avec Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
  [Didacticiel CoreOS de Tim Park]: https://github.com/timfpark/coreos-azure
  [Didacticiel CoreOS de Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
  [CoreOS, clusters et conteneurs Linux]: #intro
  [Sécurité]: #security
  [Utilisation de CoreOS sur Azure]: #usingcoreos
  [Étapes suivantes]: #next-steps
  [Docker]: http://docker.io
  [etcd]: https://github.com/coreos/etcd
  [fleet]: https://github.com/coreos/fleet
  [Présentation de CoreOS]: https://coreos.com/using-coreos/
  [xplat-cli]: ../xplat-cli/
  [YAML]: http://yaml.org/
  [Utilisation de SSH avec Linux sur Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-linux-use-ssh-key/
  [Utilisation de Cloud-Config]: https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/
  [nouveau portail]: https://portal.azure.com
  [0]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
  [1]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
