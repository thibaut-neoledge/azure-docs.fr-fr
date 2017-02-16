---
title: "Interaction avec les clusters Service Fabric à l’aide de l’interface de ligne de commande | Microsoft Docs"
description: "Comment utiliser l’interface de ligne de commande Azure pour interagir avec un cluster Service Fabric"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: c3ec8ff3-3b78-420c-a7ea-0c5e443fb10e
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2016
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 615e7ea84aae45f384edb671a28e4ff98b4ade3a
ms.openlocfilehash: d61b7a9c8199b15c8bb24e7146ea93a2f67fb0a7


---
# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>À l’aide de l’interface de ligne de commande Azure pour interagir avec un cluster Service Fabric
Vous pouvez interagir avec le cluster Service Fabric à partir d’ordinateurs Linux à l’aide de l’interface de ligne de commande Azure sous Linux.

La première étape consiste à obtenir la dernière version de l’interface de ligne de commande à partir de la représentation git et à l’installer dans votre chemin d’accès à l’aide des commandes suivantes :

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Pour chaque commande prise en charge, vous pouvez taper le nom de la commande pour obtenir de l’aide de cette commande. La saisie semi-automatique est prise en charge pour les commandes. Par exemple, la commande suivante vous aide pour toutes les commandes de l’application. 

```sh
 azure servicefabric application 
```

Vous pouvez filtrer davantage l’aide pour une commande spécifique, comme le montre l’exemple suivant :

```sh
 azure servicefabric application  create
```

Pour activer la saisie semi-automatique dans l’interface de ligne de commande, exécutez les commandes suivantes :

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Les commandes suivantes se connectent au cluster et vous montrent les nœuds du cluster :

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Pour utiliser des paramètres nommés et trouver à quoi ils correspondent, vous pouvez taper --aide après une commande. Par exemple :

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Lors de la connexion à un cluster de plusieurs ordinateurs à partir d’un ordinateur **qui ne fait pas partie du cluster**, utilisez la commande suivante :

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Remplacez la balise PublicIPorFQDN avec l’IP réelle ou le nom de domaine complet comme il convient. Lors de la connexion à un cluster de plusieurs ordinateurs à partir d’un ordinateur **qui fait partie du cluster**, utilisez la commande suivante :

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Vous pouvez utiliser PowerShell ou l’interface de ligne de commende pour interagir avec votre cluster Service Fabric Linux créé via le portail Azure. 

> [!WARNING]
> Ces clusters ne sont pas sécurisés, par conséquent, vous pouvez ouvrir votre boîtier unique en ajoutant l’adresse IP publique dans le manifeste de cluster.

## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>À l’aide de l’interface de ligne de commande Azure pour se connecter à un cluster Service Fabric
Les commandes de l’interface de ligne de commande Azure ci-après expliquent comment se connecter à un cluster sécurisé. Les détails du certificat doivent correspondre à un certificat sur les nœuds du cluster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

Si votre certificat est associé à des autorités de certification, vous devez ajouter le paramètre --ca-cert-path comme indiqué dans l’exemple suivant : 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Si vous saisissez plusieurs autorités de certification, utilisez des virgules comme délimiteurs.

Si le nom commun du certificat ne correspond pas au point de terminaison de connexion, vous pouvez utiliser le paramètre `--strict-ssl-false` pour ignorer la vérification, comme indiqué dans la commande suivante : 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false 
```

Si vous souhaitez ignorer l’étape de vérification de l’autorité de certification, vous pouvez ajouter le paramètre --reject-unauthorized-false, comme indiqué dans la commande suivante : 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

Une fois que vous vous êtes connecté, vous devez être en mesure d’exécuter d’autres commandes d’interface de ligne de commande pour interagir avec le cluster. 

## <a name="deploying-your-service-fabric-application"></a>Déploiement de votre application Service Fabric
Exécutez les commandes suivantes pour copier, inscrire et démarrer l’application Service Fabric :

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Mettre à niveau votre application
Le processus est similaire au [processus dans Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Construisez, copiez, enregistrez et créez votre application à partir du répertoire racine du projet. Si votre instance d’application s’appelle fabric:/MySFApp et que le type est MySFApp, les commandes se présentent comme suit :

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Modifiez votre application et reconstruisez le service modifié.  Mettez à jour le fichier manifeste du service modifié (ServiceManifest.xml) avec les versions mises à jour du Service (Code, Config ou Données comme il convient). Modifiez également le manifeste de l’application (ApplicationManifest.xml) avec le numéro de version mis à jour de l’application et le service modifié.  Copiez et enregistrez votre application mise à jour en utilisant les commandes suivantes :

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Vous pouvez désormais démarrer la mise à niveau de l’application avec la commande suivante :

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Vous pouvez contrôler la mise à niveau de l’application à l’aide de SFX. Dans quelques minutes, l’application aura été mise à jour.  Vous pouvez également essayer une application mise à jour avec une erreur et vérifiez la fonctionnalité de restauration automatique dans Service Fabric.

## <a name="converting-from-pfx-to-pem-and-vice-versa"></a>Conversion de PFX vers PEM et vice versa

Vous devrez peut-être installer un certificat sur votre ordinateur local (avec Windows ou Linux) pour accéder aux clusters sécurisés dans un environnement différent. Par exemple, lorsque vous accédez à un cluster Linux sécurisé à partir d’un ordinateur Windows et vice versa, vous devrez convertir votre certificat du format PFX vers PEM et vice versa. 

Pour convertir un fichier PEM en fichier PFX, utilisez la commande suivante :

```bash
openssl pkcs12 -export -out certificate.pfx -inkey mycert.pem -in mycert.pem -certfile mycert.pem
```

Pour convertir un fichier PFX en fichier PEM, utilisez la commande suivante :

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

Reportez-vous à la [documentation OpenSSL](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html) pour plus d’informations.

<a id="troubleshooting"></a>
## <a name="troubleshooting"></a>Résolution de problèmes
### <a name="copying-of-the-application-package-does-not-succeed"></a>Échec de la copie du package d’application
Vérifiez si `openssh` est installé. Par défaut, cet élément n’est pas installé sur le bureau Ubuntu. Installez-le en utilisant la commande suivante :

```
 sudo apt-get install openssh-server openssh-client**
```

Si le problème persiste, essayez de désactiver PAM pour ssh en modifiant le fichier **sshd_config** à l’aide des commandes suivantes :

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Si le problème persiste, essayez d’augmenter le nombre de sessions ssh en exécutant les commandes suivantes :

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
L’utilisation des clés pour l’authentification ssh (par opposition aux mots de passe) n’étant pas encore prise en charge (puisque la plate-forme utilise ssh pour copier les packages), utilisez plutôt l’authentification par mot de passe.



## <a name="next-steps"></a>Étapes suivantes
Configurez l’environnement de développement et déployez une application Service Fabric vers un cluster Linux.




<!--HONumber=Jan17_HO1-->


