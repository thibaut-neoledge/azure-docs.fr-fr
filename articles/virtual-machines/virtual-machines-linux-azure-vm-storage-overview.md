---
title: Stockage des machines virtuelles Azure et Linux | Microsoft Docs
description: "Décrit le Stockage Standard et Premium Azure avec des machines virtuelles Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/04/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bc18d25044fb790ef85ce950a785259cc1204fe4


---
# <a name="azure-and-linux-vm-storage"></a>Stockage des machines virtuelles Azure et Linux
Le service Stockage Azure est la solution de stockage cloud pour les applications récentes qui s’appuient sur la durabilité, la disponibilité et la scalabilité pour répondre aux besoins des clients.  Le Stockage Azure permet aux développeurs de créer des applications à grande échelle pour prendre en charge de nouveaux scénarios, et fournit également une base de stockage pour les Machines Virtuelles Azure.

## <a name="azure-storage-standard-and-premium"></a>Stockage Azure : Standard et Premium
Les machines virtuelles Azure peuvent reposer sur des disques de stockage Standard ou Premium.  Lorsque vous utilisez le portail pour choisir votre machine virtuelle, vous devez dérouler une liste sur l’écran Informations de base pour afficher les disques Standard et Premium.  La capture d’écran ci-dessous met en évidence ce menu déroulant.  Quand l’option SSD est sélectionnée, seules les machines virtuelles compatibles Stockage Premium, toutes assorties de lecteurs SSD, s’affichent.  Quand l’option disque dur est sélectionnée, les machines virtuelles compatibles Stockage Standard, assorties de lecteurs de disques rotatifs, s’affichent, ainsi que les machines virtuelles de Stockage Premium assorties de SSD.

  ![écran1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Lorsque vous créez une machine virtuelle à partir de l’interface `azure-cli`, vous pouvez choisir entre Standard et Premium pour la taille de la machine virtuelle en sélectionnant l’indicateur `-z` ou `--vm-size`.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Créer une machine virtuelle avec Stockage Standard sur l’interface CLI
L’indicateur CLI `-z` choisit Standard_A1, A1 étant une machine virtuelle Linux avec Stockage Standard.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Créer une machine virtuelle avec Stockage Premium sur l’interface CLI
L’indicateur CLI `-z` choisit Standard_DS1, DS1 étant une machine virtuelle Linux avec Stockage Premium.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Stockage Standard
Le Stockage Standard Azure est le type de stockage par défaut.  Le Stockage Standard est rentable tout en étant performant.  

## <a name="premium-storage"></a>Stockage Premium
Azure Premium Storage offre une prise en charge très performante et à faible latence des disques pour les machines virtuelles exécutant des charges de travail qui utilisent beaucoup d'E/S. Les disques de machine virtuelle qui utilisent Premium Storage stockent les données sur des disques SSD. Vous pouvez migrer les disques de machine virtuelle de votre application dans Azure Premium Storage pour tirer parti de la vitesse et des performances de ces disques.

Caractéristiques du Stockage Premium :

* Disques de stockage Premium : le Stockage Premium Azure prend en charge les disques de machines virtuelles pouvant être attachés à des machines virtuelles Azure des séries DS, DSv2 ou GS.
* Objet blob de pages Premium : le Stockage Premium prend en charge les objets blob de pages Azure, utilisés pour stocker les disques persistants des Machines Virtuelles Azure.
* Stockage Premium localement redondant : un compte de Stockage Premium prend uniquement en charge l’option Stockage localement redondant (LRS) pour la réplication et conserve trois copies des données dans une même région.
* [Stockage Premium](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Machines virtuelles non prises en charge par le Stockage Premium
Le Stockage Premium prend en charge les machines virtuelles Azure des séries DS, DSv2, GS et Fs. Vous pouvez utiliser des disques de Stockage Standard et Premium avec les machines virtuelles prises en charge par le Stockage Premium. Mais vous ne pouvez pas utiliser des disques de stockage Premium avec des séries de machines virtuelles non compatibles avec le Stockage Premium.

Les distributions Linux suivantes ont été validées avec le stockage Premium.

| Distribution | Version | Noyau pris en charge |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7.x, 8.x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+, 7.2+ | |

## <a name="file-storage"></a>Stockage Fichier
Le stockage de fichiers Azure propose des partages de fichiers dans le cloud s’appuyant sur le protocole SMB standard. Avec Azure Files, vous pouvez migrer des applications d’entreprise qui s’appuient sur des serveurs de fichiers dans Azure. Les applications exécutées dans Azure permettent de monter facilement des partages de fichiers à partir de machines virtuelles Azure exécutées sous Linux. Et avec la dernière version du stockage de fichiers, vous pouvez également monter un partage de fichiers à partir d’une application locale prenant en charge SMB 3.0.  Étant donné que les partages de fichiers sont des partages SMB, vous pouvez y accéder via les API de système de fichiers standard.

File Storage repose sur la même technologie que Blob Storage, Table Storage et Queue Storage : il offre donc la disponibilité, la durabilité, l’évolutivité et la redondance géographique intégrées à la plateforme de stockage Azure. Pour plus d’informations sur les objectifs et les limites des performances du Stockage Fichier, consultez Objectifs d’évolutivité et de performances du Stockage Azure.

* [Utilisation du stockage de fichiers Azure avec Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Stockage chaud
Le niveau de stockage à chaud Azure est optimisé pour le stockage des données souvent sollicitées.  Le stockage à chaud est le type de stockage par défaut pour les magasins d’objets blob.

## <a name="cool-storage"></a>Stockage à froid
Le niveau de stockage à froid Azure est optimisé pour le stockage des données rarement sollicitées et durables. Parmi les exemples d’utilisation du stockage à froid, citons les sauvegardes, le contenu multimédia, les données scientifiques, la conformité et les données d’archivage. En général, toutes les données auxquelles vous accédez rarement sont des candidats idéaux au stockage à froid.

|  | niveau de stockage chaud | niveau de stockage froid |
|:--- |:---:|:---:|
| Availability |99,9 % |99 % |
| Disponibilité (lectures RA-GRS) |99,99 % |99,9 % |
| Frais d'utilisation |Coûts de stockage supérieurs |Coûts de stockage inférieurs |
| Accès inférieur |Accès supérieur | |
| et coûts de transaction |et coûts de transaction | |

## <a name="redundancy"></a>Redondance
Les données dans votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir une durabilité, ainsi qu’une haute disponibilité, et répondre ainsi au contrat de niveau de service Azure Storage, y compris face aux défaillances matérielles temporaires.

Lorsque vous créez un compte de stockage, vous devez sélectionner une des options de réplication suivantes :

* Stockage localement redondant (LRS)
* Stockage redondant dans une zone (ZRS)
* Stockage géo-redondant (GRS)
* Stockage géo-redondant avec accès en lecture (RA-GRS)

### <a name="locally-redundant-storage"></a>Stockage localement redondant
Le stockage localement redondant (LRS) réplique vos données dans la région dans laquelle vous avez créé votre compte de stockage. Pour maximiser la durabilité, chaque demande adressée aux données dans votre compte de stockage est répliquée trois fois. Ces trois réplicas se trouvent dans des domaines d’erreur et de mise à niveau distincts.  Une demande est retournée avec succès uniquement après son écriture dans les trois réplicas.

### <a name="zone-redundant-storage"></a>Stockage redondant dans une zone
Le stockage redondant dans une zone (ZRS) réplique vos données dans deux ou trois installations au sein d’une même région ou dans deux régions distinctes, ce qui confère aux données une durabilité supérieure à celle offerte par le stockage LRS. Si le ZRS est activé pour votre compte de stockage, vos données restent durables, même en cas de défaillance de l’une des installations.

### <a name="geo-redundant-storage"></a>Stockage géo-redondant
Le stockage géo-redondant (GRS) réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire. Si le GRS est activé pour votre compte de stockage, vos données restent durables, même en cas de panne régionale totale ou d’incident empêchant la récupération depuis la région primaire.

### <a name="read-access-geo-redundant-storage"></a>Stockage géo-redondant avec accès en lecture
Le stockage géo-redondant avec accès en lecture (RA-GRS) optimise la disponibilité de votre compte de stockage, en fournissant un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la réplication entre les deux régions offerte par GRS. Dans le cas où les données sont indisponibles dans la région primaire, votre application peut lire des données à partir de la région secondaire.

Pour une présentation approfondie de la redondance du Stockage Azure, consultez :

* [Réplication Azure Storage](../storage/storage-redundancy.md)

## <a name="scalability"></a>Extensibilité
Azure Storage est massivement évolutif, ce qui vous permet de stocker et de traiter des centaines de téraoctets de données pour prendre en charge les scénarios Big Data exigés par les analyses scientifiques ou financières et les applications multimédias. Vous pouvez également stocker les petites quantités de données requises pour un petit site Web professionnel. Quels que soient vos besoins, vous payez uniquement pour les données que vous stockez. Azure Storage stocke actuellement des dizaines de billions d'objets client uniques et traite en moyenne des millions de requêtes par seconde.

Pour les comptes de stockage Standard : un compte de stockage Standard a un taux de demandes total maximal de 20 000 opérations d’E/S par seconde. Le nombre d'opérations d'E/S par seconde sur l'ensemble de vos disques de machine virtuelle dans un compte de stockage standard ne doit pas dépasser cette limite.

Pour les comptes de stockage Premium : un compte de stockage Premium a un débit total maximum de 50 Gbit/s. Le débit total sur l'ensemble de vos disques de machine virtuelle ne doit pas dépasser cette limite.

## <a name="availability"></a>Availability
Nous garantissons que, pendant au moins 99,99 % du temps (99,9 % pour le niveau d’accès Froid), nous serons en mesure de traiter les demandes de lecture de données émanant de comptes de stockage géo-redondant avec accès en lecture (RA-GRS), à condition que, en cas d’échec de lecture des données à partir de la région primaire, de nouvelles tentatives soient effectuées dans la région secondaire.

Nous garantissons que, pendant au moins 99,9 % du temps (99 % pour le niveau d’accès Froid), nous serons en mesure de traiter les demandes de lecture de données sur des comptes de stockage localement redondant (LRS), de stockage redondant dans une zone (ZRS) et de stockage géo-redondant (GRS).

Nous garantissons que, pendant au moins 99,9 % du temps (99 % pour le niveau d’accès Froid), nous serons en mesure de traiter les demandes d’écriture de données sur des comptes de stockage localement redondant (LRS), de stockage redondant dans une zone (ZRS), de stockage géo-redondant (GRS) et de stockage géo-redondant avec accès en lecture (RA-GRS).

* [Contrat SLA Azure pour le stockage](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>Régions
Azure est mis en disponibilité générale dans 30 régions du monde et quatre autres régions seront bientôt annoncées. L’expansion géographique constitue une priorité pour Azure car elle permet à nos clients d’atteindre de meilleures performances. De plus, elle permet la prise en charge de leurs exigences et préférences en termes d’emplacement des données.  La dernière région Azure à lancer se trouve en Allemagne.

Microsoft Cloud Allemagne représente une option différente des services cloud Microsoft déjà disponibles en Europe, qui crée de meilleures possibilités d’innovation et de croissance économique pour les partenaires et clients soumis à des réglementations strictes en Allemagne, dans l’Union européenne (UE) et au sein de l’Association européenne de libre-échange (AELE).

Les données clients de ces nouveaux centres de données, à Magdebourg et Frankfort, sont gérées sous le contrôle d’un tiers de confiance, T-Systems International, une société allemande indépendante et filiale de Deutsche Telekom. Les services cloud commerciaux de Microsoft dans ces centres de données adhèrent aux réglementations allemandes en matière de gestion des données et offrent aux clients des choix supplémentaires sur la manière et l’endroit où sont traitées les données.

* [Carte des régions Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Sécurité
Azure Storage propose un ensemble complet de fonctionnalités de sécurité dont l’utilisation combinée permet aux développeurs de créer des applications sécurisées. Le compte de stockage proprement dit peut être sécurisé à l’aide du contrôle d’accès en fonction du rôle et d’Azure Active Directory. Les données peuvent être sécurisées en transit entre une application et Azure au moyen du chiffrement côté client, de HTTPS ou de SMB 3.0. Les données peuvent être définies de façon à être automatiquement chiffrées au moment où elles sont écrites dans le Stockage Azure en utilisant Storage Service Encryption (SSE). Les disques de système d’exploitation et de données utilisés par les machines virtuelles peuvent être définis de façon à être chiffrés à l’aide d’Azure Disk Encryption. Il est possible d’accorder un accès délégué aux objets de données d’Azure Storage en utilisant des signatures d’accès partagé.

### <a name="management-plane-security"></a>Sécurité du plan de gestion
Le plan de gestion se compose des ressources utilisées pour gérer votre compte de stockage. Dans cette section, nous allons parler du modèle de déploiement Azure Resource Manager et de la façon d’utiliser le contrôle d’accès en fonction du rôle (RBAC) pour contrôler l’accès à vos comptes de stockage. Nous évoquerons aussi la gestion des clés de compte de stockage et vous expliquerons comment les régénérer.

### <a name="data-plane-security"></a>Sécurité du plan de données
Dans cette section, nous verrons comment autoriser l’accès aux objets de données réels de votre compte de stockage (objets blob, fichiers, files d’attente et tables) en utilisant des signatures d’accès partagé et des stratégies d’accès stockées. Nous évoquerons à la fois les signatures d’accès partagé (SAP) au niveau des services et les signatures d’accès partagé au niveau des comptes. Nous verrons aussi comment limiter l’accès à une adresse IP spécifique (ou à une plage d’adresses IP), comment limiter le protocole utilisé pour HTTPS et comment révoquer une signature d’accès partagé sans attendre son expiration.

## <a name="encryption-in-transit"></a>Chiffrement en transit
Cette section explique comment sécuriser les données pendant leur transfert vers et à partir d’Azure Storage. Nous vous livrerons des recommandations concernant l’utilisation de HTTPS et vous parlerons du chiffrement utilisé par SMB 3.0 pour les partages de fichiers Azure. Nous nous intéresserons aussi au chiffrement côté client, qui vous permet de chiffrer les données avant leur transfert vers Storage dans une application cliente et de déchiffrer les données après leur transfert à partir de Storage.

## <a name="encryption-at-rest"></a>Chiffrement au repos
Nous nous pencherons sur Storage Service Encryption (SSE) et sur la procédure d’activation de ce service pour un compte de stockage, qui a pour effet de chiffrer automatiquement les objets blob de bloc, les objets blog de pages et les objets blob d’ajout quand ils sont écrits dans Azure Storage. Nous verrons aussi comment utiliser Azure Disk Encryption et explorerons les différences fondamentales entre Disk Encryption, SSE et le chiffrement côté client, ainsi que des cas d’utilisation. Nous nous pencherons brièvement sur la conformité aux normes FIPS des ordinateurs de l’administration américaine.

* [Guide de sécurité du Stockage Azure](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Réduction des coûts
* [Coût de stockage](https://azure.microsoft.com/pricing/details/storage/)
* [Calcul des coûts de stockage](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limites de stockage
* [Limites de service de stockage](../azure-subscription-service-limits.md#storage-limits)




<!--HONumber=Nov16_HO3-->


