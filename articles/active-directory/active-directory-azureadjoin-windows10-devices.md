<properties 
	pageTitle="Utilisation d’appareils Windows 10 sur votre lieu de travail | Microsoft Azure" 
	description="Fournit une vue d'ensemble des fonctionnalités offertes aux utilisateurs et services informatiques, en mettant en contraste les différentes manières dont un appareil peut être approvisionné et utilisé dans une entreprise dotée de Windows 10." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Utilisation d’appareils Windows 10 sur votre lieu de travail

Windows 10 propose trois modèles pour les entreprises permettant aux utilisateurs d’accéder à leurs ressources de travail de manière sécurisée et pratique.

1. **Azure AD Join**, nouveauté de Windows 10, est une expérience d’approvisionnement de ressources de travail en libre-service conçue pour les professionnels qui accèdent à leurs ressources essentiellement dans le cloud, comme Office 365.
1. La **jonction de domaine**, améliorée dans Windows 10 lorsque vous êtes connecté à Azure AD, est conçue pour les entreprises qui réalisent des investissements importants dans les applications et ressources sur site.
1. **Une nouvelle expérience BYOD simplifiée** qui permet aux utilisateurs d’ajouter un compte professionnel à Windows pour accéder facilement aux ressources de travail sur des appareils personnels.

Le tableau suivant présente un aperçu des fonctionnalités offertes aux utilisateurs et services informatiques, en mettant en contraste les différentes façons dont un appareil peut être approvisionné et utilisé dans une entreprise dotée de Windows 10 :

| | Jonction de domaine | Azure AD Join | Appareil personnel |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Connexion aux appareils Windows avec des comptes professionnels | Oui | Oui | Non |
| Authentification unique de l’utilisateur sur les applications Office 365 et Azure AD, [1] Authentification unique ou possibilité d’accéder aux ressources de l’entreprise sans entrer les informations d’identification après une première connexion. | Oui | Oui | Oui |
| Authentification unique de l’utilisateur sur les applications Kerberos/NTLM | Oui | Limité | Via VPN |
| Authentification forte et connexion pratique pour les comptes professionnels avec Microsoft Passport et Windows Hello | Oui | Oui | Oui |
| Accès au Windows Store pour entreprises à l’aide d’un compte professionnel (aucun compte Microsoft) | Oui | Oui | Oui |
| Itinérance des paramètres utilisateur compatible pour l’entreprise sur tous les appareils en utilisant un compte professionnel | Oui | Oui | Oui |
| Restreindre l’accès aux applications professionnelles à partir d’appareils compatibles avec les stratégies de l’entreprise concernant les appareils | Oui | Oui | Oui |
| Approvisionnement en libre-service par l’utilisateur d’appareils pour travailler n’importe où | Non | Oui | Oui |
| Possibilité de gérer des appareils | Oui via la stratégie de groupe/SCCM | Oui | Oui |

##Appareils professionnels : Azure AD Join et la jonction de domaine

Windows 10 propose deux modèles pour les appareils professionnels pour accéder aux ressources de travail :

- Azure AD Join
- Jonction de domaine

 Ces deux options sont pertinentes en fonction des besoins et exigences de l’entreprise. Dans certains cas, les entreprises peuvent trouver pertinent de mettre en place ces deux méthodes de déploiement.

## Quand utiliser Azure Active Directory Join ?

Azure AD Join est une nouvelle expérience d’approvisionnement de ressources de travail en libre-service dans Windows 10. Elle est conçue pour les professionnels qui accèdent à leurs ressources de travail essentiellement dans le Cloud comme Office 365. C’est une méthode légère de configuration des PC, tablettes et téléphones pour l’entreprise. Les appareils sont gérés via la gestion des périphériques mobiles (GPM) avec des contrôles homogènes entre les différentes plateformes Windows.

**Voici plusieurs raisons d’utiliser Azure AD Join **:

1.	Vous souhaitez permettre l’approvisionnement en libre-service d’appareils pour les professionnels souvent en déplacement.
2.	Vous fournissez aux utilisateurs des appareils professionnels, comme des tablettes et téléphones.
3.	Vous souhaitez gérer un ensemble d’utilisateurs dans Azure AD, au lieu d’AD, par exemple pour les travailleurs saisonniers, sous-traitants ou étudiants.
4.	Vous souhaitez fournir des fonctionnalités de jonction aux travailleurs dans des succursales distantes avec une infrastructure locale limitée.
5.	Vous n’avez pas d’AD local.

