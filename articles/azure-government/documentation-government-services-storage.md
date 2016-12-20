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
ms.sourcegitcommit: 004860b319b7b32955415ac2d6c7308c32037848
ms.openlocfilehash: 36312ce195e931d174e00d54cab4cb071a85b618


---
# <a name="azure-government-storage"></a>Stockage Azure Government
## <a name="azure-storage"></a>Azure Storage
Pour plus d’informations sur ce service et son utilisation, consultez la [documentation publique du stockage Azure](../storage/index.md).

### <a name="variations"></a>Variantes
Les URL pour les comptes de stockage dans Azure Government sont différents :

| Type de service | Azure (public) | Azure Government |
| --- | --- | --- |
| Stockage d’objets blob |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Stockage de files d'attente |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Stockage de tables |*.table.core.windows.net |*.table.core.usgovcloudapi.net |

> [!NOTE]
> Tous vos scripts et codes doivent prendre en compte les points de terminaison appropriés.  Voir [Configuration des chaînes de connexion du Stockage Azure](../storage/storage-configure-connection-string.md). 
>
>

Pour en savoir plus sur les API, consultez le <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Cloud Storage Account Constructor</a> (Constructeur de compte de stockage Cloud).

Le suffixe de point de terminaison à utiliser dans ces surcharges est core.usgovcloudapi.net

### <a name="considerations"></a>Considérations
Les informations suivantes identifient les limites d’Azure Government pour le stockage Azure :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Les données entrées, stockées et traitées au sein d’un produit de stockage Azure peuvent contenir des données contrôlées à l’exportation. Authentificateurs statiques, tels que les mots de passe et les codes confidentiels de carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Autres informations de sécurité/secrets, tels que les certificats, clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure. |Les métadonnées Azure Storage n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre produit de stockage.  N’entrez pas de données réglementées/contrôlées dans les champs suivants : Groupes de ressources, Noms de déploiements, Noms de ressources, Balises de ressources |

## <a name="premium-storage"></a>Stockage Premium
Pour en savoir plus, consultez l’article [Premium Storage : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md).

### <a name="variations"></a>Variantes
Le stockage Premium est disponible en Virginie (Gouvernement américain). Il comprend des machines virtuelles de la série DS.

### <a name="considerations"></a>Considérations
Les considérations concernant les données de stockage mentionnées ci-dessus s’appliquent aux comptes de stockage Premium.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.



<!--HONumber=Nov16_HO3-->


