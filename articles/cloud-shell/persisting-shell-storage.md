---
title: "Conserver des fichiers dans Azure Cloud Shell (préversion) | Microsoft Docs"
description: "Procédure pas à pas de conservation des fichiers avec Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 61a8bfcf3704f361432400771d8fcc8b81927b53
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="persist-files-in-azure-cloud-shell"></a>Conserver des fichiers dans Azure Cloud Shell
Cloud Shell tire profit de Stockage Fichier Azure pour conserver les fichiers d’une session à l’autre.

## <a name="set-up-a-clouddrive-file-share"></a>Créer un partage de fichiers `clouddrive`
Lors du premier démarrage, Cloud Shell vous invite à associer un partage de fichiers nouveau ou existant afin de conserver les fichiers entre les sessions.

### <a name="create-new-storage"></a>Créer un stockage

Lorsque vous utilisez des paramètres de base et sélectionnez uniquement un abonnement, Cloud Shell crée trois ressources pour vous dans la région prise en charge la plus proche de vous :
* Groupe de ressources : `cloud-shell-storage-<region>`
* Compte de stockage : `cs<uniqueGuid>`
* Partage de fichiers : `cs-<user>-<domain>-com-<uniqueGuid>`

![Paramètre d’abonnement](media/basic-storage.png)

Le partage de fichiers est monté comme un `clouddrive` dans votre répertoire `$Home`. Ce partage de fichiers est également utilisé pour stocker une image de 5 Go créée pour vous, et qui met à jour et conserve automatiquement votre répertoire `$Home`. Cette opération n’a lieu qu’une seule fois, car le partage de fichiers est automatiquement monté dans les sessions suivantes.

### <a name="use-existing-resources"></a>Utiliser les ressources existantes

L’option Avancé vous permet d’associer des ressources existantes. Lorsque l’invite de configuration du stockage s’affiche, sélectionnez **Afficher les paramètres avancés** pour visualiser des options supplémentaires. Les partages de fichiers existants reçoivent une image utilisateur de 5 Go pour conserver votre répertoire `$Home`. Les menus déroulants sont filtrés sur la région Cloud Shell ainsi que sur les comptes de stockage localement redondants et géoredondants.

