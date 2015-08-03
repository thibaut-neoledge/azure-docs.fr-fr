<properties
	pageTitle="Utilisation de CoreOS sur Azure"
	description="Décrit CoreOS, la création d'une machine virtuelle CoreOS sur Azure et son utilisation de base."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/16/2015"
	ms.author="rasquill"/>

# Utilisation de CoreOS sur Azure

Cette rubrique décrit [CoreOS] et explique la création d'un cluster de trois machines virtuelles CoreOS sur Azure pour commencer rapidement à comprendre CoreOS. Elle utilise les éléments de base des déploiements CoreOS et des exemples tirés de [CoreOS avec Azure], du [Didacticiel CoreOS de Tim Park] et du [Didacticiel CoreOS de Patrick Chanezon] pour démontrer les conditions minimales permettant de comprendre la structure de base d'un déploiement CoreOS et d'obtenir un cluster de trois machines virtuelles fonctionnant sans problème.

## <a id='intro'>CoreOS, clusters et conteneurs Linux</a>

CoreOS est une version légère de Linux conçue pour créer rapidement des clusters potentiellement volumineux de machines virtuelles qui utilisent des conteneurs Linux comme seul mécanisme d'empaquetage, y compris les conteneurs [Docker]. CoreOS assure :

+ une automatisation très poussée ;
+ le déploiement facile et cohérent d'applications ;
+ l'évolutivité aux niveaux application et système.

À haut niveau, les fonctionnalités CoreOS qui prennent en charge ces caractéristiques sont :