Certaines entreprises utilisent Azure AD Join comme principale méthode de déploiement des appareils professionnels, notamment lorsqu’ils migrent toutes leurs ressources ou la plupart de leurs ressources dans le cloud. Les organisations hybrides disposant à la fois d’AD et d’Azure AD peuvent également choisir de déployer une méthode ou l’autre en fonction de l’utilisateur ou du département. Par exemple, les académies scolaires et les universités peuvent souhaiter gérer leur personnel dans AD et leurs élèves/étudiants dans Azure AD, ou bien certaines entreprises peuvent souhaiter que les bureaux distants ou départements des ventes soient gérés sur Azure AD. Les deux méthodes Azure AD Join et Jonction de domaine peuvent être utilisées au sein d’une organisation hybride. Azure AD Join peut être un bon complément pour le déploiement d’appareils dans un environnement de travail.

**Si la plupart des accès aux ressources d’entreprise se font via le cloud, une organisation peut souhaiter bénéficier des avantages supplémentaires** :

- Vous pourrez supprimer des dépendances sur une infrastructure d’identité locale.
- Vous pouvez simplifier votre modèle de déploiement d’appareils en remplaçant les solutions de création d’images par une configuration en libre-service.
- GPM permet de gérer tous vos appareils sur différentes plateformes.

