---
title: "Options de migration hors d’Azure RemoteApp | Microsoft Docs"
description: "Découvrez les options de migration hors d’Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c4e0e5bc-5c13-4487-b1b6-ebf2a5edc1f0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 7c75fd870efb93bcf6c69ccf8a8ea9b56288c749
ms.openlocfilehash: 0744b317f595786b91afce3c9e9d59f64ceef25d


---
# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Options de migration hors d’Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Si vous avez cessé d’utiliser RemoteApp du fait de [l’annonce de la suppression](https://go.microsoft.com/fwlink/?linkid=821148) ou parce que vous avez terminé votre évaluation, vous devez migrer d’Azure RemoteApp vers un autre service d’application. Il existe deux approches différentes pour la migration : le déploiement autogéré (souvent appelé Infrastructure en tant que Service [IaaS]) ou une offre de plateforme entièrement gérée (souvent appelée Plateforme ou Logiciel en tant que service [PaaS/SaaS]). 

L’IaaS en libre-service est un déploiement personnalisable que vous gérez, exploitez et possédez, directement déployé sur des machines virtuelles (VM) ou des systèmes physiques. En face, une offre de PaaS/SaaS entièrement géré ressemble davantage à Azure RemoteApp : un partenaire fournit une couche de service sur une solution d’accès distant qui gère les opérations et la maintenance, pendant que vous, en tant que client, effectuez la gestion des images et applications.

