---
title: "Problèmes connus et guide de dépannage | Microsoft Docs"
description: "Liste des problèmes connus et guide de dépannage"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 8a8d4a9e9246bca5513787e776e91e7e3f2eed68
ms.contentlocale: fr-fr
ms.lasthandoff: 09/28/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - Problèmes connus et guide de dépannage 
Cet article vous permet de rechercher et corriger les erreurs ou défaillances rencontrées dans le cadre de l’utilisation de l’application Azure Machine Learning Workbench. 

> [!IMPORTANT]
> Lors de la communication avec l’équipe de support, il est important de disposer du numéro de build. Pour trouver le numéro de build de l’application, cliquez le menu **Aide**. Cliquez sur le numéro de build pour le copier dans le Presse-papiers. Vous pouvez le coller dans vos e-mails ou sur des forums d’aide pour signaler des problèmes.

![vérifier le numéro de version](media/known-issues-and-troubleshooting-guide/buildno.png)

## <a name="how-to-get-help"></a>Comment obtenir de l'aide
Il existe différentes manières pour obtenir de l’aide.

### <a name="post-to-msdn-forum"></a>Publier sur le forum MSDN
Nous avons un forum MSDN sur leque vous pouvez poster des questions. L’équipe produit surveille le forum activement. L’adresse est la suivante : [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum).

### <a name="gather-diagnostics-information"></a>Collecter des informations de diagnostic
Parfois, nous vous demanderons peut-être de nous envoyer des informations de diagnostic d’une exécution spécifique. Vous pouvez créer un package avec les fichiers appropriés à l’aide de la commande suivante :

```azurecli
# Find out the run id first
$ az ml history list -o table

# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

La commande `az ml experiment diagnostics` génère un fichier `diagnostics.zip` dans le dossier racine du projet. Ce fichier ZIP contient le dossier de projet complet à l’état de sa dernière exécution, ainsi que des informations de journalisation. Veillez à supprimer toutes les informations sensibles que vous ne souhaitez pas inclure avant de nous envoyer le fichier de diagnostics.

### <a name="send-us-a-frown-or-a-smile"></a>Envoyez-nous un smiley mécontent (ou un sourire)

Lorsque vous utilisez Azure ML Workbench, vous pouvez également nous envoyer un smiley mécontent (ou un sourire) en cliquant sur l’émoticône dans l’angle inférieur gauche de l’interpréteur de commandes de l’application. Vous pouvez éventuellement choisir d’inclure votre adresse e-mail (de sorte que nous puissions vous contacter), et/ou une capture d’écran de l’état actuel. 

## <a name="known-service-limits"></a>Limites connues du service
- Taille maximale autorisée pour le dossier du projet : 25 Mo.
    >[!NOTE]
    >Cette limite ne s’applique pas aux dossiers `.git`, `docs` et `outputs`. Ces noms de dossier respectent la casse. Si vous travaillez avec des fichiers volumineux, reportez-vous à [Persistance des modifications et gestion des fichiers volumineux](how-to-read-write-files.md).

- Temps d’exécution maximal autorisé pour les expérimentations : sept jours
- Taille maximale du fichier suivi dans le dossier `outputs` après une exécution : 512 Mo
  - Autrement dit, si votre script génère un fichier de plus de 512 Mo dans le dossier de sortie, ce fichier n’est pas collecté. Si vous travaillez avec des fichiers volumineux, reportez-vous à [Persistance des modifications et gestion des fichiers volumineux](how-to-read-write-files.md).

- Les clés SSH ne sont pas prises en charge lors de la connexion à un ordinateur distant ou un cluster Spark via SSH. Seul le mode nom d’utilisateur/mot de passe est actuellement pris en charge.

- Les transformations de clustering de texte ne sont pas prises en charge sur Mac.

- La bibliothèque RevoScalePy n’est pas prise en charge uniquement sur Windows ou Linux (dans des conteneurs Docker). Pas de prise en charge sur macOS.

## <a name="docker-error-read-connection-refused"></a>Erreur Docker « read: connection refused »
Lors de l’exécution avec un conteneur Docker local, l’erreur suivante est susceptible de s’afficher : 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Vous pouvez résoudre ce problème en modifiant le serveur DNS de Docker à partir de `automatic` sur une valeur fixe de `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Supprimer l’erreur d’exécution de machine virtuelle « no tty present »
Lors de l’exécution avec un conteneur Docker sur une machine Linux distante, vous êtes susceptible de rencontrer le message d’erreur suivant :
```
sudo: no tty present and no askpass program specified.
``` 
Cela peut se produire si vous utilisez le portail Azure pour modifier le mot de passe racine d’une machine virtuelle Ubuntu Linux. 

