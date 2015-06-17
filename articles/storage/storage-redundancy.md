
<properties 
  pageTitle="Options de redondance d'Azure Storage | Microsoft Azure" 
  description="Les données dans votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir une durabilité, ainsi qu’une haute disponibilité. En savoir plus sur les options de duplication pour votre compte de stockage Azure." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="03/20/2015" 
  ms.author="tamram"/>

# Options de redondance d'Azure Storage



Les données dans votre compte de stockage Microsoft Azure sont toujours répliquées pour garantir une durabilité, ainsi qu'une haute disponibilité, et répondre ainsi au [contrat de niveau de service Azure Storage](http://azure.microsoft.com/support/legal/sla/), y compris face aux défaillances matérielles temporaires. Pour plus d'informations sur la durabilité avec Azure Storage, consultez le [Document SOSP sur Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

Lorsque vous créez votre compte de stockage, vous devez sélectionner une des options de réplication suivantes :  

- **Stockage localement redondant (LRS).** Le stockage localement redondant conserve trois copies de vos données. LRS est répliqué trois fois au sein d'une installation unique dans une seule région. LRS protège vos données contre les défaillances matérielles normales, mais pas les défaillances d'une installation unique.
  
Vous pouvez bénéficier d'une réduction pour le stockage LRS. Pour une durabilité maximale, nous vous recommandons d'utiliser le stockage géo-redondant décrit ci-dessous.


- **Stockage redondant dans une zone (ZRS).** Le stockage redondant dans une zone conserve trois copies de vos données. ZRS est répliqué trois fois sur deux à trois installations, dans une même région ou deux régions, pour une meilleure durabilité que le stockage LRS. ZRS garantit que vos données restent durables dans une région unique.
Le stockage ZRS offre un niveau de durabilité supérieur à celui du stockage LRS ; toutefois, pour une durabilité maximale, nous vous recommandons d'utiliser le stockage géo-redondant décrit ci-dessous.  

	> [AZURE.NOTE] Actuellement, le stockage ZRS est uniquement disponible pour les objets blob de blocs.  
	Une fois votre compte de stockage créé et la réplication ZRS sélectionnée, vous ne pouvez pas revenir en arrière et utiliser un autre type de réplication (et vice-versa).
 


- **Stockage géo-redondant (GRS)**. Le stockage géo-redondant est activé par défaut sur votre compte de stockage lors de la création de celui-ci. Le stockage GRS effectue six copies de vos données. Avec le stockage GRS, vos données sont répliquées trois fois dans la région primaire et trois fois dans une région secondaire située à des centaines de kilomètres de la région primaire, ce qui confère aux données le plus haut niveau de durabilité disponible. En cas de défaillance dans la région primaire, Azure Storage bascule vers la région secondaire. Le stockage GRS assure la durabilité de vos données dans deux régions distinctes.


- **Stockage géo-redondant avec accès en lecture (RA-GRS)**. Le stockage géo-redondant avec accès en lecture réplique vos données dans un emplacement secondaire et permet d'y accéder en lecture. Le stockage géo-redondant avec accès en lecture offre un accès aux données, qu'elles se situent dans l'emplacement principal ou secondaire, si l'un des deux n'est pas disponible.

	> [AZURE.IMPORTANT] Vous pouvez modifier la façon dont vos données sont répliquées une fois que votre compte de stockage a été créé, mais notez que vous risquez de payer des frais de transfert supplémentaires ponctuels si vous basculez de LRS à GRS ou à RA-GRS. Si vous choisissez GRS lorsque vous créez votre compte, vous ne pouvez pas passer par la suite à un autre type de réplication et vice versa.
 

Le tableau suivant fournit une vue d'ensemble rapide des différences entre LRS, ZRS, GRS et RA-GRS, tandis que les sections suivantes décrivent chaque type de réplication plus en détail.


|Stratégie de réplication|LRS|ZRS|GRS|RA-GRS 
|--------------------|---|---|---|------
|Les données sont répliquées entre plusieurs installations|Non|Oui|Oui|Oui|
|Les données peuvent être lues à partir de l'emplacement secondaire et principal|Non|Non|Non|Oui
|Nombre de copies de données conservées sur des nœuds distincts|3|3|6|6
 

Pour plus d'informations sur la tarification relative à la réplication du compte de stockage, consultez la page [Tarification - Stockage.](http://azure.microsoft.com/pricing/details/storage/)

## Stockage localement redondant (LRS)

Le stockage localement redondant réplique vos données dans la région dans laquelle vous avez créé votre compte de stockage. Pour maximiser la durabilité, chaque demande adressée aux données dans votre compte de stockage est répliquée trois fois. Ces trois réplicas se trouvent dans des domaines d'erreur et de mise à niveau distincts. Un domaine d'erreur est un groupe de nœuds qui représentent une unité physique d'incident et peuvent être considérés comme des nœuds appartenant au même rack physique. Un domaine de mise à niveau est un groupe de nœuds qui sont mis à jour ensemble au cours du processus de mise à niveau de service (déploiement). Les trois réplicas sont réparties sur des domaines d'erreur et de mise à jour afin de garantir que les données sont disponibles même en cas de défaillance matérielle d'un rack et lorsque les nœuds sont mis à niveau pendant un déploiement. Une demande est retournée avec succès uniquement après son écriture dans les trois réplicas.

Même si le stockage géo-redondant est recommandé pour la plupart des applications, le stockage localement redondant peut être souhaitable dans certains scénarios :  

- Le LRS est moins cher que le GRS et offre un débit supérieur. Si votre application stocke des données qui peuvent être recréées facilement, vous pouvez opter pour un stockage LRS.

- Certaines applications sont limitées à la réplication des données dans une seule région en raison des exigences de gouvernance des données.

- Si votre application a sa propre stratégie de géo-réplication, il est possible qu'elle n'ait pas besoin du GRS.


## Stockage redondant dans une zone (ZRS)

Le stockage redondant dans une zone réplique vous données dans deux ou trois installations au sein d'une même région ou dans deux régions distinctes, ce qui confère aux données une durabilité supérieure à celle offerte par le stockage LRS. Si le ZRS est activé pour votre compte de stockage, vos données restent durables, même en cas de défaillance de l'une des installations.


>[AZURE.NOTE]  Actuellement, le stockage ZRS est uniquement disponible pour les objets blob de blocs. Notez qu'une fois votre compte de stockage créé et la réplication ZRS sélectionnée, vous ne pouvez pas revenir en arrière et utiliser un autre type de réplication (et vice-versa).


## Stockage géo-redondant (GRS).


Le stockage géo-redondant réplique vos données vers une région secondaire à des centaines de kilomètres de la région primaire. Si le GRS est activé pour votre compte de stockage, vos données restent durables, même en cas de panne régionale totale ou d'incident empêchant la récupération depuis la région primaire. 

Pour un compte de stockage avec GRS activé, une mise à jour est d'abord appliquée dans la région primaire, où elle est répliquée trois fois. La mise à jour est ensuite répliquée trois fois également dans la région secondaire, dans des domaines d'erreur et de mise à niveau distincts.

 
> [AZURE.NOTE] Avec GRS, les demandes d'écriture de données sont répliquées de manière asynchrone dans la région secondaire. Le mode de stockage GRS n'affecte pas la latence des demandes formulées dans la région primaire. Toutefois, étant donné que la réplication asynchrone implique un délai, il est possible que, en cas de sinistre régional, les modifications n'ayant pas encore été répliquées dans la région secondaire soient perdues, si les données ne peuvent pas être récupérées à partir de la région primaire.
 
Lorsque vous créez un compte de stockage, vous sélectionnez la région primaire pour le compte. La région secondaire est déterminée en fonction de la région primaire et ne peut pas être modifiée. Le tableau suivant montre les paires de régions primaires et secondaires :

|Primaire            |Secondaire        
| ---------------   |----------------
|Nord-Centre des États-Unis   |Sud-Centre des États-Unis
|Sud-Centre des États-Unis   |Nord-Centre des États-Unis
|Est des États-Unis            |Ouest des États-Unis        
|Ouest des États-Unis            |Est des États-Unis         
|Est des États-Unis 2          |Centre des États-Unis      
|Centre des États-Unis         |Est des États-Unis 2       
|Europe du Nord       |Europe de l'Ouest     
|Europe de l'Ouest        |Europe du Nord    
|Asie du Sud-Est    |Asie de l'Est       
|Asie de l'Est          |Asie du Sud-Est 
|Est de la Chine         |Nord de la Chine     
|Nord de la Chine        |Est de la Chine      
|Est du Japon         |Ouest du Japon      
|Ouest du Japon         |Est du Japon      
|Sud du Brésil       |Sud-Centre des États-Unis
|Est de l'Australie     |Sud-Est de l'Australie
|Sud-Est de l'Australie|Est de l'Australie  

 
## Stockage géo-redondant avec accès en lecture (RA-GRS)

Le stockage géo-redondant avec accès en lecture optimise la disponibilité de votre compte de stockage, en fournissant un accès en lecture seule aux données dans l'emplacement secondaire, en plus de la réplication entre les deux régions offerte par GRS. Dans le cas où les données sont indisponibles dans la région primaire, votre application peut lire des données à partir de la région secondaire.

Lorsque vous activez l'accès en lecture seule à vos données dans la région secondaire, vos données sont disponibles sur un point de terminaison secondaire, en plus du point de terminaison primaire pour votre compte de stockage. Le point de terminaison secondaire est similaire au point de terminaison primaire, mais ajoute le suffixe " -secondary " au nom du compte. Par exemple, si votre point de terminaison primaire pour le service BLOB est `myaccount.blob.core.windows.net`, votre point de terminaison secondaire est `myaccount-secondary.blob.core.windows.net`. Les clés d'accès pour votre compte de stockage sont les mêmes pour le point de terminaison primaire et secondaire.

## Étapes suivantes

- [Objectifs d'évolutivité et de performances d'Azure Storage](storage-scalability-targets.md) 
- [Options de redondance de Microsoft Azure Storage et stockage géo-redondant avec accès en lecture](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [Émulateur de stockage Microsoft Azure 3.1 avec RA-GRS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/08/microsoft-azure-storage-emulator-3-1-with-ra-grs.aspx)
- [Document SOSP sur Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)


<!--HONumber=52--> 