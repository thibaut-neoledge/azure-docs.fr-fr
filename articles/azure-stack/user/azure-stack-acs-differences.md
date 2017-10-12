---
title: "Stockage Azure Stack : différences et points à prendre en compte"
description: "Découvrez les différences entre le stockage Azure Stack et le stockage Azure, ainsi que les points à prendre en compte quand vous déployez Azure Stack."
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 381950321ac3a5ea8a43b76f3fba868da4be4682
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Stockage Azure Stack : différences et points à prendre en compte

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Le stockage Azure Stack est l’ensemble des services cloud de stockage fournis dans Microsoft Azure Stack. Le stockage Azure Stack fournit des fonctionnalités de gestion des objets blob, des tables, des files d’attente et des comptes avec une sémantique Azure cohérente.

Cet article récapitule les différences connues entre le stockage Azure Stack et le stockage Azure. Il expose également les principaux points à prendre en compte quand vous déployez Azure Stack. Pour connaître les différences majeures entre Azure Stack et Azure, consultez la rubrique [Principales considérations](azure-stack-considerations.md).

## <a name="cheat-sheet-storage-differences"></a>Aide-mémoire : différences entre les stockages

| Fonctionnalité | Azure (global) | Azure Stack |
| --- | --- | --- |
|Stockage Fichier|Partages de fichiers SMB sur le cloud pris en charge|Pas encore pris en charge
|Chiffrement des données au repos|Chiffrement AES 256 bits|Pas encore pris en charge
|Type de compte de stockage|Comptes de stockage à usage général et comptes de stockage d’objets blob Azure|À usage général uniquement
|Options de réplication|Stockage localement redondant, stockage géoredondant, stockage géoredondant avec accès en lecture et stockage redondant dans une zone|Stockage localement redondant
|Stockage Premium|Entièrement pris en charge|Peut être approvisionné, mais sans limite ni garantie de performances
|Disques gérés|Premium et standard pris en charge|Pas encore pris en charge
|Nom de l’objet blob|1 024 caractères (2 048 octets)|880 caractères (1 760 octets)
|Taille maximale d’un objet blob de blocs|4,75 To (100 Mo X 50 000 blocs)|50 000 X 4 Mo (195 Go environ)
|Copie d’instantané incrémentiel d’objet blob de pages|Objets blob de pages Azure Premium et standard pris en charge|Pas encore pris en charge
|Taille maximale d’un objet blob de pages|8 To|1 To
|Taille de page d’un objet blob de pages|512 octets|4 Ko
|Taille de la clé de ligne et de la clé de partition de table|1 024 caractères (2 048 octets)|400 caractères (800 octets)

### <a name="metrics"></a>Mesures
Il existe également des différences sur le plan des métriques de stockage :
* Les données de transaction dans les métriques de stockage ne font pas la distinction entre la bande passante réseau interne et externe.
* Les données de transaction dans les métriques de stockage ne prennent pas en compte l’accès des machines virtuelles aux disques montés.

## <a name="api-version"></a>Version de l'API
Les versions suivantes sont prises en charge avec le stockage Azure Stack :

* Services de données du stockage Azure : [version du 05-04-2015 de l’API REST](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Services de gestion du stockage Azure : [préversion du 01-05-2015, version du 15-06-2015 et version du 01-01-2016](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec les outils de développement du stockage Azure Stack](azure-stack-storage-dev.md)
* [Présentation du stockage Azure Stack](azure-stack-storage-overview.md)

