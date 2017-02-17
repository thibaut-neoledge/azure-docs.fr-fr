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
ms.date: 11/18/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: ca01baebd755e92aff1ef080af1fcd4606b9a2ec
ms.openlocfilehash: 200caac4b480495b0bbb8f10cc89fdd73c42dbe2


---
# <a name="azure-government-compute"></a>Calcul Azure Government
## <a name="virtual-machines"></a>Machines virtuelles
Pour plus d’informations sur ce service et son utilisation, consultez [Tailles de Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="variations"></a>Variantes
Les produits (machines virtuelles) sont disponibles dans Azure Government :

| Produits (machines virtuelles) | Gouvernement américain VA | Gouvernement américain IA |
| --- | --- | --- |
| A0-A7 |O |O |
| Série D |O |N |
| Série Dv2 |O |O |
| Série DS |O |N |
| Série F |O |O |
| Série FS |N |N |
| Série G |O |N |
| Série GS |O |N |

### <a name="data-considerations"></a>Considérations sur les données
Les informations suivantes identifient les limites d’Azure Government pour les machines virtuelles Azure :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Les données entrées, stockées et traitées au sein d’une machine virtuelle peuvent contenir des données contrôlées à l’exportation. Fichiers binaires exécutés au sein des machines virtuelles Azure. Authentificateurs statiques, tels que les mots de passe et les codes confidentiels de carte à puce pour l’accès aux composants de la plateforme Azure. Clés privées des certificats utilisés pour gérer les composants de la plateforme Azure. Chaînes de connexion SQL.  Autres informations de sécurité/secrets, tels que les certificats, clés de chiffrement, clés principales et clés de stockage stockées dans les services Azure. |Les métadonnées n’ont pas le droit de contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre machine virtuelle Azure.  N’entrez pas de données réglementées/contrôlées dans les champs suivants : Noms de rôles client, Groupes de ressources, Noms de déploiements, Noms de ressources, Balises de ressources |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government. </a>




<!--HONumber=Dec16_HO1-->


