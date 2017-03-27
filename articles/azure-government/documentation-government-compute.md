---
title: Calcul Azure Government | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 3/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 146fcd9788bab55e4abd9e70dcfe25741f041d34
ms.lasthandoff: 03/15/2017


---
# <a name="azure-government-compute"></a>Calcul Azure Government
## <a name="virtual-machines"></a>Machines virtuelles
Pour plus d’informations sur ce service et son utilisation, consultez [Tailles de Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="variations"></a>Variantes
Les produits (machines virtuelles) sont disponibles dans Azure Government :

| Produits (machines virtuelles) | Gouvernement américain VA | Gouvernement américain IA | Est des États-Unis – US DoD | Ouest des États-Unis - US DoD
| --- | --- | --- |--- |--- |
| A0-A7 |O |O |O |O |O |
| Av2 |O |O |O |O |O |
| Série D |O |N |N |N |
| Série Dv2 |O |O |O |O |
| Série DS |O |N |N |N |
| Séries DSv2 |O |N |O |O |
| Série F |O |O |O |O |
| Série FS |O |N |O |O |
| Série G |O |N |N |N |
| Série GS |O |N |N |N |

### <a name="data-considerations"></a>Considérations sur les données
Les informations suivantes identifient les limites d’Azure Government pour les machines virtuelles Azure :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Les données entrées, stockées et traitées au sein d’une machine virtuelle peuvent contenir des données contrôlées à l’exportation. Fichiers binaires exécutés au sein des machines virtuelles Azure. Authentificateurs statiques, tels que les mots de passe et les codes confidentiels de carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Chaînes de connexion SQL.  Autres informations de sécurité/secrets, tels que les certificats, clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure. |Les métadonnées n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre machine virtuelle Azure.  N’entrez pas de données réglementées/contrôlées dans les champs suivants : Noms de rôles client, Groupes de ressources, Noms de déploiements, Noms de ressources, Balises de ressources |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government. </a>