1. Système en un seul package : CoreOS exécute uniquement des images de conteneurs Linux qui s’exécutent dans des conteneurs Linux pour améliorer la vitesse, la cohérence et la facilité de déploiement.
2. Mises à jour des systèmes d'exploitation effectuées atomiquement de façon à ce qu'ils soient mis à jour comme une seule entité et à pouvoir les rétablir facilement dans un état connu.
3. Démons (services) intégrés [etcd](https://github.com/coreos/etcd) et [fleet](https://github.com/coreos/fleet) pour les communications et la gestion dynamiques des machines virtuelles et des clusters.

Il s'agit d'une description très générale de CoreOS et de ses fonctionnalités. Pour plus d'informations sur CoreOS, consultez la [Présentation de CoreOS].

## <a id='security'>Considérations relatives à la sécurité</a>
Actuellement, CoreOS suppose que les utilisateurs qui peuvent utiliser SSH pour se connecter au cluster sont autorisés à le gérer. Résultat : sans modification, les clusters CoreOS sont parfaits pour les environnements de test et de développement, mais vous devez appliquer des mesures de sécurité dans les environnements de production.

## <a id='usingcoreos'>Utilisation de CoreOS dans Azure</a>

Cette section décrit la création d’un service cloud Azure comportant trois machines virtuelles CoreOS à l’aide de l’[interface de ligne de commande Azure (Azure CLI)]. Procédure de base :

1. Créez les certificats et les clés SSH afin de sécuriser les communications avec la machine virtuelle CoreOS.
2. Obtenez l'ID etcd de votre cluster pour les intercommunications.
3. Créez un fichier cloud-config au format [YAML].
4. Utilisez l’interface de ligne de commande Azure pour créer un service cloud Azure et trois machines virtuelles CoreOS.
5. Testez votre cluster CoreOS à partir d’une machine virtuelle Azure.
6. Testez votre cluster CoreOS à partir de localhost.

### Création de clés publiques et privées pour les communications

Suivez les instructions fournies dans la rubrique [Utilisation de SSH avec Linux sur Azure](virtual-machines-linux-use-ssh-key.md) pour créer une clé SSH privée et publique. (La procédure de base figure dans les instructions ci-dessous). Vous allez utiliser ces clés pour vous connecter aux machines virtuelles du cluster et vérifier qu'elles fonctionnent et communiquent entre elles.

> [AZURE.NOTE]Cette rubrique part du principe que vous ne disposez pas de ces clés et vous impose de créer les fichiers `myPrivateKey.pem` et `myCert.pem` pour des raisons de clarté. Si vous avez déjà une paire clé publique/clé privée enregistrée dans `~/.ssh/id_rsa`, il vous suffit de taper `openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem` pour obtenir le fichier .pem nécessaire pour le téléchargement vers Azure.

1. Dans un répertoire de travail, tapez `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem` pour créer la clé privée et le certificat X.509 associé.

2. Pour indiquer que le propriétaire de la clé privée peut lire le fichier ou écrire dedans, tapez `chmod 600 myPrivateKey.key`.

Les fichiers `myPrivateKey.key` et `myCert.pem` doivent maintenant se trouver dans votre répertoire de travail.


### Obtention de l'ID etcd de votre cluster

Le démon `etcd` de CoreOS nécessite un ID de découverte pour interroger automatiquement tous les nœuds dans le cluster. Pour récupérer votre ID de découverte et l’enregistrer dans un fichier `etcdid`, tapez

```
curl https://discovery.etcd.io/new | grep ^http.* > etcdid
```

### Création d'un fichier cloud-config

Toujours dans le même répertoire de travail, créez avec l’éditeur de texte de votre choix un fichier contenant le texte ci-dessous et enregistrez-le sous le nom `cloud-config.yaml`. Vous pouvez l'enregistrer sous n'importe quel nom, mais lorsque vous créerez des machines virtuelles à l'étape suivante, vous devrez référencer le nom de ce fichier dans l'option **--custom-data** de la commande **azure create vm**.

> [AZURE.NOTE]N’oubliez pas de taper `cat etcdid` pour obtenir l’ID de découverte etcd dans le fichier `etcdid` créé précédemment. Remplacez `<token>` dans le fichier `cloud-config.yaml` suivant par le nombre généré à partir de votre fichier `etcdid`. Si vous ne pouvez pas finalement valider votre cluster, vous avez peut-être oublié ces étapes !

```
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
```

Pour en savoir plus sur le fichier cloud-config, consultez la rubrique [Utilisation de Cloud-Config](https://coreos.com/docs/cluster-management/setup/cloudinit-cloud-config/) dans la documentation CoreOS.

### Utilisation de l’interface de ligne de commande Azure pour créer une machine virtuelle CoreOS
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

1. Si ce n’est pas déjà fait, installez l’[interface de ligne de commande Azure (Azure CLI)]. Connectez-vous en utilisant un ID scolaire ou professionnel ou téléchargez un fichier .publishsettings et importez-le dans votre compte.
2. Localisez votre image CoreOS. Pour localiser à tout moment les images disponibles, tapez `azure vm image list | grep CoreOS` ; une liste de résultats similaire à celle indiquée ci-dessous s’affiche :

	data: 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 Public Linux

3. Créez un service cloud pour votre cluster de base : tapez `azure service create <cloud-service-name>` où *<cloud-service-name>* est le nom de votre service cloud CoreOS. Cet exemple utilise le nom **`coreos-cluster`** ; vous devrez réutiliser le nom que vous avez choisi pour créer les instances CoreOS de vos machines virtuelles dans le service cloud.

Remarque : si vous examinez votre travail dans le [portail](https://portal.azure.com), vous constaterez que le nom de votre service cloud est à la fois celui d’un groupe de ressources et d’un domaine (voir l’image suivante) :

![][CloudServiceInNewPortal] 4. Connectez-vous à votre service cloud et créez une machine virtuelle CoreOS dans ce service au moyen de la commande **azure vm create**. Vous transmettez l'emplacement de votre certificat X.509 avec l'option **--ssh-cert**. Créez l'image de votre première machine virtuelle : tapez ce qui suit en n'oubliant pas de remplacer **coreos-cluster** par le nom du service cloud que vous avez créé :

```
azure vm create --custom-data=cloud-config.yaml --ssh=22 --ssh-cert=./myCert.pem --no-ssh-password --vm-name=node-1 --connect=coreos-cluster --location="West US" 2b171e93f07c4903bcad35bda10acf22__CoreOS-Stable-522.6.0 core
```

5. Créez le deuxième nœud en répétant la commande de l'étape 4 et en remplaçant la valeur **--vm-name** par **node-2** et la valeur du port **--ssh** par 2022.

6. Créez le troisième nœud en répétant la commande de l'étape 4 et en remplaçant la valeur **--vm-name** par **node-3** et la valeur du port **--ssh** par 3022.

Vous constatez dans la copie d'écran ci-dessous comment le cluster CoreOS apparaît dans le nouveau portail.

![][EmptyCoreOSCluster]

### Test de votre cluster CoreOS à partir d'une machine virtuelle Azure

Pour tester votre cluster, vérifiez que vous vous trouvez dans votre répertoire de travail. Connectez-vous ensuite à **node-1** en utilisant **ssh** et en tapant ce qui suit pour transmettre la clé privée :

	ssh core@coreos-cluster.cloudapp.net -p 22 -i ./myPrivateKey.key

Lorsque vous êtes connecté, tapez `sudo fleetctl list-machines` pour savoir si le cluster a déjà identifié toutes les machines virtuelles qui s’y trouvent. Vous devez recevoir une réponse ayant l'aspect suivant :


	core@node-1 ~ $ sudo fleetctl list-machines
	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-


### Test de votre cluster CoreOS à partir de localhost

Enfin, installez **fleet** pour tester votre cluster CoreOS à partir de votre client local Linux. **fleet** nécessite **golang** ; vous devrez donc peut-être l'installer d'abord en tapant la commande suivante :

`sudo apt-get install golang`

Clonez ensuite le référentiel **fleet** à partir de github. Tapez :

`git clone https://github.com/coreos/fleet.git`

Générez **fleet** en passant au répertoire `fleet` et en tapant

`./build`

Placez enfin **fleet** pour faciliter l'utilisation (en fonction de votre configuration, vous devrez peut-être exécuter la commande **sudo**) :

`cp bin/fleetctl /usr/local/bin`

Vérifiez que **fleet** dispose de l’accès à votre `myPrivateKey.key` dans le répertoire de travail en tapant :

`ssh-add ./myPrivateKey.key`

> [AZURE.NOTE]Si vous utilisez déjà la clé **`~/.ssh/id_rsa`**, ajoutez-la avec `ssh-add ~/.ssh/id_rsa`.

Vous êtes maintenant prêt à effectuer un test à distance en utilisant la même commande **fleetctl** que vous avez utilisée sur **node-1**, mais en transmettant certains arguments de connexion à distance :

`fleetctl --tunnel coreos-cluster.cloudapp.net:22 list-machines`

Les résultats doivent être exactement identiques :


	MACHINE		IP		METADATA
	442e6cfb...	100.71.168.115	-
	a05e2d7c...	100.71.168.87	-
	f7de6717...	100.71.188.96	-

## Étapes suivantes

Vous devez avoir un cluster CoreOS exécutant trois nœuds sur Azure. À ce stade, vous pouvez découvrir comment créer des clusters plus complexes et utiliser Docker et comment créer des applications plus intéressantes. Pour essayer quelques exemples rapides, consultez la page [Prise en main de fleet avec CoreOS sur Azure].

<!--Anchors-->
[CoreOS, Clusters, and Linux Containers]: #intro
[Security Considerations]: #security
[How to use CoreOS on Azure]: #usingcoreos
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

<!--Image references-->
[CloudServiceInNewPortal]: ./media/virtual-machines-linux-coreos-how-to/cloudservicefromnewportal.png
[EmptyCoreOSCluster]: ./media/virtual-machines-linux-coreos-how-to/endresultemptycluster.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[interface de ligne de commande Azure (Azure CLI)]: ../xplat-cli.md
[CoreOS]: https://coreos.com/
[Présentation de CoreOS]: https://coreos.com/using-coreos/
[CoreOS avec Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Didacticiel CoreOS de Tim Park]: https://github.com/timfpark/coreos-azure
[Didacticiel CoreOS de Patrick Chanezon]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Prise en main de fleet avec CoreOS sur Azure]: virtual-machines-linux-coreos-fleet-get-started.md
 

<!---HONumber=July15_HO4-->