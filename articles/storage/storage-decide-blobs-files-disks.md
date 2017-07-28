---
title: "Quand utiliser des objets blob Azure, des fichiers Azure ou des disques de données Azure"
description: "Découvrez les différentes façons de stocker les données dans Azure et d’y accéder pour choisir la technologie la mieux adaptée."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: e282a8a7bec1cb6e48110e7c9859a3a19ab8a11e
ms.contentlocale: fr-fr
ms.lasthandoff: 06/14/2017

---

# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-data-disks"></a>Quand utiliser des objets blob Azure, des fichiers Azure ou des disques de données Azure

Microsoft Azure propose plusieurs fonctionnalités dans le stockage Azure pour stocker vos données dans le cloud et y accéder. Cet article traite des fichiers, des objets blob et des disques de données Azure, afin de vous aider à choisir entre ces fonctionnalités.

## <a name="scenarios"></a>Scénarios

Le tableau suivant compare les fichiers, les objets blob et les disques de données, et il présente des exemples de scénarios appropriés pour chacun.

| Fonctionnalité | Description | Quand utiliser |
|--------------|-------------|-------------|
| **Azure Files** | Fournit une interface SMB, des bibliothèques clientes et une [interface REST](/rest/api/storageservices/file-service-rest-api) qui permet d’accéder en tout lieu aux fichiers stockés. | Vous souhaitez développer et transférer une application dans le cloud qui utilise déjà les API du système de fichiers natif pour partager des données avec d’autres applications s’exécutant dans Azure.<br/><br/>Vous souhaitez stocker les outils de développement et de débogage qui doivent être accessibles à partir de nombreuses machines virtuelles. |
| **Objets blob Azure** | Fournit des bibliothèques clientes et une [interface REST](/rest/api/storageservices/blob-service-rest-api) qui permet de stocker les données non structurées et d’y accéder à grande échelle dans les objets blob de blocs. | Vous souhaitez que votre application prenne en charge le streaming et l’accès aléatoire.<br/><br/>Vous souhaitez être en mesure d’accéder aux données d’application à partir de n’importe quel endroit. |
| **Disques de données Azure** | Fournit des bibliothèques clientes et une [interface REST](/rest/api/compute/virtualmachines/virtualmachines-create-or-update) qui permet de stocker de manière permanente les données et d’y accéder à partir d’un disque dur virtuel joint. | Vous souhaitez développer et transférer des applications qui utilisent les API du système de fichiers natif pour lire et écrire des données sur des disques persistants.<br/><br/>Vous souhaitez stocker des données dont l’accès n’est pas requis à l’extérieur de la machine virtuelle à laquelle le disque est joint. |

## <a name="comparison-files-and-blobs"></a>Comparaison : fichiers et objets blob

Le tableau suivant compare les fichiers Azure et les objets blob Azure.  
  
||||  
|-|-|-|  
|**Attribut**|**Objets blob Azure**|**Azure Files**|  
|Options de durabilité|LRS, ZRS, GRS (et RA-GRS pour une disponibilité supérieure)|LRS, GRS|  
|Accessibilité|API REST|API REST<br /><br /> SMB 2.1 et SMB 3.0 (API du système de fichiers standard)|  
|Connectivité|API REST : monde entier|API REST : monde entier<br /><br /> SMB 2.1 : région<br /><br /> SMB 3.0 : monde entier|  
|Endpoints|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Répertoires|Espace de noms plat|Vrais objets d’annuaire|  
|Sensibilité des noms à la casse|Respect de la casse|Non sensible à la casse, mais la casse est conservée|  
|Capacité|Conteneurs jusqu’à 500 To|Partages de fichiers de 5 To|  
|Débit|Jusqu’à 60 Mo/s par objet blob de blocs|Jusqu’à 60 Mo/s par partage|  
|Taille de l’objet|Jusqu’à 200 Go/objet blob de blocs|Jusqu’à 1 To/fichier|  
|Capacité facturée|En fonction des octets écrits|En fonction de la taille de fichier|  
|Bibliothèques clientes|Plusieurs langages|Plusieurs langages|  
  
## <a name="comparison-files-and-data-disks"></a>Comparaison : fichiers et disques de données

Les fichiers Azure complètent les disques de données Azure. Un disque de données ne peut être joint qu’à une seule machine virtuelle Azure à la fois. Les disques de données sont des VHD de format fixe stockés en tant qu’objets blob de pages dans le stockage Azure, et ils sont utilisés par la machine virtuelle pour stocker des données durables. Les partages de fichiers dans les fichiers Azure sont accessibles de la même façon que le disque local (par le biais des API du système de fichiers natif) et peuvent être partagés entre plusieurs machines virtuelles.  
 
Le tableau suivant compare les fichiers Azure et les disques de données Azure.  
 
||||  
|-|-|-|  
|**Attribut**|**Disques de données Azure**|**Azure Files**|  
|Scope|Exclusif à une seule machine virtuelle|Accès partagé entre plusieurs machines virtuelles|  
|Captures instantanées et copie|Oui|Non|  
|Configuration|Connexion au démarrage de la machine virtuelle|Connexion après le démarrage de la machine virtuelle|  
|Authentification|Intégration|Configuration avec net use|  
|Nettoyage|Automatique|Manuel|  
|Accès à l’aide de REST|Les fichiers du disque dur virtuel ne sont pas accessibles|Les fichiers stockés dans un partage sont accessibles|  
|Taille maximale|Disque de 1 To|Partage de fichiers de 5 To et fichier de 1 To au sein du partage|  
|E/S par seconde de 8 Ko max.|500 E/S par seconde|1 000 E/S par seconde|  
|Débit|Jusqu’à 60 Mo/s par disque|Jusqu’à 60 Mo/s par partage de fichiers|  

## <a name="next-steps"></a>Étapes suivantes

Lors des décisions concernant le stockage et l’accès de vos données, vous devez également prendre en considération les coûts impliqués. Pour plus d’informations, consultez [Tarification du stockage Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Certaines fonctionnalités SMB ne sont pas applicables au cloud. Pour plus d’informations, consultez [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Fonctionnalités non prises en charge par le service Azure File).
  
Pour plus d’informations sur les disques de données, consultez [Managing disks and image](storage-about-disks-and-vhds-linux.md) (Gestion des disques et des images) et [Comment attacher un disque de données à une machine virtuelle Windows](../virtual-machines/windows/classic/attach-disk.md).
