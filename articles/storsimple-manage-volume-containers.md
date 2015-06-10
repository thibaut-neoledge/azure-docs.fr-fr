<properties 
   pageTitle="Gérer vos conteneurs de volumes StorSimple"
   description="Explique comment utiliser la page des conteneurs de volumes du service StorSimple Manager pour ajouter, modifier ou supprimer un conteneur de volumes."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/13/2015"
   ms.author="v-sharos" />

# Gérer vos conteneurs de volumes StorSimple

## Vue d'ensemble

Ce didacticiel explique comment utiliser le service StorSimple Manager pour créer et gérer des conteneurs de volumes StorSimple.

Un conteneur de volumes créé dans un appareil Microsoft Azure StorSimple contient un ou plusieurs volumes hébergeant les paramètres de consommation du compte de stockage, du chiffrement et de la bande passante. Un appareil peut comporter plusieurs conteneurs de volumes pour l’ensemble de ses volumes.

Un conteneur de volumes présente les attributs suivants :

- **Volumes** : les volumes StorSimple alloués dynamiquement et hébergés dans le conteneur de volumes. Un conteneur de volumes peut contenir jusqu’à 256 volumes StorSimple alloués dynamiquement.

- **Chiffrement** : clé de chiffrement pouvant être définie pour chaque conteneur de volumes. Cette clé est utilisée pour chiffrer les données envoyées depuis votre appareil StorSimple sur le cloud. Une clé AES 256 bits de niveau militaire est utilisée avec la clé saisie par l’utilisateur. Pour sécuriser vos données, nous vous recommandons de toujours activer le chiffrement du stockage cloud.

- **Compte de stockage** : le compte de stockage qui est lié à votre fournisseur de services de stockage cloud. L’ensemble des volumes hébergés dans votre conteneur de volumes partagent ce compte de stockage. Vous pouvez choisir un compte de stockage à partir d’une liste existante ou créer un compte lors de la création du conteneur de volumes, avant de spécifier les informations d’identification d’accès associées à ce compte.

- **Bande passante du cloud** : consommation de bande passante de l’appareil lors de l’envoi de ses données vers le cloud. Vous pouvez appliquer un contrôle de bande passante en définissant une valeur comprise entre 1 et 1 000 Mbits/s lorsque vous définissez ce conteneur. Si vous souhaitez que l’appareil consomme toute la bande passante disponible, définissez la valeur llimité dans ce champ. Vous pouvez également créer et appliquer un modèle de bande passante, afin d’allouer la bande passante suivant une planification.

![Page des conteneurs de volumes](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Les procédures suivantes expliquent comment utiliser la page **Conteneurs de volume** StorSimple pour effectuer les opérations courantes ci-dessous :

- Ajouter un conteneur de volumes 
- Modifier un conteneur de volumes 
- Supprimer un conteneur de volumes 

## Ajouter un conteneur de volumes

Pour créer un conteneur de volumes, procédez comme suit.

[AZURE.INCLUDE [storsimple-add-volume-container](../includes/storsimple-add-volume-container.md)]


## Modifier un conteneur de volumes

Pour modifier un conteneur de volumes, procédez comme suit.

[AZURE.INCLUDE [storsimple-modify-volume-container](../includes/storsimple-modify-volume-container.md)]


## Supprimer un conteneur de volumes

Un conteneur de volumes comporte plusieurs volumes. Il peut être supprimé uniquement après la suppression de l’ensemble des volumes qu’il héberge. Pour supprimer un conteneur de volumes, procédez comme suit.

[AZURE.INCLUDE [storsimple-delete-volume-container](../includes/storsimple-delete-volume-container.md)]

## Étapes suivantes

Élargissez vos connaissances en matière de [gestion des volumes StorSimple](storsimple-manage-volumes.md).

<!---HONumber=58-->