[Consultez les webinaires Azure RemoteApp sur les options de migration](https://social.msdn.microsoft.com/Forums/azure/40557aaa-3e9f-403c-b221-ad3eac10dc56/migration-option-webinar-recordings?forum=AzureRemoteApp), ou lisez la suite pour plus d’informations (y compris des exemples des différentes options d’hébergement).

## <a name="self-managed-iaas-solutions"></a>Solutions autogérées (IaaS)
### <a name="rds-on-iaas"></a>**RDS sur IaaS**
Vous pouvez déployer un déploiement de services Bureau à distance natif basé sur les sessions (dans Windows Server) à l’aide de RemoteApp sur des postes de travail locaux ou dans un environnement hébergé (comme sur les machines virtuelles Azure). Les services Bureau à Distance sur les déploiements IaaS sont parfaits pour les clients déjà familiarisés avec cette solution et qui ont une expertise technique existante pour les déploiements de services Bureau à Distance. 

> [!NOTE]
> Vous avez besoin de licences en volume avec Software Assurance (SA) pour les licences d’accès client aux services Bureau à distance pour utiliser cette option de déploiement.
> 
> 

Le déploiement des services Bureau à Distance sur des machines virtuelles Azure est plus facile que jamais lorsque vous utilisez le déploiement et des modèles d’application de correctifs (lisez la [présentation](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) puis [récupérez-les](https://aka.ms/rdautomation)). Vous pouvez obtenir les mêmes fonctionnalités de mise à l’échelle flexibles avec des ressources de modèle de déploiement Azure Classic (et non des ressources de modèle de ressource Azure) dans Azure RemoteApp à l’aide du [script de mise à l’échelle automatique](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), bien qu’il existe davantage de personnalisations et de configurations. Lorsque vous déployez des services Bureau à Distance sur des machines virtuelles Azure, le support technique est fourni via le [Support Azure](https://azure.microsoft.com/support/plans/), les mêmes professionnels d’assistance technique qui vous ont assisté avec Azure RemoteApp. Vous pouvez obtenir des estimations de coût en fonction de votre utilisation existante en contactant le [Support Azure](https://azure.microsoft.com/support/plans/), ou vous pouvez effectuer les calculs vous-même à l’aide de notre calculateur de coûts à venir.  En outre, avec les machines virtuelles de série N (actuellement en version préliminaire privée), vous pouvez ajouter des vGPU - Pour plus d’informations sur l’ajout de vGPU et sur la [prise en main des améliorations des services de Bureau à distance dans Windows Server 2016](https://myignite.microsoft.com/videos/2794), consultez notre session Ignite.   

Nous proposons des guides de déploiement étape par étape pour [Windows Server 2012 R2](http://aka.ms/rdsonazure) et [Windows Server 2016](http://aka.ms/rdsonazure2016) afin de faciliter votre déploiement. Consultez le [blog du Bureau à distance](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) pour les dernières informations.

### <a name="citrix-on-iaas"></a>**Citrix sur IaaS**
Un déploiement Citrix natif de XenApp ou de XenDesktop basé sur les sessions peut être déployé localement ou dans un environnement hébergé (par exemple sur des machines virtuelles Azure). 

Consultez le guide de déploiement étape par étape, [Citrix XA 7.6 sur Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), pour plus d’informations. En savoir plus sur [Citrix sur Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), avec notamment un calculateur de prix. Vous pouvez également rechercher un [contact Citrix](http://citrix.com/English/contact/index.asp) pour discuter de vos options.

## <a name="fully-managed-paassaas-offerings"></a>Offres entièrement gérés (PaaS/SaaS)
### <a name="citrix-cloud"></a>**Citrix Cloud**
[Solution cloud Citrix existante](https://www.citrix.com/products/citrix-cloud/), identique à une architecture à Citrix XenApp Express. Citrix offre une [réduction de&50; %](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) pour les clients Azure RemoteApp existants. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (en version préliminaire technique)**
[Inscrivez-vous à leur présentation technique](http://now.citrix.com/remoteapp), et regardez leur [session Ignite](https://myignite.microsoft.com/videos/2792) (commençant à 20:30). XenApp Express est une architecture identique à Citrix Cloud, à la différence qu’elle inclut une interface utilisateur de gestion simplifiée et d’autres fonctionnalités et capacités qui sont semblables à celles d’Azure RemoteApp. 

En savoir plus sur [Citrix XenApp Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programme de fournisseur de services de Citrix**
Le programme de fournisseur de services de Citrix permet aux fournisseurs de services d’offrir la simplicité du cloud computing virtuel aux petites et moyennes entreprises, en leur offrant les services souhaités dans un modèle simple et proposant un paiement à l’utilisation. Les fournisseurs de services Citrix développent leurs entreprises Microsoft SPLA et prolongent leurs investissements sur la plateforme de services de Bureau à distance sur tout appareil, avec un accès en tout lieu, la meilleure prise en charge d’applications disponible, une expérience riche, une meilleure sécurité et une évolutivité accrue. Ainsi, les fournisseurs de services de Citrix attirent plus d’abonnés, améliorent la satisfaction des clients et réduisent les coûts d’exploitation. [En savoir plus](http://www.citrix.com/products/service-providers.html) ou [trouver un partenaire](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Fournisseur de services hébergés Microsoft**
Les partenaires d’hébergement offrent généralement un service hébergé entièrement géré de service d’application et bureau Windows, ce qui peut inclure la gestion des ressources Azure, des systèmes d’exploitation, des applications et du support technique en fonction des contrats de licence du partenaire avec Microsoft et d’autres fournisseurs de logiciels, ainsi qu’un contrat de licence de fournisseur de services pour permettre la revente de la licence d’accès SAL. Les informations suivantes fournissent des détails et des informations de contact pour certains des hébergeurs spécialisés dans l’assistance aux clients pour leur migration Azure RemoteApp. Découvrez [la liste actuelle des fournisseurs de services hébergés](http://aka.ms/rdsonazurecertified) qui ont effectué le parcours d’apprentissage et l’évaluation de RDS sur IaaS.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) se spécialise dans les éditeurs de logiciels en transition vers le cloud et ceux cherchant à optimiser leurs configurations cloud actuelles. ASPEX offre un large éventail de services gérés, d’opérations de développement et de conseil.  

Emplacement principal : Anvers, Belgique

Région d’activité : Europe de l’Ouest

Statut du partenaire : [Silver](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Fournisseur de services Microsoft : Oui

Proposer des solutions RemoteApp et Desktop basées sur les sessions : Oui, les deux

Solutions de migration Azure RemoteApp : Oui, [en savoir plus](https://www.aspex.be/en/azure-remote-apps)

**Contact :**

* Téléphone  : +3232202198
* Mail : [info@aspex.be](mailto:info@aspex.be)
* Web : [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (nom de la plateforme : MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) est une plateforme d’automatisation pour les entreprises informatiques qui souhaitent simplifier, optimiser et mettre à l’échelle la migration et la livraison de bureaux à distance, d’applications à distance et d’infrastructures dans le cloud Microsoft Azure. 

La plateforme MyCloudIT réduit le temps nécessaire au déploiement de 95 %, les coûts liés à Azure de 30 %, et déplace l’infrastructure informatique entière de leurs clients dans le cloud en quelques clics. Les partenaires peuvent désormais gérer les clients à partir d’un tableau de bord global, les utilisateurs finaux de service à travers le monde entier comme jamais auparavant et augmenter leur chiffre d’affaires sans ajouter de surcharge supplémentaire ou nécessiter de formation approfondie à Azure.  

Emplacement principal : Dallas, Texas, États-Unis

Région de l’opération  : monde entier

Statut du partenaire : [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Fournisseur de services Microsoft : Oui

Proposer des solutions RemoteApp et Desktop basées sur les sessions : Oui, les deux

Solutions de migration Azure RemoteApp : Oui, [en savoir plus](https://mycloudit.com/remote-app-microsoft/)

**Contact :**

* Brian Garoutte, vice-président du développement commercial
  
   Téléphone : 972-218-0741
  
   E-mail : [brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) se spécialise dans la fourniture de solutions de bureaux hébergés, en livrant des expériences complètes d’application de bureau et d’éditeurs de logiciels construites sur la technologie Microsoft pour une base de clients globale, à partir d’Azure et leurs propres centres de données.

Emplacements principaux : Londres, Royaume-Uni ; Singapour ; Houston, Texas

Région de l’opération  : monde entier

Statut du partenaire : Gold

Fournisseur de services Microsoft : Oui

Proposer des solutions RemoteApp et Desktop basées sur les sessions : Oui, les deux

Solutions de migration Azure RemoteApp : Oui, [en savoir plus](http://www.acuutech.com/ara-migration/)

**Contact :**

* Royaume-Uni :
  
  5/6 York maison, Langston Road,
  
  Loughton, Essex IG10 3TQ
  
  Téléphone : +44 (0) 20 8502 2155
* Singapour :
  
  100 Cecil Street, #09-02, 
  
  The Globe, Singapore 069532
  
  Téléphone : +65 6709 4933
* Amérique du Nord : 
  
  3601 S. Sandman St.
  
  Suite 200, Houston, TX 77098
  
  Téléphone : +1 713 691 0800

#### <a name="saasplaza"></a>**SaaSplaza**
[SaaSplaza](http://www.saasplaza.com/) propose des services cloud privés et publics (Azure) pour toute la gamme Microsoft Dynamics (NAV, AX, GP, SL, CRM).

Emplacement principal : Pays-Bas

Région de l’opération : monde entier

Statut du partenaire : [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/saasplaza_4295495801/791011_2?k=saasplaza)

Fournisseur de services Microsoft : Oui

Proposer des solutions RemoteApp et Desktop basées sur les sessions : Oui, les deux

**Contact :**

- Europe/Moyen-Orient/Afrique :

   Prins Mauritslaan 29-35

   71 LP Badhoevedorp

   Pays-Bas

   Téléphone : +31 20 547 8060 

- Amérique :

   171 Saxony Road, Suite 105

   Encinitas, CA 92024

   San Diego

   États-Unis

   Téléphone : +1 858 385 8900 

- Asie-Pacifique :

   105 Cecil Street
   
   \#11-08, The Octagon

   Singapour 069534

   Singapour
   
   Téléphone - Singapour : +65 6222 6591

   Téléphone - Australie : +61 2 8310 5568 
   
   Téléphone - Nouvelle-Zélande : +64 4 488 0321

## <a name="need-more-help"></a>Besoin de plus d’aide ?
Toujours besoin d’aide pour choisir ou des questions supplémentaires ? Utilisez l’une des méthodes suivantes pour obtenir de l’aide. 

1. Envoyez-nous un e-mail à l’adresse [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. Contacter le [support Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Commencez par créer un [ticket d’assistance Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3. Appelez-nous. [Rechercher le numéro de téléphone d'un service commercial local](https://azure.microsoft.com/overview/sales-number/).




<!--HONumber=Feb17_HO2-->


