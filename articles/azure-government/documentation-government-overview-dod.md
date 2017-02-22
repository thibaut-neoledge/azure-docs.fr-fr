---
title: "Vue d’ensemble des informations du Ministère de la défense dans Azure Government| Microsoft Docs"
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 71e8742d71ce6ebab08988751a886df4df000f8a
ms.openlocfilehash: 4e815c5fce563155f7a747262a3695a5180706e3


---
# <a name="department-of-defense-dod-in-azure-government"></a>Ministère de la défense (DoD) dans Azure Government
## <a name="overview"></a>Vue d'ensemble
Azure Government est utilisé par des entités du Ministère américain de la défense (DoD) pour déployer un vaste éventail de charges de travail et solutions, dont des charges de travail décrites dans le <a href="http://iasecontent.disa.mil/cloud/SRG/index.html">guide du Ministère de la défense relatif aux exigences de sécurité en matière de cloud computing</a>, version 1, édition 2, au niveau d’impact 4 (L4) et au niveau d’impact 5 (L5).

Azure Government est le premier et le seul service cloud commercial hyperscale à recevoir une autorisation provisoire « Information Impact Level 5 » (informations de niveau d’impact 5) pour le ministère de la défense américain auprès de l’agence de défense des systèmes d'information (Defense Information Systems Agency). En outre, les régions Azure Government dédiées aux charges de travail clientes du ministère américain de la défense sont désormais disponibles au public.

L’une des principales motivations du Ministère de la défense pour migrer vers le cloud est de permettre aux organisations de se concentrer sur leurs missions et de minimiser la confusion liée à la création et à la gestion de solutions informatiques internes.

Les architectures cloud basées sur Azure Government permettent au personnel du Ministère de la défense de se concentrer sur des objectifs de mission, et de gérer des services informatiques de base, tels que SharePoint et d’autres charges de travail d’application.  Cela permet de réorienter les ressources informatiques critiques pour se concentrer sur le développement d’applications, l’analyse et divers problèmes de sécurité.

L’élasticité et la flexibilité qu’apporte Azure offrent des avantages considérables aux clients du Ministère de la défense. Il est plus simple, plus rapide et plus économique d’adapter l’échelle d’une charge de travail dans le cloud que de passer par les processus traditionnels d’approvisionnement en matériel et services dans le cadre du travail local, ou dans des centres de données du Ministère de la défense. Par exemple, l’acquisition de nouveau matériel multiserveur, même pour un environnement de test, peut prendre plusieurs mois et nécessiter l’approbation d’investissement considérables. En revanche, avec Azure, un test de migration pour une charge de travail existante peut être configuré en quelques jours ou semaines, et de façon économique (une fois le test est terminé, l’environnement peut être détruit sans autre coût).

Cette flexibilité est importante. En migrant vers Azure, les clients du Ministère de la défense ne font pas que réaliser des économies. Le cloud leur ouvre de nouvelles opportunités. Par exemple, il est facile à faire tourner un environnement de test pour découvrir de nouvelles technologies, ou de migrer une application et la tester dans Azure avant de valider son déploiement en mode production dans le cloud. Les responsables de mission peuvent explorer des options moins onéreuses plus facilement et sans risque.

La sécurité est un autre aspect clé. Si tout déploiement cloud nécessite une planification appropriée pour s’assurer de la fourniture sécurisée et fiable du service, en réalité les charges de travail basées sur le cloud les mieux configurées (jusqu’aux charges de travail de niveau L4 comprise) dans Azure Government sont plus sécurisées que bon nombre de déploiements traditionnels dans des centres de données et autres emplacements du Ministère de la défense. En effet, si les agences de défense disposent de l’expérience et des connaissances nécessaires pour sécuriser physiquement leurs ressources, la surface d’exposition informatique les confronte à d’autres défis. La cybersécurité est un domaine qui connaît une évolution constante et rapide, nécessitant des compétences d’experts et une capacité à rapidement développer et déployer des contre-mesures en fonction des besoins. La plateforme Azure, tant sur le plan commercial qu’administratif, prend désormais en charge des centaines de milliers de clients. Cette échelle permet à Microsoft de détecter rapidement les vecteurs d’attaque en constante évolution, puis de consacrer ses ressources au développement et à la mise en œuvre rapides des défenses appropriées.

