---
title: "Conservation des fichiers dans Azure Cloud Shell (version préliminaire) | Microsoft Docs"
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
ms.date: 07/17/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 9f988b4d2d33d0ca07dd7d837608f47bd9690bb1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>Conservation des fichiers dans Azure Cloud Shell
Cloud Shell tire profit d’Azure Stockage Fichier pour conserver les fichiers entre les sessions.

## <a name="setup-clouddrive"></a>Configurer clouddrive
Au démarrage initial, Cloud Shell vous invite à associer un partage de fichiers nouveau ou existant afin de conserver les fichiers entre les sessions.

### <a name="creating-new-storage"></a>Création d’un stockage
![](media/basic-storage.png)

Lorsque vous utilisez les paramètres de base et ne sélectionnez qu’un abonnement, trois ressources sont créées en votre nom dans la région prise en charge la plus proche de vous :
1. Groupe de ressources nommé : `cloud-shell-storage-<region>`
2. Compte de stockage nommé : `cs-uniqueGuid`
3. Partage de fichiers nommé : `cs-<user>-<domain>-com-uniqueGuid`

Ce partage de fichiers sera monté en tant que `clouddrive` dans le répertoire $Home. Ce partage de fichiers est également utilisé pour stocker une image de 5 Go créée pour vous qui met à jour et conserve votre répertoire $Home automatiquement. Il s’agit d’une action unique qui se monte automatiquement pour les sessions ultérieures.

### <a name="use-existing-resources"></a>Utiliser les ressources existantes
![](media/advanced-storage.png) Vous disposez également d’une option avancée qui vous permet d’associer une ressource existante. Lorsque l’invite de configuration du stockage s’affiche, sélectionnez « Afficher les paramètres avancés » pour visualiser des options supplémentaires. Les partages de fichiers existants recevront une image utilisateur de 5 Go pour conserver votre répertoire $Home. Les listes déroulantes sont filtrées pour la région Cloud Shell qui vous a été attribuée et pour les comptes de stockage localement/globalement redondants.

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restreindre la création de ressources avec une stratégie de ressource Azure
Les comptes de stockage créés par Cloud Shell sont identifiés à l’aide de la balise `ms-resource-usage:azure-cloud-shell`. Si vous souhaitez interdire aux utilisateurs de créer des comptes de stockage par le biais de Cloud Shell, créez une [stratégie de ressource Azure pour les balises](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags) déclenchée par cette balise spécifique.

## <a name="how-it-works"></a>Fonctionnement
### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Cloud Shell conserve les fichiers avec les deux méthodes suivantes :
1. Créez une image disque de votre répertoire $Home pour conserver la totalité du contenu figurant dans $Home. Cette image disque est enregistrée dans votre partage de fichiers spécifié en tant que `acc_<User>.img` à l’emplacement `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` et synchronise automatiquement les modifications.

2. Montez votre partage de fichiers spécifié en tant que `clouddrive` dans votre répertoire $Home pour l’interaction directe du partage de fichiers. 
`/Home/<User>/clouddrive` est mappé à `fileshare.storage.windows.net/fileshare`.
 
> [!Note]
> Tous les fichiers figurant dans votre répertoire $Home, tels que les clés SSH, sont conservés dans l’image de disque utilisateur stockée dans votre partage de fichiers monté. Appliquez les meilleures pratiques lors de la conservation d’informations dans votre répertoire $Home et votre partage de fichiers monté.

## <a name="using-the-clouddrive-command"></a>Utilisation de la commande clouddrive
Cloud Shell permet aux utilisateurs d’exécuter une commande appelée `clouddrive` qui permet de mettre à jour manuellement le partage de fichiers monté sur Cloud Shell.
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Monter un nouveau clouddrive

### <a name="pre-requisites-for-manual-mounting"></a>Conditions préalables pour le montage manuel
Vous pouvez mettre à jour le partage de fichiers associé à Cloud Shell avec la commande `clouddrive mount`.

En cas de montage d’un partage de fichiers existant, les comptes de stockage doivent être :
1. LRS ou GRS pour prendre en charge les partages de fichiers.
2. Situés dans votre région affectée. Lors de l’intégration, la région qui vous est affectée est répertoriée dans le nom de groupe de ressources `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Régions de stockage prises en charge
Azure Files doit résider dans la même région que la machine Cloud Shell sur laquelle le montage est effectué. Les machines Cloud Shell existent dans les régions suivantes :
|Domaine|Région|
|---|---|
|Amérique|Est des États-Unis, Sud du centre des États-Unis, Ouest des États-Unis|
|Europe|Europe du Nord, Europe de l’Ouest|
|Asie-Pacifique|Inde-Centre, Sud-Est asiatique|

### <a name="mount-command"></a>Commande de montage

> [!NOTE]
> Si vous montez un nouveau partage de fichiers, une nouvelle image utilisateur sera créée pour votre répertoire $Home, car votre image $Home précédente est conservée dans le partage de fichiers précédent.

Exécutez `clouddrive mount` les paramètres suivants <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Pour plus de détails, exécutez `clouddrive mount -h` : <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Démontage de clouddrive
Vous pouvez démonter un partage de fichiers monté sur Cloud Shell à tout moment. Toutefois, Cloud Shell requiert un partage de fichiers monté, vous serez donc invité à créer et monter un nouveau partage de fichiers lors de la prochaine session en cas de suppression.

Pour supprimer un partage de fichiers de Cloud Shell :
1. Exécutez `clouddrive unmount`.
2. Acceptez et confirmez les invites

Votre partage de fichiers continue à exister, sauf en cas de suppression manuelle. Cloud Shell ne fera plus de recherche pour ce partage de fichiers lors des sessions ultérieures.

Pour plus de détails, exécutez `clouddrive mount -h` : <br>
![](media/unmount-h.png)

> [!WARNING]
> Cette commande ne supprime pas de ressources. Toutefois, la suppression manuelle du groupe de ressources, compte de stockage ou partage de fichiers mappé à Cloud Shell effacera votre image disque du répertoire $Home et les fichiers présents dans votre partage de fichiers. Cette action est irréversible.

## <a name="list-clouddrive"></a>Répertorier clouddrive
Pour découvrir quel partage de fichiers est monté en tant que `clouddrive` :

Exécutez `df`. 

Le chemin d’accès à clouddrive affiche le nom de votre compte de stockage et le partage de fichiers dans l’URL.

`//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

## <a name="transfer-local-files-to-cloud-shell"></a>Transférer des fichiers locaux vers Cloud Shell
Le répertoire `clouddrive` se synchronise dans le panneau Stockage du portail Azure. Utilisez celui-ci pour échanger des fichiers avec votre partage de fichiers. La mise à jour des fichiers à partir Cloud Shell se reflète dans l’interface graphique de stockage de fichiers lors de l’actualisation du panneau.

### <a name="download-files"></a>Télécharger des fichiers
![](media/download.png)
1. Accéder au partage de fichiers monté
2. Sélectionner le fichier cible dans le portail
3. Appuyez sur « Télécharger »

### <a name="upload-files"></a>Charger des fichiers
![](media/upload.png)
1. Accéder au partage de fichiers monté
2. Sélectionner « Télécharger ».
3. Sélectionner le fichier à charger
4. Confirmer le chargement

Vous devriez à présent voir le fichier accessible dans votre répertoire clouddrive dans Cloud Shell.

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Cloud Shell](quickstart.md) <br>
[En savoir plus sur Azure Stockage Fichier](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[En savoir plus sur les balises de stockage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
