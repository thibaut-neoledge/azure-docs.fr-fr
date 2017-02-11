---
title: "Bonnes pratiques en matière d’authentification MFA | Microsoft Docs"
description: "Ce document propose de meilleures pratiques à l’aide de l’authentification Multifacteur Azure avec des comptes Azure"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2471a8daa91113c1865507239361d093f8695e30


---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Meilleures pratiques pour l’utilisation de l’authentification multifacteur Azure avec des comptes Azure AD
Pour améliorer le processus d’authentification, la vérification en deux étapes est le choix privilégié par la plupart des organisations. Azure multi-Factor Authentication (MFA) permet aux entreprises de satisfaire les exigences des sociétés en matière de sécurité et de conformité tout en fournissant une expérience de connexion facile pour leurs utilisateurs. Cet article décrit certaines bonnes pratiques que vous devez prendre en compte au moment de planifier l’adoption de l’authentification MFA.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Meilleures pratiques pour Azure Multi-Factor Authentication dans le cloud
Pour que tous les utilisateurs bénéficient de la vérification en deux étapes et des fonctionnalités étendues qu’offre l’authentification Azure MFA, vous devez activer celle-ci pour tous les utilisateurs.  Pour ce faire, utilisez l’une des implémentations suivantes :

* Azure AD Premium ou la suite Enterprise Mobility Suite
* Fournisseur d’authentification multi facteurs

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Azure AD Premium/Enterprise Mobility Suite
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

La première étape pour l’adoption d’Azure MFA dans le cloud en utilisant Azure AD Premium ou Enterprise Mobility Suite consiste à attribuer des licences à vos utilisateurs.  Azure Multi-Factor Authentication faisant partie de ces suites, votre organisation n’a pas besoin d’étendre la fonctionnalité de vérification en deux étapes à tous les utilisateurs. Vous devez uniquement attribuer une licence à un utilisateur. Ensuite, vous pouvez activer l’authentification MFA.

Durant la configuration de Multi-Factor Authentication, prenez en compte les éléments suivants :