## <a name="dod-region-qa"></a>Questions et réponses sur les régions DoD

### <a name="what-are-the-azure-government-dod-regions"></a>Que sont les régions DoD Azure Government ? 
Les régions DoD Est et Centre des États-Unis sont des régions physiquement séparées de Microsoft Azure conçues pour répondre aux exigences de sécurité du ministère américain de la défense (DoD) pour le cloud computing, en particulier pour les données classées « Impact Level 5 » (niveau d’impact 5) dans le guide du ministère de la défense relatif aux exigences de sécurité en matière de cloud computing.   

### <a name="what-is-the-difference-between-azure-government-and-the-azure-government-dod-regions"></a>Quelle est la différence entre Azure Government et les régions DoD Azure Government ? 
Azure Government est un cloud communautaire du gouvernement américain fournissant des services aux clients, communautés, entités soumises à ITAR et fournisseurs de solutions travaillant pour les autorités locales, fédérales et gouvernementales. Toutes les régions Azure Government sont conçues pour et répondent aux exigences de sécurité pour les données DoD Impact Level 5 (niveau d’impact 5) et les normes FedRAMP High.

Les régions Azure Government DoD sont conçues pour prendre en charge les exigences de séparation physique des données Impact Level 5 en fournissant une infrastructure de stockage et de calcul dédiée pour les clients DoD uniquement.  

#### <a name="what-is-the-difference-between-impact-level-4-and-impact-level-5-data"></a>Quelle est la différence entre les données Impact Level 4 et les données Impact Level 5 ?  
Les données Impact Level 4 sont des informations non classifiées contrôlées (CUI) qui peuvent inclure des données soumises au contrôle de l’exportation, des informations de confidentialité, des informations sanitaires protégées et d’autres données nécessitant une désignation CUI explicite (par exemple, Pour utilisation officielle uniquement, Données d’application de la loi sensibles, Informations de sécurité sensibles).

Les données Impact Level 5 comprennent les informations non classifiées, contrôlées (CUI) qui nécessitent un niveau supérieur de protection, comme jugé nécessaire par le propriétaire des informations, le droit public ou la loi.  Les données Impact Level 5 comprennent les systèmes de sécurité nationale non classifiés.  Vous trouverez plus d’informations sur les niveaux d’impact SRG et leurs exigences et caractéristiques respectives dans la section 3 du guide du Ministère de la défense relatif aux exigences de sécurité en matière de cloud computing.  

### <a name="what-data-is-categorized-as-impact-level-5"></a>Quelles données sont-elles considérées Impact Level 5 ? 
Les données Impact Level 5 comprennent les informations non classifiées, contrôlées (CUI) qui nécessitent un niveau supérieur de protection par rapport à Impact Level 4, comme jugé nécessaire par le propriétaire des informations, le droit public ou autres réglementations gouvernementales. Impact Level 5 prend également en charge les systèmes de sécurité nationale (NSS) non classifiés.  Ce niveau prend en charge les catégorisations d’informations NSS et CUI sur la base de CNSSI-1253, jusqu’à une confidentialité moyenne et une intégrité moyenne (M-M-x).

### <a name="what-is-microsoft-doing-differently-to-support-impact-level-5-data"></a>Qu’est-ce que Microsoft fait différemment pour prendre en charge les données Impact Level 5 ? 
Les données Impact Level 5, par définition, peuvent uniquement être traitées dans une infrastructure dédiée qui garantit la séparation physique des clients DoD et des clients d’organismes gouvernementaux non fédéraux.  En proposant ses services pour les régions DoD Est et Centre des États-Unis, Microsoft fournit un service exclusif pour les clients DoD qui répond à un niveau plus strict encore que les spécifications du DoD et dépasse le niveau de protection et de fonctionnalité offert par les autres solutions commerciales de cloud hyperscale.

