
<properties 
	pageTitle="Utilisation d’un groupe pour gérer l’accès aux applications SaaS| Microsoft Azure" 
	description="Une rubrique vous expliquant comment utiliser des groupes dans Azure AD Premium pour attribuer l’accès à des applications SaaS intégrées avec Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="swadhwa" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" identity
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>


# Utilisation d’un groupe pour gérer l’accès aux applications SaaS

Avec Azure AD Premium, vous pouvez utiliser les groupes pour attribuer l’accès à une application SaaS intégrée avec Azure AD. Par exemple, si vous souhaitez attribuer l’accès à différentes applications SaaS au service marketing, vous pouvez créer un groupe comportant les utilisateurs du service marketing, puis allouer ce groupe aux cinq applications SaaS dont il a besoin. De cette manière, vous pouvez gagner du temps en gérant de manière centralisée l’adhésion du service marketing. Ensuite, les utilisateurs sont affectés à l’application lorsqu’ils sont ajoutés en tant que membres du groupe marketing. Leurs attributions sont retirées de l’application lors de leur suppression du groupe marketing.

Cette fonctionnalité peut être utilisée avec des centaines d’applications pouvant être ajoutées à partir de la galerie d’applications Azure AD.

**Pour attribuer à un groupe l’accès à une application SaaS**


1. Ouvrez le navigateur de votre choix et accédez au portail de gestion Azure. Dans la barre de navigation à gauche du portail de gestion Azure, recherchez l’extension Active Directory. Sous l’onglet **Répertoire**, cliquez sur le répertoire dans lequel vous souhaitez attribuer l’accès à un groupe à une application SaaS.


2. Cliquez sur l’onglet Applications associé à votre répertoire. Cliquez sur une application ajoutée à partir de la galerie d’applications, puis cliquez sur l’onglet Utilisateurs et groupes.

3. Sur l’onglet Utilisateurs et groupes, dans le champ Commencer par, saisissez le nom du groupe auquel vous souhaitez attribuer l’accès, puis cliquez sur la coche dans le coin supérieur droit. Il vous suffit de saisir le début du nom du groupe. Ensuite, cliquez sur le groupe pour le mettre en surbrillance, puis cliquez sur le bouton **Autoriser l’accès**, puis sur **Oui** lorsque le message de confirmation s’affiche.


4. Vous pouvez également voir quels utilisateurs sont affectés à l’application, soit directement ou en étudiant l’adhésion au sein d’un groupe. Pour ce faire, modifiez la définition de **la liste déroulante Affichage de Groupe** à **Tous les utilisateurs**. La liste affiche les utilisateurs du répertoire et indique s’ils sont affectés ou non à l’application. Elle indique également si les utilisateurs sont affectés directement à l’application (type d’affectation direct) ou par le biais de l’adhésion à un groupe (type d’affectation hérité).


> [AZURE.NOTE]L’onglet Utilisateurs et groupes s’affichera une fois que vous avez activé Azure AD Premium.

Voici quelques rubriques vous fournissant des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)

* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=August15_HO6-->