Azure Machine Learning Workbench requiert un accès sans mot de passe aux Sudoers pour s’exécuter sur des hôtes distants. Pour ce faire, le plus simple consiste à utiliser _visudo_ pour modifier le fichier suivant (vous pouvez créer le fichier s’il n’existe pas) :

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Il est important de modifier le fichier avec _visudo_ et non avec une autre commande. _visudo_ effectue des vérifications syntaxiques automatiques de tous les fichiers de configuration sudo. En cas de fichier Sudoers incorrect syntaxiquement, vous risquez de ne plus pouvoir accéder au sudo.

Insérez la ligne suivante à la fin du fichier :

```
username ALL=(ALL) NOPASSWD:ALL
```

Où _nom d’utilisateur_ est le nom qu’Azure Machine Learning Workbench utilisera pour se connecter à votre hôte distant.

La ligne doit être placée après #includedir "/etc/sudoers.d", sinon elle risque d’être remplacée par une autre règle.

Si vous avez une configuration sudo plus complexe, nous vous conseillons de consulter la documentation sudo pour Ubuntu disponible ici : https://help.ubuntu.com/community/Sudoers

L’erreur ci-dessus peut également se produire si vous n’utilisez pas une VM Linux basée sur Ubuntu dans Azure comme cible d’exécution. Nous prenons en charge uniquement les VM Linux basées sur Ubuntu pour les exécutions à distance. 

## <a name="vm-disk-is-full"></a>Disque de machine virtuelle plein
Par défaut lorsque vous créez une nouvelle VM Linux dans Azure, vous disposez d’un disque de 30 Go pour le système d’exploitation. Le moteur Docker utilise par défaut le même disque pour extraire des images et exécuter des conteneurs. Cela peut saturer le disque du système d’exploitation, et vous voyez une erreur de type « Disque de VM plein » lorsque cela se produit.

Un correctif rapide consiste à supprimer toutes les images Docker que vous n’utilisez plus. C’est précisément ce à quoi sert la commande Docker suivante. (Bien sûr vous devez intégrer un SSH dans la machine virtuelle afin d’exécuter la commande Docker à partir d’un interpréteur de commandes bash.)

```
$ docker system prune -a
```

Vous pouvez également ajouter un disque de données et configurer le moteur Docker pour utiliser le disque de données afin d’y stocker des images. Voici [comment ajouter un disque de données](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk). Vous pouvez ensuite [modifier où Docker stocke les images](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Ou bien, vous pouvez développer le disque du système d’exploitation, sans avoir à modifier la configuration du moteur Docker. Voici [comment étendre le disque du système d’exploitation](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk).

## <a name="sharing-c-drive-on-windows"></a>Partage du lecteur C dans Windows
Si l’exécution se fait dans un conteneur Docker local sur Windows, la définition de `sharedVolumes` sur `true` dans le fichier `docker.compute` sous `aml_config` peut améliorer les performances d’exécution. Toutefois, cela requiert que vous partagiez le lecteur C dans l’_outil Docker pour Windows_. Si vous ne parvenez pas à partager le lecteur C, essayez les conseils suivants :

* Vérifier le partage sur le lecteur C à l’aide de l’Explorateur de fichiers
* Ouvrir les paramètres de carte réseau et désinstaller/réinstaller « Partage de fichiers et d’imprimantes pour les réseaux Microsoft » pour vEthernet
* Ouvrir les paramètres Docker et partager le lecteur C à partir des paramètres Docker
* Les modifications apportées au mot de passe Windows affectent le partage. Ouvrez l’Explorateur de fichiers, repartagez le lecteur C et entrez le nouveau mot de passe.
* Vous pouvez également rencontrer un problème de pare-feu lorsque vous tentez de partager votre lecteur C avec Docker. Ce [post Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) peut s’avérer utile.
* Lorsque vous partagez le lecteur C à l’aide d’informations d’identification de domaine, le partage peut cesser de fonctionner sur les réseaux où le contrôleur de domaine n’est pas accessible (par exemple, réseau domestique, Wi-Fi public, etc.). Pour plus d’informations, consultez [ce post](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Vous pouvez également éviter le problème de partage, contre un léger amoindrissement des performances en définissant `sharedVolumne` sur `false` dans le fichier `docker.compute`.

## <a name="some-useful-docker-commands"></a>Quelques commandes Docker utiles

Voici quelques commandes Docker utiles :

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