### <a name="do-these-regions-support-classified-data-requirements"></a>Ces régions prennent-elles en charge les exigences pour les données classifiées ? 
Ces régions Azure Government DoD prennent uniquement en charge les données non classifiées jusqu’à Impact niveau 5 (inclus).  Les données Impact Level 6 sont définies comme des informations classifiées jusqu'au secret Défense.

### <a name="what-organizations-in-the-dod-can-use-the-azure-government-dod-regions"></a>Quelles organisations du DoD peuvent utiliser les régions Azure Government DoD ? 
Les régions DoD Est et Centre des États-Unis sont conçues pour prendre en charge la base de clients du ministère américain de la défense.  notamment :
* Le secrétariat de la défense
* Le comité des chefs d’état-major interarmées
* L’état-major
* Les organismes de défense
* Les activités de terrain du ministère de la défense
* L’armée de Terre
* La marine (y compris l’United States Marine Corps)
* L’armée de l’Air
* La Garde côtière
* Les commandements interarmes de combat
* Les autres départements, agences, activités et commandements sous le contrôle ou la surveillance d’une des entités approuvées susmentionnées

### <a name="are-the-dod-regions-more-secure"></a>Les régions DoD sont-elles plus sécurisées ? 
Microsoft gère tous ses centres de données Azure et l’infrastructure sous-jacente pour se conformer aux normes locales et internationales de sécurité et de conformité, et offre les meilleures plateformes cloud commerciales en termes de conformité et de résultats.  Ces nouvelles régions DoD offriront des garanties et engagements spécifiques pour respecter les exigences définies dans le SRG du DoD pour le cloud computing.

### <a name="why-are-there-multiple-dod-regions"></a>Pourquoi y a-t-il plusieurs régions DoD ? 
En ayant plusieurs régions DoD, Microsoft offre aux clients la possibilité d’élaborer leurs solutions de récupération d’urgence à travers les régions pour garantir la continuité d’activité et satisfaire aux exigences d’accréditation du système.  En outre, les clients peuvent optimiser les performances en déployant des solutions dans la zone géographique la plus proche de leur emplacement physique.

### <a name="are-these-dod-regions-connected-to-the-niprnet"></a>Ces régions DoD sont-elles connectées à NIPRNet ? 
Le DoD impose que les services cloud commerciaux utilisés pour les informations CUI soient connectés aux clients via un point d’accès cloud (CAP).  Par conséquent, les régions Azure DoD sont connectées à NIPRNet via des connexions redondantes à plusieurs points d’accès cloud répartis géographiquement.  Un CAP DoD est un système de dispositifs de protection et de surveillance des limites du réseau offrant une protection au réseau et aux services des systèmes d’informations du DoD.

### <a name="what-does-general-availability-mean"></a>Que signifie disponibilité générale ? 
Disponibilité générale signifie que les régions DoD dans Azure Government peuvent être utilisées pour soutenir des charges de travail de production et que les contrats SLA soutenus financièrement pour tous les services déployés dans les régions et également généralement disponibles seront pris en charge.

### <a name="how-does-a-dod-customer-acquire-azure-government-dod-services"></a>Comment un client du DoD obtient-il des services Azure Government DoD ? 
Les services Azure Government DoD peuvent être achetés par les entités qualifiées via les mêmes canaux de revendeurs que pour Azure Government.  Conformément à l’engagement de Microsoft pour simplifier la planification d’acquisition de services cloud et l’estimation des prix, la tarification pour les régions Azure Government DoD figurera dans l’outil de calcul de tarification au moment de la disponibilité générale.  Les services Azure Government DoD peuvent rapidement évoluer pour correspondre à vos besoins, vous ne payez que ce que vous utilisez.
Aucune modification contractuelle ne sera nécessaire pour les clients Contrat Entreprise utilisant déjà Azure Government.  

