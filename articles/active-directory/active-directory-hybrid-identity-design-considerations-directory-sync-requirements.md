<properties
	pageTitle="Considérations relatives à la conception d'identités hybrides Azure Active Directory : déterminer les exigences en matière de synchronisation d'annuaire | Microsoft Azure"
	description="Identifier les exigences nécessaires pour la synchronisation de tous les utilisateurs entre en local et cloud pour l'entreprise."
	documentationCenter=""
	services="active-directory"
	authors="billmath"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="billmath"/>

# Déterminer les exigences de synchronisation de répertoire
La synchronisation consiste à fournir aux utilisateurs une identité dans le cloud basée sur leur identité locale. Qu'ils comptent utiliser le compte synchronisé pour l'authentification ou pour l'authentification fédérée, les utilisateurs doivent toujours avoir une identité dans le cloud. Cette identité devra être gérée et mise à jour de temps en temps. Les mises à jour peuvent prendre différentes formes, des modifications de titre aux modifications de mot de passe.

Commencez par évaluer la solution d'identité locale des organisations et les exigences des utilisateurs. Cette évaluation est importante pour définir les exigences techniques quant à la manière dont les identités d'utilisateur seront créées et gérées dans le cloud. Pour la plupart des organisations, Active Directory est local et sera l'annuaire local à partir duquel les utilisateurs seront synchronisés, ce qui ne sera cependant pas toujours le cas.

Veillez à répondre aux questions suivantes :


- Combien avez-vous de forêts AD: une, plusieurs ou aucune ?
 - Vers combien d'annuaires Azure AD allez-vous synchroniser ?
 
    1. Utilisez-vous le filtrage ?
    2. Avez-vous prévu plusieurs serveurs Azure AD Connect ?
  
- Avez-vous actuellement un outil de synchronisation local ?
  - Si oui, vos utilisateurs bénéficient-ils d'un annuaire/d'une intégration virtuels d'identités ?
- Avez-vous un autre annuaire local que vous voulez synchroniser (par exemple, annuaire LDAP, base de données des ressources humaines, etc.) ?
  - Procéderez-vous à une synchronisation de la liste d’adresses globale (GALSync) ?
  - Quel est l'état actuel des UPN dans votre organisation ? 
  - Avez-vous un annuaire différent pour l'authentification des utilisateurs ?
  - Votre entreprise utilise-t-elle Microsoft Exchange ?
    - Le déploiement d'Exchange hybride est-il prévu ? 
   
Maintenant que vous avez une idée des exigences de votre entreprise en termes de synchronisation, vous devez évaluer les applications qui utilisent ces services d'annuaire. Cette évaluation est importante pour définir les exigences techniques qui permettront d'intégrer ces applications dans le cloud. Veillez à répondre aux questions suivantes :

- Ces applications migreront-elles vers le cloud et utiliseront-elles l'annuaire ?
- Existe-t-il des attributs spéciaux qui doivent être synchronisées vers le cloud afin que ces applications puissent les utiliser correctement ?
- Ces applications devront-elles être réécrites pour tirer parti de l'authentification par le cloud ?
- Ces applications continueront-elles à résider en local alors que les utilisateurs y accéderont à l'aide de l'identité de cloud ?

Vous devez également déterminer la synchronisation des annuaires d'exigences et de contraintes de sécurité. Cette évaluation est importante pour obtenir une liste des exigences qui seront nécessaires pour créer et gérer des identités d'utilisateurs dans le cloud. Veillez à répondre aux questions suivantes :

- Où se trouvera le serveur de synchronisation ?
- Sera-t-il lié à un domaine ?
- Le serveur se trouvera-t-il sur un réseau restreint derrière un pare-feu, par exemple une zone DMZ ?
  - Pourrez-vous ouvrir les ports de pare-feu requis pour prendre en charge la synchronisation ?
- Avez-vous un plan de récupération d'urgence pour le serveur de synchronisation ?
- Avez-vous un compte disposant des autorisations appropriées pour toutes les forêts avec lesquelles vous souhaitez synchroniser ?
 - Si votre entreprise ne connaît pas la réponse à cette question, consultez la section « Autorisations pour la synchronisation de mot de passe » dans l'article [Installer le Service de synchronisation Azure Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) et déterminez si vous avez déjà un compte disposant de ces autorisations, ou si vous devez en créer un.
- Si vous disposez d'une synchronisation à forêts multiples, le serveur de synchronisation est-il en mesure d'atteindre chaque forêt ?
 
>[AZURE.NOTE]Veillez à noter chaque réponse et à comprendre le raisonnement derrière la réponse. La section [Déterminer les exigences de réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) passe en revue les options disponibles. En répondant à ces questions, vous sélectionnerez l’option la mieux adaptée à vos besoins métier.

## Étapes suivantes
[Déterminer les exigences d’authentification multifacteur](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## Voir aussi
[Présentation des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-directory-overview.md)

<!---HONumber=Nov15_HO3-->