![Paramètre Groupe de ressources](media/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restreindre la création de ressources avec une stratégie de ressource Azure
Les comptes de stockage que vous créez dans Cloud Shell sont identifiés à l’aide de la balise `ms-resource-usage:azure-cloud-shell`. Si vous souhaitez interdire aux utilisateurs de créer des comptes de stockage par le biais de Cloud Shell, créez une [stratégie de ressource Azure pour les balises](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) déclenchée par cette balise spécifique.

## <a name="how-cloud-shell-works"></a>Fonctionnement de Cloud Shell
Cloud Shell conserve les fichiers à l’aide des deux méthodes suivantes :
* Création d’une image disque de votre répertoire `$Home` pour conserver la totalité du contenu figurant dans le répertoire. Cette image disque est enregistrée dans votre partage de fichiers spécifié en tant que `acc_<User>.img` à l’emplacement `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` et synchronise automatiquement les modifications.

* Montage du partage de fichiers spécifié en tant que `clouddrive` dans votre répertoire `$Home` pour l’interaction directe avec le partage de fichiers. `/Home/<User>/clouddrive` est mappé à `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Tous les fichiers figurant dans votre répertoire `$Home`, tels que les clés SSH, sont conservés dans l’image disque utilisateur qui est stockée dans votre partage de fichiers monté. Appliquez les bonnes pratiques lors de la conservation d’informations dans votre répertoire `$Home` et votre partage de fichiers monté.

## <a name="use-the-clouddrive-command"></a>Utiliser la commande `clouddrive`
Avec Cloud Shell, vous pouvez exécuter une commande appelée `clouddrive`, qui vous permet de mettre à jour manuellement le partage de fichiers qui est monté dans Cloud Shell.
![Utilisation de la commande clouddrive](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Monter un nouveau `clouddrive`

### <a name="prerequisites-for-manual-mounting"></a>Prérequis pour le montage manuel
Vous pouvez mettre à jour le partage de fichiers qui est associé à Cloud Shell à l’aide de la commande `clouddrive mount`.

Si vous montez un partage de fichiers existant, les comptes de stockage doivent être :
* Des comptes de stockage localement redondant ou géoredondant pour prendre en charge les partages de fichiers.
* Situés dans votre région affectée. Lors de l’intégration, la région qui vous est affectée est répertoriée dans le nom de groupe de ressources `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Régions de stockage prises en charge
Les fichiers Azure doivent résider dans la même région que la machine Cloud Shell sur laquelle le montage est effectué. Les clusters Cloud Shell existent actuellement dans les régions suivantes :
|Domaine|Région|
|---|---|
|Amérique|Est des États-Unis, Sud du centre des États-Unis, Ouest des États-Unis|
|Europe|Europe du Nord, Europe de l’Ouest|
|Asie-Pacifique|Inde-Centre, Sud-Est asiatique|

### <a name="the-clouddrive-mount-command"></a>La commande `clouddrive mount`

> [!NOTE]
> Si vous montez un nouveau partage de fichiers, une nouvelle image utilisateur est créée pour votre répertoire `$Home`, car votre image `$Home` précédente est conservée dans le partage de fichiers précédent.

Exécutez la commande `clouddrive mount` avec les paramètres suivants :

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Pour afficher plus de détails, exécutez `clouddrive mount -h`, comme illustré ici :

![Exécution de la commande clouddrive mount](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Démonter `clouddrive`
Vous pouvez démonter un partage de fichiers monté sur Cloud Shell à tout moment. Une fois le partage de fichiers démonté, vous êtes invité à monter un nouveau partage de fichiers avant la prochaine session.

Pour supprimer un partage de fichiers de Cloud Shell :
1. Exécutez `clouddrive unmount`.
2. Acceptez et confirmez les invites.

Votre partage de fichiers continue d’exister, tant que vous ne le supprimez pas manuellement. Cloud Shell ne fera plus de recherche pour ce partage de fichiers lors des sessions ultérieures.

Pour afficher plus de détails, exécutez `clouddrive unmount -h`, comme illustré ici :

![Exécution de la commande clouddrive unmount](media/unmount-h.png)

> [!WARNING]
> L’exécution de cette commande ne supprime pas de ressources. La suppression manuelle du groupe de ressources, compte de stockage ou partage de fichiers mappé à Cloud Shell efface votre image du répertoire `$Home` et tout autre fichier présent dans votre partage de fichiers. Il est impossible d’annuler cette opération.

## <a name="list-clouddrive-file-shares"></a>Répertorier les partages de fichiers `clouddrive`
Pour détecter quel partage de fichiers est monté comme `clouddrive`, exécutez la commande `df` suivante. 

Le chemin de fichier vers clouddrive affiche le nom de votre compte de stockage et le partage de fichiers dans l’URL. Par exemple, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                               1K-blocks     Used Available Use% Mounted on
overlay                                                   30428648 15585636  14826628  52% /
tmpfs                                                       986704        0    986704   0% /dev
tmpfs                                                       986704        0    986704   0% /sys/fs/cgroup
/dev/sda1                                                 30428648 15585636  14826628  52% /etc/hosts
shm                                                          65536        0     65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName        6291456  5242944   1048512  84% /usr/justin/clouddrive
/dev/loop0                                                 5160576   601652   4296780  13% /home/justin
```

## <a name="transfer-local-files-to-cloud-shell"></a>Transférer des fichiers locaux vers Cloud Shell
Le répertoire `clouddrive` se synchronise dans le panneau Stockage du portail Azure. Utilisez ce panneau pour échanger des fichiers avec votre partage de fichiers. La mise à jour des fichiers à partir de Cloud Shell se reflète dans l’interface graphique du stockage de fichiers quand vous actualisez le panneau.

### <a name="download-files"></a>Télécharger des fichiers

![Liste de fichiers locaux](media/download.png)
1. Dans le portail Azure, accédez au partage de fichiers monté.
2. Sélectionnez le fichier cible.
3. Cliquez sur le bouton **Télécharger**.

### <a name="upload-files"></a>Charger des fichiers

![Fichiers locaux à charger](media/upload.png)
1. Accédez au partage de fichiers monté.
2. Cliquez sur le bouton **Charger**.
3. Sélectionnez le ou les fichiers que vous souhaitez charger.
4. Confirmez le chargement.

Vous devriez à présent voir les fichiers accessibles dans votre répertoire `clouddrive` dans Cloud Shell.

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Cloud Shell](quickstart.md) <br>
[En savoir plus sur Azure Stockage Fichier](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[En savoir plus sur les balises de stockage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