### <a name="how-are-the-dod-regions-priced"></a>Quelle est la tarification des régions DoD ? 
Les régions DoD utilisent une tarification basée sur la région.  Cela signifie que les coûts de service pour les clients validés du DoD reposent sur la région Azure Government dans laquelle vous exécutez vos charges de travail.  Pour plus des informations plus spécifiques sur la tarification, veuillez consulter votre responsable de compte Microsoft.  La tarification pour les régions DoD sera fournie via la calculatrice Azure.com à une date ultérieure.

### <a name="how-does-a-dod-organization-get-validated-for-the-azure-government-dod-regions"></a>Comment une organisation DoD est-elle validée pour les régions Azure Government DoD ? 
Pour accéder aux régions Azure DoD, les clients doivent suivre un processus de qualification préliminaire pour faire vérifier leur organisation et leur utilisation prévue de l’environnement Azure DoD.  Une fois le processus de qualification préliminaire terminé avec succès, Microsoft fournira au demandeur des instructions supplémentaires pour la création d’un abonnement, l’accès à l’environnement et la remise d’un contrôle d’accès basé sur les rôles pour les autres membres de l’organisation.

### <a name="can-independent-software-vendors-and-solution-providers-building-on-azure-deploy-solutions-in-the-azure-government-dod-regions"></a>Les éditeurs de logiciels et fournisseurs de solutions indépendants travaillant sur Azure peuvent-ils déployer des solutions dans les régions Azure Government DoD ? 
Les fournisseurs de solutions avec des offres de services cloud basés sur Azure peuvent opérer sur des solutions à un ou plusieurs locataires du DoD uniquement dans les régions Azure Government DoD.  Ces fournisseurs doivent d’abord prouver leur éligibilité en fournissant des preuves documentées d’un contrat avec une entité approuvée du DoD ou une lettre de parrainage d’une entité approuvée du DoD.  Les fournisseurs offrant des services dans les régions Azure Government DoD doivent inclure la défense du réseau d’ordinateurs, les rapports d’incidents et la vérification des antécédents du personnel pour exploiter des solutions gérant des informations Impact Level 5 dans leur offre.  Vous trouverez plus d’informations pour les fournisseurs de solutions dans le guide du Ministère de la défense relatif aux exigences de sécurité en matière de cloud computing.

### <a name="will-office-365-or-microsoft-dynamics-365-be-a-part-of-this-offering"></a>Office 365 et Microsoft Dynamics 365 feront-ils partie de cette offre ? 
Microsoft fournit des services Office 365 pour les DoD à Impact Level 5 conjointement à cette offre.  Dynamics 365 devrait proposer des services Impact Level 5 pour les régions Azure DoD à une date ultérieure.

### <a name="how-do-i-connect-to-the-dod-regions-once-i-have-a-subscription"></a>Comment puis-je me connecter aux régions DoD une fois que j’ai un abonnement ? 
Les régions DoD pour Azure Government sont disponibles via le portail de gestion Azure Government.  Les clients DoD approuvés verront les régions répertoriées en tant qu’options disponibles lors du déploiement des services disponibles.  Pour des instructions générales sur la gestion de vos abonnements Azure Government, consultez notre documentation.

### <a name="what-services-are-part-of-your-impact-level-5-accreditation-scope"></a>Quels services font partie de votre accréditation Impact Level 5 ? 
Azure est un service persistant dans lequel de nouveaux services et de nouvelles fonctionnalités sont ajoutés chaque semaine, et le nombre de services inclus augmente régulièrement.  Pour obtenir les informations les plus récentes à ce sujet, visitez le<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Centre de gestion de la confidentialité Microsoft.


## <a name="next-steps"></a>Étapes suivantes :
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA">Centre de gestion de la confidentialité Microsoft - page web sur le Ministère de la défense</a>

<a href="http://iasecontent.disa.mil/cloud/SRG/index.html"> Le guide du Ministère de la défense relatif aux exigences de sécurité en matière de cloud computing, version 1, édition 2 </a>

<a href="https://azure.microsoft.com/en-us/offers/azure-government/"> Canaux de revendeurs Azure Government

<a href="https://blogs.msdn.microsoft.com/azuregov/">Blog Microsoft Azure Government. </a>




<!--HONumber=Jan17_HO3-->


