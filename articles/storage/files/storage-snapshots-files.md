---
title: "Vue d’ensemble des instantanés de partage de fichiers Azure (préversion) | Microsoft Docs"
description: "Un instantané de partage de fichiers Azure est une version en lecture seule d’un partage de fichiers Azure pris à un point dans le temps. Un instantané de partage peut être lu, copié ou supprimé, mais pas modifié. Les instantanés de partage sont une façon de sauvegarder le partage à un instant T."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 70b7fec318b527c6c67bd281892c10814712bbc6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-files-share-snapshot-preview-overview"></a>Vue d’ensemble des instantanés de partage de fichiers Azure (préversion)
Azure Files vous permet de prendre des instantanés de partages de fichiers. Les instantanés de partage (préversion) capturent l’état d’un partage à ce point dans le temps. Dans cet article, nous décrivons les fonctionnalités fournies par les instantanés de partage et comment vous pouvez les utiliser dans votre propre cas d’usage.


## <a name="when-to-use-share-snapshots"></a>Quand utiliser les instantanés de partage

### <a name="protection-against-application-error-and-data-corruption"></a>Protection contre l’altération des données et les erreurs d’application
-------------------------------------------------------------------------

Les applications qui utilisent des partages de fichiers Azure effectuent diverses opérations telles que l’écriture, la lecture, le stockage, la transmission ou le traitement. Une application peut présenter un défaut de configuration ou un bogue involontaire peut être introduit, entraînant l’altération ou le remplacement accidentel de quelques blocs. Pour vous protéger contre ces scénarios, vous pouvez prendre un instantané de partage avant de déployer le nouveau code d’application ; si le nouveau déploiement introduit un bogue ou une erreur d’application, vous pouvez revenir à une version précédente de vos données résidant sur ce partage de fichiers. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Protection contre une suppression accidentelle ou une modification involontaire
----------------------------------------------------------------------------------------------

Imaginons que nous travaillons sur un fichier texte résidant dans un partage de fichiers. Une fois le fichier texte fermé, nous ne pouvons plus annuler les modifications que nous avons apportées. Dans ce cas, nous devons récupérer une version précédente du fichier. Les instantanés de partage vous permettent de récupérer des versions précédentes du fichier s’il est renommé ou supprimé accidentellement.

### <a name="general-backup-purposes"></a>Sauvegarde générale
---------------------------

Après avoir créé un partage de fichiers Azure, vous pouvez régulièrement créer un instantané de ce partage en vue de l’utiliser à des fins de sauvegarde de données. Pris régulièrement, un instantané de partage facilite la conservation de versions antérieures de données à des fins d’audit ou de récupération d’urgence.

## <a name="share-snapshot-capabilities"></a>Fonctionnalités d’instantané de partage

Un instantané de partage est une copie en lecture seule de vos données à un point dans le temps. Vous pouvez créer, supprimer et gérer les instantanés à l’aide de l’API REST. Les mêmes fonctionnalités sont disponibles dans la bibliothèque cliente, l’interface CLI et le portail Azure. L’intégration à PowerShell des fonctionnalités d’instantané de partage sera également bientôt disponible. Vous pouvez afficher les instantanés d’un partage à l’aide de l’API REST et de SMB. Les clients peuvent récupérer la liste des versions de répertoire ou de fichier, ainsi que monter une version spécifique directement en tant que lecteur. Un instantané de partage peut être lu, copié ou supprimé, mais pas modifié. Vous ne pouvez pas copier un instantané de partage entier vers un autre compte de stockage ; vous devez effectuer cette opération fichier par fichier à l’aide de la commande azcopy ou d’autres mécanismes de copie.

La fonctionnalité d’instantané de partage est fournie au niveau du partage de fichiers, tandis que la récupération est fournie au niveau des fichiers individuels pour permettre la restauration de fichiers spécifiques. Vous pouvez restaurer un partage de fichier complet à l’aide de SMB, de l’API REST, du portail, de la bibliothèque cliente, ou des outils PowerShell/CLI.

