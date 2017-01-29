---
title: Stockage Azure Government | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: e9287ab74e9bed8681b66fa9a168c8d7c403c63b
ms.openlocfilehash: 8c9488e302540ca0c67926f6aa6fad591c6ce037


---
# <a name="azure-government-storage"></a>Stockage Azure Government
## <a name="azure-storage"></a>Azure Storage
Pour plus d’informations sur ce service et son utilisation, consultez la [documentation publique du stockage Azure](../storage/index.md).

### <a name="storage-service-availability-by-azure-government-region"></a>Disponibilité des services de stockage par région Azure Government

| Service | Gouvernement américain - Virginie | USGov Iowa | Remarques
| --- | --- | --- | --- |
| [Stockage Blob] (../storage/storage-introduction.md#blob-storage) |GA |GA |
| [Stockage Table] (../storage/storage-introduction.md#table-storage) |GA  |GA |
| [Stockage File d’attente] (../storage/storage-introduction.md#queue-storage) |GA | GA |
| [Stockage Fichier] (../storage/storage-introduction.md#file-storage) |GA |GA |
| [Stockage Blob à chaud/froid] (../storage/storage-blob-storage-tiers.md) |N/D |N/D |
| [Stockage Chiffrement du service] (../storage/storage-service-encryption.md) |GA |GA |
| [Stockage Premium] (../storage/storage-premium-storage.md) |GA |N/D | Comprend des machines virtuelles de la série DS. |
| [Importation/Exportation d’objets Blob] (../storage/storage-import-export-service.md) |GA |GA | Seuls les comptes de stockage classiques sont actuellement pris en charge. |

### <a name="variations"></a>Variantes
Les URL pour les comptes de stockage dans Azure Government sont différents :

| Type de service | Azure (public) | Azure Government |
| --- | --- | --- |
| Stockage d’objets blob |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Stockage de files d'attente |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Stockage de tables |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| Stockage de fichiers |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> Tous vos scripts et codes doivent prendre en compte les points de terminaison appropriés.  Voir [Configuration des chaînes de connexion du Stockage Azure](../storage/storage-configure-connection-string.md). 
>
>

Pour en savoir plus sur les API, consultez le <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Cloud Storage Account Constructor</a> (Constructeur de compte de stockage Cloud).

Le suffixe de point de terminaison à utiliser dans ces surcharges est core.usgovcloudapi.net

> [!NOTE]
> [L’explorateur de stockage Microsoft Azure] (../vs-azure-tools-storage-manage-with-storage-explorer.md) ne prend actuellement pas en charge la [connexion à un abonnement Azure] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription) en ajoutant un compte dans Azure Government. Utilisez d’autres méthodes de [connexion à un compte de stockage] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service).
En cas [d’attachement à un compte de stockage externe] (../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account) à **l’étape 3**, sélectionnez **Autres... (spécifiez ci-dessous)** en tant que domaine de point de terminaison de stockage et spécifiez **core.usgovcloudapi.net** pour Azure Government.
>
>

> [!NOTE]
> Si l’erreur 53 indiquant que « Le chemin réseau n'a pas été trouvé ». est retournée pendant le [montage du partage de fichiers] (../storage/storage-dotnet-how-to-use-files.md#mount-the-file-share). Elle peut être due au blocage du port de sort par un pare-feu. Essayez de monter le partage de fichiers sur la machine virtuelle qui se trouve dans le même abonnement Azure que le compte de stockage.
>
>

### <a name="considerations"></a>Considérations
Les informations suivantes identifient les limites d’Azure Government pour le stockage Azure :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Les données entrées, stockées et traitées au sein d’un produit de stockage Azure peuvent contenir des données contrôlées à l’exportation. Authentificateurs statiques, tels que les mots de passe et les codes confidentiels de carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Autres informations de sécurité/secrets, tels que les certificats, clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure. |Les métadonnées Azure Storage n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre produit de stockage.  N’entrez pas de données réglementées/contrôlées dans les champs suivants : Groupes de ressources, Noms de déploiements, Noms de ressources, Balises de ressources |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.



<!--HONumber=Dec16_HO3-->


