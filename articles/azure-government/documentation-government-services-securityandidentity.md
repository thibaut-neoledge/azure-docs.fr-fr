---
title: "Sécurité et identité Azure Government | Microsoft Docs"
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: cd01170c3c0a3f62024de3357d342af1f4f90c6c
ms.openlocfilehash: 27d447e8e3c336bbce2e1ca81d2c7c413b0360fc


---
# <a name="azure-government-security--identity"></a>Sécurité et identité Azure Government
## <a name="key-vault"></a>Key Vault
Pour plus d’informations sur ce service et son utilisation, consultez la [documentation publique d’Azure Key Vault](../key-vault/index.md).

### <a name="data-considerations"></a>Considérations sur les données
Les informations suivantes identifient les limites d’Azure Government pour Azure Key Vault :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Toutes les données chiffrées avec une clé Azure Key Vault peuvent contenir des données contrôlées/réglementées. |Les métadonnées Azure Key Vault n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre coffre de clés.  N’entrez pas de données réglementées/contrôlées dans les champs suivants : Noms de groupes de ressources, Noms de coffres de clés, Nom d’abonnement |

Azure Key Vault est mis à la disposition générale dans Azure Government. Comme en environnement public, il n’y a pas d’extension. Key Vault est donc uniquement disponible via PowerShell et l’interface CLI.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government. </a>




<!--HONumber=Nov16_HO3-->