* Vous n’avez pas à créer de fournisseur d’authentification multifacteur.  Azure AD Premium et Enterprise Mobility Suite sont intégrés à Azure Multi-Factor Authentication.  
* Azure AD Connect est une exigence uniquement si vous synchronisez votre environnement Active Directory local avec un répertoire Azure AD. Si vous utilisez un annuaire Azure AD non synchronisé avec une instance locale d’Active Directory, vous n’avez pas besoin d’Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Fournisseur d’authentification multi facteurs
![Fournisseur d’authentification multi facteurs](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si vous ne disposez pas d’Azure AD Premium ou d’Enterprise Mobility Suite, la première étape pour adopter l’authentification Azure MFA dans le cloud consiste à créer un fournisseur d’authentification MFA. Bien que l’authentification MFA soit disponible par défaut pour les administrateurs généraux disposant d’Azure Active Directory, quand vous déployez l’authentification MFA pour votre organisation, vous devez étendre la vérification en deux étapes à tous les utilisateurs. Pour ce faire, vous avez besoin d’un fournisseur d’authentification multifacteur.
Lorsque vous sélectionnez le fournisseur d’authentification, vous devez sélectionner un répertoire et de prendre en compte les éléments suivants :

* Vous n'avez pas besoin d’un répertoire Azure AD pour créer un fournisseur Multi-Factor Auth. Toutefois, vous devez associer le fournisseur d’authentification multifacteur à un annuaire Azure AD si vous souhaitez les fonctionnalités suivantes :  
  * Étendre la vérification en deux étapes à tous les utilisateurs  
  * Offrir à vos administrateurs généraux des fonctionnalités supplémentaires, telles que le portail de gestion, les messages de bienvenue personnalisés et les rapports
* DirSync ou AAD Sync sont nécessaires uniquement si vous synchronisez votre environnement Active Directory local avec un annuaire Azure AD. Si vous utilisez un annuaire Azure AD qui n’est pas synchronisé avec une instance locale d’Active Directory, DirSync ou AAD Sync est inutile.
* Veillez à choisir le modèle d’utilisation convenant à votre entreprise (par authentification ou par utilisateur activé). Une fois le modèle d’utilisation sélectionné, vous ne pourrez plus en changer.
  * Par authentification : vous payez pour chaque vérification. Utilisez ce modèle si vous souhaitez la vérification en deux étapes pour toute personne qui accède à une certaine application, et non pour des utilisateurs spécifiques.
  * Par utilisateur activé : vous payez pour chaque utilisateur que vous activez pour Azure MFA. Utilisez ce modèle si une partie seulement des utilisateurs possède des licences Azure AD Premium Enterprise Mobility Suite.

### <a name="user-account"></a>Compte d’utilisateur
Quand vous activez l’authentification Azure MFA pour vos utilisateurs, les comptes d’utilisateurs peuvent être dans un des trois états principaux : désactivé, activé ou appliqué.
Utilisez les instructions suivantes afin de vous assurer que vous utilisez bien l’option le mieux adaptée à votre déploiement :

* Quand l’état est défini sur **désactivé**, l’utilisateur n’effectue pas la vérification en deux étapes. Il s’agit de la valeur par défaut.
* Quand l’état est défini sur **activé**, cela signifie que l’utilisateur est activé, mais que la procédure d’inscription n’est pas terminée. Il seront invités à terminer le processus lors de la prochaine connexion. Ce paramètre n’affecte pas les applications sans navigateur. Toutes les applications continuent de fonctionner jusqu’à ce que le processus d’inscription soit terminé.
* Quand l’état est défini sur **appliqué**, cela ne signifie pas forcément que l’utilisateur a terminé son inscription. S’il a terminé le processus d’inscription, il effectue la vérification en deux étapes. Sinon, l’utilisateur est invité à terminer la procédure lors de la prochaine connexion Ce paramètre affecte les applications sans navigateur. Les applications ne fonctionnent pas tant que les mots de passe d’application ne sont ni créés ni utilisés.

Utilisez le modèle de Notification utilisateur disponible dans l’article [Prise en main de l’authentification multifacteur Azure dans le cloud](multi-factor-authentication-get-started-cloud.md) pour envoyer un courrier électronique au sujet de l’adoption de l’authentification Multifacteur à vos utilisateurs.

### <a name="supportability"></a>Prise en charge
Dans la mesure où la plupart des utilisateurs sont habitués à utiliser uniquement les mots de passe pour s’authentifier, il est important que votre société sensibilise l’ensemble des utilisateurs à ce processus. Le fait de connaître le processus peut réduire la probabilité que les utilisateurs appellent votre support technique pour des problèmes mineurs liés à l’authentification Multifacteur.
Toutefois, pour certains scénarios, il est nécessaire de désactiver provisoirement l’authentification Multifacteur. Suivez les indications suivantes pour comprendre comment gérer ces scénarios :

* Vérifiez que le personnel de support technique est formé à la gestion de scénarios dans lesquels l’application mobile ou le téléphone ne reçoivent pas de notification ou ne passe pas d’appel téléphonique, et pour cette raison, l’utilisateur se trouve dans l’impossibilité de se connecter. Le support technique peut activer une option de contournement à usage unique qui permet à un utilisateur de s’authentifier une seule fois en « contournant » la vérification en deux étapes. Le contournement est temporaire et expire après le nombre de secondes spécifié.
* Si nécessaire, vous pouvez utiliser la fonctionnalité de fournisseurs d’identité approuvés dans Azure MFA. Cette fonction permet aux administrateurs d’un locataire géré ou fédéré de contourner la vérification en deux étapes des utilisateurs qui se connectent depuis l’intranet local de l’entreprise. Les fonctionnalités sont disponibles pour les locataires Azure AD titulaires d’une licence Azure AD Premium, Enterprise Mobility Suite ou Azure Multi-Factor Authentication.

## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Meilleures pratiques pour l’authentification multifacteur Azure locale
Si votre entreprise a décidé de se servir de sa propre infrastructure pour activer l’authentification MFA, il est nécessaire de déployer un serveur Azure Multi-Factor Authentication localement. Les composants serveur de l’authentification MFA figurent dans le diagramme suivant :

![Fournisseur d’authentification multifacteur](./media/multi-factor-authentication-security-best-practices/server.png)
\*N’est pas installé par défaut \**Installé, mais non activé par défaut

Le serveur d’authentification multifacteur Azure permet de sécuriser les ressources de cloud et des ressources locales qui sont accessibles via des comptes Azure AD. Toutefois, cette opération peut uniquement être effectuée à l’aide de fédération.  En d’autres termes, vous devez disposer d’AD FS et le fédérer avec votre locataire Azure AD.
Durant la configuration du serveur Multi-Factor Authentication, tenez compte de ce qui suit :

* Si vous sécurisez des ressources Azure AD avec services de fédération Active Directory, le premier facteur d’authentification est effectué localement avec AD FS. Le second facteur est effectué localement en répondant à la demande.
* Il n’est pas nécessaire d’installer le serveur Azure Multi-Factor Authentication sur votre serveur de fédération AD FS. Toutefois, l’adaptateur Multi-Factor Authentication pour AD FS doit être installé sur un serveur Windows Server 2012 R2 exécutant AD FS. Vous pouvez installer le serveur sur un autre ordinateur, tant qu’il s’agit d’une version prise en charge, et installer l’adaptateur AD FS séparément sur votre serveur de fédération AD FS. Consultez la procédure ci-dessous pour obtenir des instructions sur l'installation séparée de l’adaptateur.
* L’assistant d’installation de l’adaptateur AD FS Multi-Factor Authentication crée un groupe de sécurité appelé PhoneFactor Admins dans votre annuaire Active Directory, puis ajoute votre compte de service AD FS à ce groupe. Nous vous recommandons de vérifier sur votre contrôleur de domaine que le groupe PhoneFactor Admins est bien créé et que le compte de service AD FS est membre de ce groupe. Si nécessaire, ajoutez manuellement le compte de service AD FS au groupe PhoneFactor Admins sur votre contrôleur de domaine.

### <a name="user-portal"></a>Portail de l'utilisateur
Ce portail s’exécute sur un site web Internet Information Server (IIS), qui autorise les fonctionnalités en libre-service et fournit un ensemble complet de fonctionnalités d’administration des utilisateurs. Utilisez les instructions suivantes pour configurer ce composant :

* IIS 6 ou version ultérieure est requis
* ASP.NET v2.0.507207 doit être installé et inscrit
* Ce serveur peut être déployé dans un réseau de périmètre.

### <a name="app-passwords"></a>Mots de passe d'application
Si votre organisation est fédérée (SSO) avec Azure AD et que vous vous apprêtez à utiliser l’authentification Azure MFA, voici ce que vous devez absolument savoir à propos de l’utilisation des mots de passe d’application (gardez à l’esprit que cela ne s’applique que dans le cadre de la fédération (SSO)) :

* Le mot de passe est vérifié par Azure AD et il contourne ainsi la fédération. La fédération n’est utilisée activement que lorsque vous configurez le mot de passe d’application.
* Les mots de passe utilisateur sont stockés dans l’id d’organisation. Si l'utilisateur quitte l'entreprise, ces informations doivent être stockées en temps réel dans l'id d'organisation à l'aide de DirSync. La désactivation/suppression de compte peut mettre jusqu’à trois heures à se synchroniser, ce qui peut retarder la désactivation/suppression du mot de passe dans Azure AD.
* Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
* Aucune authentification locale de journalisation/fonctionnalité d’audit n'est disponible pour Mot de passe
* Pour utiliser le client Microsoft Lync 2013, l’utilisateur final doit être très qualifié.
* Certaines conceptions architecturales avancées peuvent nécessiter l’utilisation d’une combinaison de noms d’utilisateur, de mots de passe et de mots de passe d’application durant l’utilisation de la vérification en deux étapes avec les clients, selon l’emplacement où ils s’authentifient. Pour les clients qui s’authentifient auprès d’une infrastructure locale, vous devez utiliser le nom d’utilisateur et le mot de passe d’une organisation. Pour les clients qui s'authentifient auprès d'Azure AD, vous utiliseriez le mot de passe d’application.
* Par défaut, les utilisateurs ne peuvent pas créer des mots de passe d'application. Si, en raison des contraintes de votre entreprise ou de vos besoins, les utilisateurs doivent pouvoir créer un mot de passe d’application dans certains scénarios, vérifiez que l’option **Autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur** est sélectionnée.

## <a name="additional-considerations"></a>Considérations supplémentaires
Utilisez la liste ci-dessous pour connaître certaines considérations supplémentaires, et les meilleures pratiques pour chaque composant qui sera déployé sur site :

| Méthode | Description |
|:--- |:--- |
| [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md) |Informations sur la configuration d'Azure Multi-Factor Authentication avec AD FS. |
| [Authentification RADIUS](multi-factor-authentication-get-started-server-radius.md) |Informations sur l'installation et la configuration du serveur Azure MFA avec RADIUS. |
| [Authentification IIS](multi-factor-authentication-get-started-server-iis.md) |Informations sur l'installation et la configuration du serveur Azure MFA avec IIS. |
| [Authentification Windows](multi-factor-authentication-get-started-server-windows.md) |Informations sur l'installation et la configuration du serveur Azure MFA avec l’authentification Windows. |
| [Authentification LDAP](multi-factor-authentication-get-started-server-ldap.md) |Informations sur l'installation et la configuration du serveur Azure MFA avec l’authentification LDAP. |
| [Passerelle des services Bureau à distance et serveur Azure Multi-Factor Authentication avec RADIUS](multi-factor-authentication-get-started-server-rdg.md) |Informations sur l'installation et la configuration du serveur Azure MFA avec Passerelle des services Bureau à distance et RADIUS. |
| [Synchronisation avec Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md) |Informations sur l’installation et la configuration de la synchronisation entre Active Directory et le serveur Azure MFA. |
| [Déploiement du service Web de l’application mobile du serveur Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md) |Informations sur l'installation et la configuration du service Web du serveur Azure MFA. |
| [Configuration VPN avancée avec l’authentification multifacteurs Azure](multi-factor-authentication-advanced-vpn-configurations.md) |Informations sur la configuration d’appareils itrix Netscaler et Juniper/Pulse Secure VPN. |

## <a name="additional-resources"></a>Ressources supplémentaires
Bien que cet article mette en évidence quelques-unes des meilleures pratiques de l’authentification Multifacteur Azure, il existe d’autres ressources que vous pouvez également utiliser au moment de la planification de votre déploiement de l’authentification Multifacteur. La liste ci-dessous présente certains articles clés qui peuvent vous aider lors de ce processus<:

* [Rapports dans Azure Multi-Factor Authentication](multi-factor-authentication-manage-reports.md)
* [Configuration de l’expérience Azure Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md)
* [Forum Aux Questions d’Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)




<!--HONumber=Nov16_HO3-->


