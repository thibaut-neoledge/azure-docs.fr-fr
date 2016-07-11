<properties
	pageTitle="Réplication Azure Storage | Microsoft Azure"
	description="Les données de votre compte de stockage Microsoft Azure sont répliquées à des fins de durabilité et de haute disponibilité. Les options de réplication incluent le stockage localement redondant (LRS), le stockage redondant dans une zone (ZRS), le stockage géo-redondant (GRS) et le stockage géo-redondant avec accès en lecture (RA-GRS)."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="tamram"/>

# Réplication Azure Storage

Les données de votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir une durabilité et une haute disponibilité, conformément au [contrat de niveau de service (SLA) Azure Storage](https://azure.microsoft.com/support/legal/sla/storage), même en cas de défaillances matérielles temporaires.

Lorsque vous créez un compte de stockage, vous devez sélectionner une des options de réplication suivantes :

- [Stockage localement redondant (LRS)](#locally-redundant-storage)
- [Stockage redondant dans une zone (ZRS)](#zone-redundant-storage)
- [Stockage géo-redondant (GRS)](#geo-redundant-storage)
- [Stockage géo-redondant avec accès en lecture (RA-GRS)](#read-access-geo-redundant-storage)

Le tableau suivant fournit une vue d’ensemble rapide des différences entre LRS, ZRS, GRS et RA-GRS, tandis que les sections suivantes décrivent chaque type de réplication plus en détail.


| Stratégie de réplication | LRS | ZRS | GRS | RA-GRS |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| Les données sont répliquées entre plusieurs installations. | Non | Oui | Oui | Oui |
| Les données peuvent être lues à partir de l’emplacement secondaire et principal. | Non | Non | Non | Oui |
| Nombre de copies de données conservées sur des nœuds distincts. | 3 | 3 | 6 | 6\. |

Consultez [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/) pour connaître les informations de tarification des différentes options de redondance.

## Stockage localement redondant

Le stockage localement redondant (LRS) réplique vos données dans la région dans laquelle vous avez créé votre compte de stockage. Pour maximiser la durabilité, chaque demande adressée aux données dans votre compte de stockage est répliquée trois fois. Ces trois réplicas se trouvent dans des domaines d’erreur et de mise à niveau distincts. Un domaine d’erreur est un groupe de nœuds qui représentent une unité physique d’incident et peuvent être considérés comme des nœuds appartenant au même rack physique. Un domaine de mise à niveau est un groupe de nœuds qui sont mis à jour ensemble au cours d’un processus de mise à niveau de service (déploiement). Les trois réplicas sont réparties sur des domaines d’erreur et de mise à jour afin de garantir que les données sont disponibles même en cas de défaillance matérielle d’un rack et lorsque les nœuds sont mis à niveau pendant un déploiement. Une demande est retournée avec succès uniquement après son écriture dans les trois réplicas.

Même si le stockage géo-redondant (GRS) est recommandé pour la plupart des applications, le stockage localement redondant peut être souhaitable dans certains scénarios :

- Le LRS est moins cher que le GRS et offre un débit supérieur. Si votre application stocke des données qui peuvent être recréées facilement, vous pouvez opter pour un stockage LRS.

- Certaines applications sont limitées à la réplication des données dans une seule région en raison des exigences de gouvernance des données.

- Si votre application a sa propre stratégie de géo-réplication, il est possible qu’elle n’ait pas besoin du GRS.


## Stockage redondant dans une zone

Le stockage redondant dans une zone (ZRS) réplique vos données dans deux ou trois installations au sein d’une même région ou dans deux régions distinctes, ce qui confère aux données une durabilité supérieure à celle offerte par le stockage LRS. Si le ZRS est activé pour votre compte de stockage, vos données restent durables, même en cas de défaillance de l’une des installations.


>[AZURE.NOTE]  ZRS est actuellement disponible uniquement pour les objets BLOB de blocs et est pris en charge uniquement dans les versions 2014-02-14 et versions ultérieures. Notez qu’une fois votre compte de stockage créé et la réplication ZRS sélectionnée, vous ne pouvez pas revenir en arrière et utiliser un autre type de réplication (et vice-versa).


## Stockage géo-redondant

Le stockage géo-redondant (GRS) réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire. Si le GRS est activé pour votre compte de stockage, vos données restent durables, même en cas de panne régionale totale ou d’incident empêchant la récupération depuis la région primaire.

Pour un compte de stockage avec GRS activé, une mise à jour est d’abord appliquée dans la région primaire, où elle est répliquée trois fois. La mise à jour est ensuite répliquée trois fois également dans la région secondaire, dans des domaines d’erreur et de mise à niveau distincts.

> [AZURE.NOTE] Avec GRS, les demandes d’écriture de données sont répliquées de manière asynchrone dans la région secondaire. Le mode de stockage GRS n’affecte pas la latence des demandes formulées dans la région primaire. Toutefois, étant donné que la réplication asynchrone implique un délai, il est possible que, en cas de sinistre régional, les modifications n’ayant pas encore été répliquées dans la région secondaire soient perdues, si les données ne peuvent pas être récupérées à partir de la région primaire.
 
Lorsque vous créez un compte de stockage, vous sélectionnez la région primaire pour le compte. La région secondaire est déterminée en fonction de la région primaire et ne peut pas être modifiée. Le tableau suivant montre les paires de régions primaires et secondaires.

| Primaire | Secondaire |
|---------------------|---------------------|
| États-Unis - partie centrale septentrionale | États-Unis - partie centrale méridionale |
| États-Unis - partie centrale méridionale | États-Unis - partie centrale septentrionale |
| Est des États-Unis | Ouest des États-Unis |
| Ouest des États-Unis | Est des États-Unis |
| Est des États-Unis 2 | Centre des États-Unis |
| Centre des États-Unis | Est des États-Unis 2 |
| Europe du Nord | Europe de l'Ouest |
| Europe de l'Ouest | Europe du Nord |
| Asie du Sud-Est | Est de l'Asie |
| Est de l'Asie | Asie du Sud-Est |
| Chine orientale | Chine du Nord |
| Chine du Nord | Chine orientale |
| Est du Japon | Ouest du Japon |
| Ouest du Japon | Est du Japon |
| Sud du Brésil | États-Unis - partie centrale méridionale |
| Est de l’Australie | Sud-est de l’Australie |
| Sud-est de l’Australie | Est de l’Australie |
| Sud de l'Inde | Inde-Centre |
| Inde-Centre | Sud de l'Inde |
| Gouvernement américain - Iowa | Gouvernement américain - Virginie |
| Gouvernement américain - Virginie | Gouvernement américain - Iowa |
| Centre du Canada | Est du Canada |
| Est du Canada | Centre du Canada |
| Nord du Royaume-Uni | Sud du Royaume-Uni 2 |
| Sud du Royaume-Uni 2 | Nord du Royaume-Uni |
| Centre de l’Allemagne | Nord-Est de l’Allemagne |
| Nord-Est de l’Allemagne | Centre de l’Allemagne |


Pour obtenir des informations récentes sur les régions prises en charge par Azure, consultez [Régions Azure](https://azure.microsoft.com/regions/).
 
## Stockage géo-redondant avec accès en lecture

Le stockage géo-redondant avec accès en lecture (RA-GRS) optimise la disponibilité de votre compte de stockage, en fournissant un accès en lecture seule aux données dans l’emplacement secondaire, en plus de la réplication entre les deux régions offerte par GRS. Dans le cas où les données sont indisponibles dans la région primaire, votre application peut lire des données à partir de la région secondaire.

Lorsque vous activez l’accès en lecture seule à vos données dans la région secondaire, vos données sont disponibles sur un point de terminaison secondaire, en plus du point de terminaison primaire pour votre compte de stockage. Le point de terminaison secondaire est similaire au point de terminaison principal, mais ajoute le suffixe `–secondary` au nom du compte. Par exemple, si votre point de terminaison principal pour le service d’objets blob est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les clés d’accès pour votre compte de stockage sont les mêmes pour le point de terminaison primaire et secondaire.

## Étapes suivantes

- [Tarification d’Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
- [À propos des comptes de stockage Azure](storage-create-storage-account.md)
- [Objectifs de performance et évolutivité d'Azure Storage](storage-scalability-targets.md)
- [Options de redondance de Microsoft Azure Storage et stockage géo-redondant avec accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Document SOSP - Azure Storage : service de stockage sur le cloud à haute disponibilité et à cohérence forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!---HONumber=AcomDC_0629_2016-->