Pour plus d’informations sur Azure AD Join, consultez [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## Quand utiliser la jonction de domaine (ou continuer à l’utiliser) ?

La jonction de domaine est la méthode que les entreprises utilisent traditionnellement depuis une quinzaine d’années ou plus pour connecter leurs appareils professionnels. Elle permet aux utilisateurs de se connecter à leurs appareils avec leur compte professionnel AD et au département informatique de gérer ces appareils intégralement et de manière centralisée. En règle générale, les entreprises s’appuient sur des méthodes de création d’images pour approvisionner des appareils et utilisent System Center Configuration Manager (SCCM) ou les stratégies de groupe pour les gérer.

**Les raisons suivantes peuvent justifier votre utilisation de la jonction de domaine dans votre entreprise **:

- Vous disposez d’applications Win32 déployées sur ces appareils qui utilisent NTLM/Kerberos.
- Vous avez besoin de stratégies de groupe ou de SCCM/DCM pour gérer les appareils.
- Vous souhaitez continuer à utiliser des solutions de création d’images pour configurer les appareils de vos employés.

**La jonction de domaine dans Windows 10 va vous offre également les avantages suivants après votre connexion à Azure AD **:

- Authentification forte liée à l’appareil et connexion pratique pour les comptes professionnels avec Microsoft Passport et Windows Hello.
- Accès au Windows Store pour entreprises à l’aide de comptes professionnels (aucun compte Microsoft requis).
- Itinérance des paramètres utilisateur compatible pour l’entreprise sur tous les appareils avec un compte professionnel (aucun compte Microsoft requis).
- Possibilité de restreindre l’accès aux applications professionnelles à partir d’appareils compatibles avec les stratégies de l’entreprise concernant les appareils.
- Pour plus d’informations sur Azure AD Join, consultez [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-overview.md).

##Activation d’appareils personnels pour travailler avec des comptes professionnels

Pour prendre en charge le BYOD dans l’entreprise, Windows 10 donne à l’utilisateur la possibilité d’ajouter un compte professionnel ou scolaire sur son PC, sa tablette ou son téléphone. L’ajout d’un compte professionnel inscrit l’appareil auprès d’Azure AD et éventuellement auprès du service GPM que l’entreprise a configuré pour les appareils. Le répertoire affiche ces appareils comme « Inscrits », par opposition à « Joints à Azure AD ». Le service informatique peut appliquer différentes stratégies en fonction de ces informations, en allégeant éventuellement les stratégies appliquées sur un appareil personnel, par rapport à celles appliquées sur un appareil professionnel, le cas échéant.

Les utilisateurs peuvent ajouter un compte professionnel à leur appareil personnel très aisément : lorsqu’ils accèdent à une application professionnelle pour la première fois, ou manuellement via l’application Paramètres. Ce compte professionnel fournit une authentification unique aux ressources de l’entreprise.

Pour plus d’informations sur Azure AD Join, consultez [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## Activation de la jonction de domaine ou d’Azure AD Join 

Toutes les méthodes décrites ci-dessus (jonction de domaine, Azure AD Join et ajout d’un compte professionnel) disposent de points d’entrée dans l’expérience utilisateur Windows 10. Toutefois, leur mise en place nécessite que le département informatique active la fonctionnalité dans l’infrastructure.

##Azure AD Join

Pour déployer Azure AD joindre pour un ensemble d’utilisateurs, vous avez besoin des éléments suivants :
---------------------------------------------------------------------------
- Un abonnement Azure AD
- Pour accéder à davantage de fonctionnalités, vous avez besoin d’un abonnement Azure AD Premium, par exemple l’inscription automatique GPM.
- Un abonnement Azure AD Premium.
- Avoir un système GPM (abonnement Intune ou GPM pour Office 365 ou un fournisseur GPM tiers qui s’intègre dans Active Directory Azure ; consultez la section FAQ pour plus de détails)
- Si vous disposez d’un environnement hybride, il est fortement recommandé d’effectuer les opérations suivantes :
- Déployer Azure AD Connect pour étendre l’annuaire local vers Azure AD.

##Jonction de domaine

La jonction de domaine continue à fonctionner comme d’habitude. Cependant, pour profiter des avantages d’Azure AD, vous aurez besoin des éléments suivants :

- Un abonnement Azure AD
- Déployer Azure AD Connect pour étendre l’annuaire local vers Azure AD.
- Définition d’une stratégie pour connecter des appareils joints au domaine à Azure AD.
- Pour plus d’informations sur la jonction de domaine dans Windows 10, consultez <link-to-DJ-in-Win10-deployment-guide>.
- Pour un accès conditionnel, vous pouvez créer la stratégie qui autorise l’accès aux appareils joints à un domaine. Pour activer les règles de demande d’appareils compatibles, vous avez besoin des éléments suivants :
- System Center Configuration Manager version 1509 pour la version d’évaluation technique (consultez la documentation et le billet de bloc TechNet).

##BYOD et ajout d’un compte professionnel

Pour activer le BYOD avec des comptes professionnels, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Pour accéder à davantage de fonctionnalités, vous avez besoin d’un abonnement Azure AD Premium, par exemple l’inscription automatique GPM.
- Un abonnement Azure AD Premium.
- Avoir un système GPM (abonnement Intune ou GPM pour Office 365 ou un fournisseur GPM tiers qui s’intègre dans Active Directory Azure ; consultez la section FAQ pour plus de détails)

##Microsoft Passport

Pour activer Microsoft Passport en plus, vous devez disposer des éléments suivants :

- Infrastructure à clé publique pour la prise en charge de l’authentification basée sur le certificat à l’aide de Microsoft Passport.
- Abonnement Intune pour la prise en charge de l’authentification basée sur le certificat à l’aide de Microsoft Passport pour les comptes professionnels et Azure AD Join.
- System Center Configuration Manager version 1509 pour la version d’évaluation technique (consultez la documentation et le billet de blog TechNet) pour la prise en charge de l’authentification basée sur le certificat à l’aide de Microsoft Passport pour la jonction de domaine.
- Définissez la stratégie pour activer Microsoft Passport dans l’organisation.

Au lieu d’avoir une infrastructure à clé publique, vous pouvez activer Microsoft Passport basé sur des clés en procédant comme suit :

- Déployez des contrôleurs de domaine Windows Server 2016 Production Preview 1 (pas besoin de niveau fonctionnel de domaine ou forêt ; deux contrôleurs de domaine pour la redondance desservant chaque site AD suffisent).
- Définissez la stratégie pour activer Microsoft Passport dans l’organisation.
- Pour plus d’informations sur Microsoft Passport et Windows Hello dans Windows 10, consultez <link-to-MS-Passport-and-Windows-Hello-document>.

## Forum Aux Questions

###Qu’en est-il de la jonction d’espace de travail dans Windows 10 ?
La jonction d’espace de travail dans Windows 8.1 a été utilisée pour activer le BYOD. Dans Windows, 10, le BYOD est activé via l’ajout d’un compte professionnel, comme expliqué précédemment dans ce document. Pour les organisations qui n’intègrent pas leur GPM avec Azure AD, les utilisateurs peuvent inscrire manuellement l’appareil auprès du système de gestion via **Paramètres** > **Comptes** > **Accès professionnel**.

###L’utilisateur peut-il connecter son compte Microsoft (MSA) à son compte de domaine dans Windows 10 ?
Pas dans Windows 10. Dans Windows 8.1, les utilisateurs d’appareils de domaines joints pouvaient « connecter » leur compte MSA (par exemple, Hotmail, Live, Outlook, XBox, etc.) à leur compte de domaine pour rendre possibles certaines expériences comme l’authentification unique sur les services Live, l’utilisation de Windows Store et l’itinérance des paramètres utilisateur entre les appareils. Dans Windows 10, la fonctionnalité MSA Connect a été retirée. L’utilisateur peut ajouter un ou plusieurs comptes MSA comme comptes supplémentaires pour activer l’authentification unique sur les services utilisateur tels que le Windows Store. Cette opération est effectuée dans **Paramètres** > **Comptes** > **Votre compte**.

Les utilisateurs procédant à la mise à niveau depuis les appareils Windows 8.1 avec jonction de domaine et dont le compte MSA était connecté automatiquement verront leur compte MSA connecté ajouté à la liste des comptes supplémentaires qu’ils utilisent.


## Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0218_2016-->