Un instantané d’un partage de fichiers est identique au partage de fichiers de base, à la différence que l’URI du partage a une valeur **DateTime** à la fin qui indique l’heure à laquelle l’instantané de partage a été pris. Par exemple, si l’URI d’un partage de fichiers est http://storagesample.core.file.windows.net/myshare, l’URI de l’instantané de partage est similaire à ce qui suit :
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z.
```

Les instantanés de partage sont conservés jusqu’à ce qu’ils soient explicitement supprimés. Un instantané de partage ne peut pas être conservé plus longtemps que son partage de fichiers de base. Vous pouvez énumérer les instantanés associés au partage de fichiers de base pour effectuer le suivi de vos instantanés actuels. Quand vous créez un instantané d’un partage de fichiers, les fichiers qui résident dans les propriétés système du partage sont copiés dans l’instantané de partage avec les mêmes valeurs. Les fichiers de base et les métadonnées du partage de fichiers sont également copiés dans l’instantané de partage, sauf si vous spécifiez des métadonnées distinctes pour l’instantané de partage quand vous le créez.

Pour supprimer un partage qui a des instantanés de partage, vous devez d’abord supprimer la totalité de ces derniers.


## <a name="share-snapshot-space-usage"></a>Utilisation de l’espace par les instantanés de partage 

Les instantanés de partage sont incrémentiels par nature ; ainsi, seules sont enregistrées les données qui ont changé après le dernier instantané de partage. Cela réduit le temps nécessaire à la création de l’instantané de partage, ainsi que les coûts de stockage. Toute opération d’écriture sur l’objet ou de mise à jour de métadonnées ou de propriétés est stockée dans l’instantané de partage, car elle est considérée comme une modification de contenu. 

Afin d’économiser l’espace, vous pouvez supprimer l’instantané de partage dont la période a connu la plus forte évolution.

Bien que les instantanés de partage soient enregistrés de façon incrémentielle, le processus de suppression des instantanés de partage est conçu afin que vous ne deviez conserver que le dernier instantané de partage pour restaurer le partage. Quand vous supprimez un instantané de partage, seules sont supprimées les données propres à ce dernier. Les instantanés actifs contiennent toutes les informations nécessaires pour parcourir vos données et les restaurer (à partir du moment où l’instantané de partage a été pris) à l’emplacement d’origine ou à un autre emplacement. La restauration peut être effectuée au niveau de l’élément.

Les instantanés ne sont pas décomptés de votre limite de partage de de 5 To. L’espace total occupé par un instantané de partage n’est pas limité. Les limites de compte de stockage continuent de s’appliquer.

## <a name="limits"></a>limites

Le nombre maximal d’instantanés de partage autorisé par Azure Files s’élève aujourd’hui à 200. Au-delà de 200 instantanés de partage, l’utilisateur doit supprimer les anciens instantanés de partage pour créer d’autres instantanés de partage. Il n’existe aucune limite quant aux appels simultanés de création d’instantané de partage.
Il n’existe aucune limite quant à la quantité d’espace que peuvent consommer les instantanés d’un partage de fichiers particulier. 

## <a name="copy-data-back-to-a-share-from-share-snapshot"></a>Copier les données vers un partage à partir d’un instantané de partage

Les opérations de copie impliquant des fichiers et des instantanés de partage respectent les règles suivantes :

Vous pouvez copier des fichiers individuels d’un instantané de partage de fichiers vers son partage de base ou vers un autre emplacement. Vous pouvez restaurer une version antérieure d’un fichier ou restaurer le partage de fichiers complet en effectuant une copie fichier par fichier à partir de l’instantané de partage. L’instantané de partage n’est pas promu vers le partage de base. L’instantané de partage reste intact après la copie, mais le partage de fichiers de base est remplacé par une copie des données qui étaient disponibles dans l’instantané de partage. Tous les fichiers restaurés sont considérés comme du contenu changé.

Vous pouvez copier un fichier dans un instantané de partage vers une destination avec un nom différent. Le fichier de destination qui en découle est modifiable et n’est pas un instantané de partage.

Quand un fichier de destination est remplacé par une copie, tous les instantanés de partage associés au fichier de destination d’origine restent intacts.

## <a name="general-best-practices"></a>Bonnes pratiques générales 

Quand vous exécutez l’infrastructure sur Azure, automatisez les sauvegardes pour la récupération de données chaque fois que possible. Les actions automatisées sont plus fiables que les processus manuels, contribuant à améliorer la capacité de récupération et la protection des données. Vous pouvez utiliser l’API REST, le SDK client ou des scripts pour l’automatisation.

Étudiez attentivement vos paramètres de fréquence et de rétention des instantanés de partage avant de déployer le planificateur d’instantané de partage pour éviter de subir des frais d’instantané de partage inutiles.

Les instantanés de partage ne fournissent une protection qu’au niveau des fichiers ; ils ne protègent pas contre les suppressions accidentelles sur un compte de stockage ou un partage de fichiers. Pour protéger le compte de stockage contre une suppression accidentelle, vous pouvez verrouiller celui-ci ou le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes
* [Utiliser un instantané de partage de fichiers Azure](storage-how-to-use-files-snapshots.md)
* [Forum aux questions sur les instantanés de partage](storage-files-faq.md)

