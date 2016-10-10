<properties
	pageTitle="Activer Microsoft Windows Hello Entreprise dans votre organisation | Microsoft Azure"
	description="Instructions de déploiement pour activer Microsoft Passport dans votre organisation."
	services="active-directory"
	documentationCenter=""
	keywords="configurer le déploiement Microsoft Passport, Microsoft Windows Hello Entreprise"
	authors="markusvi"
	manager="femila"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2016"
	ms.author="femila;markvi"/>


# Activer Microsoft Windows Hello Entreprise dans votre organisation

Après avoir connecté les appareils du domaine Windows 10 à Azure Active Directory (Azure AD), procédez comme suit pour activer Microsoft Windows Hello Entreprise dans votre organisation.

## Déployer System Center Configuration Manager Current Branch 
Pour déployer des certificats utilisateur en fonction des clés de Windows Hello Entreprise, vous avez besoin des ressources suivantes :

- **System Center Configuration Manager Current Branch**. Vous devez installer la version 1606 ou une version ultérieure. Pour plus d’informations, consultez [Documentation de System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) et [Blog de l’équipe System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infrastructure à clé publique (PKI)** : pour activer Microsoft Windows Hello Entreprise à l’aide de certificats utilisateur, vous devez disposer d’une infrastructure PKI. Si vous n’en avez pas, ou si vous ne souhaitez pas l’utiliser pour les certificats utilisateur, vous pouvez procéder comme suit :
 - **Déployer un contrôleur de domaine** : déployez un contrôleur de domaine qui dispose de Windows Server 2016 build 10551 (ou version ultérieure) et suivez les étapes pour [installer un contrôleur de domaine réplica dans un domaine existant](https://technet.microsoft.com/library/jj574134.aspx) ou [installer une nouvelle forêt Active Directory, si vous créez un nouvel environnement](https://technet.microsoft.com/library/jj574166). (Les fichiers ISO sont disponibles en téléchargement sur [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## Configurer Microsoft Windows Hello Entreprise via la stratégie de groupe dans Active Directory

 Vous pouvez utiliser la stratégie de groupe Windows Server Active Directory pour configurer vos appareils joints à un domaine Windows 10 afin de fournir les données d’identification utilisateur de Windows Hello Entreprise lors de la connexion à Windows :

1. 	Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2.	Dans Gestion des stratégies de groupe, accédez au nœud du domaine qui correspond au domaine dans lequel vous voulez activer Rejoindre Azure AD.
3.	Cliquez avec le bouton droit sur **Objets de stratégie de groupe** et sélectionnez **Nouveau**. Donnez un nom à votre objet de stratégie de groupe, par exemple Activer Windows Hello Entreprise. Cliquez sur **OK**.
4.	Cliquez avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
5.	Accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Composants Windows** > **Windows Hello Entreprise**.
6.	Avec le bouton droit, cliquez sur **Activer Windows Hello Entreprise**, puis sélectionnez **Modifier**.
7.	Sélectionnez l’option **Activé**, puis cliquez sur **Appliquer**. Cliquez sur **OK**.
8.	Vous pouvez maintenant lier l’objet de stratégie de groupe à l’emplacement de votre choix. Pour activer cette stratégie pour tous les appareils joints à un domaine Windows 10 de votre organisation, liez la stratégie de groupe au domaine. Par exemple :
 - Une unité d’organisation spécifique dans Active Directory où les ordinateurs Windows 10 joints au domaine seront situés
 - Un groupe de sécurité spécifique contenant des ordinateurs Windows 10 joints au domaine qui sera inscrit automatiquement auprès d’Azure AD


## Configurer Windows Hello Entreprise à l’aide de System Center Configuration Manager

Vous pouvez uniquement utiliser System Center Configuration Manager pour déployer les stratégies Windows Hello Entreprise pour Windows 10 version 1607. Démarrez winver sur votre ordinateur Windows 10 pour vérifier la version de votre ordinateur Windows 10.

1. Ouvrez **System Center Configuration Manager**, puis accédez à **Actifs et compatibilité > Paramètres de compatibilité > Accès aux ressources de l’entreprise > Profils Windows Hello Entreprise**.

	![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. Dans la barre d’outils en haut, cliquez sur **Créer un profil Windows Hello Entreprise**.

	![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. Dans la boîte de dialogue **Général**, procédez comme suit :

	![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/03.png)

	a. Dans la zone de texte **Nom**, entrez un nom pour votre profil, par exemple, **Mon profil WHfB**.

	b. Cliquez sur **Suivant**.


2. Dans la boîte de dialogue **Plateformes prises en charge**, sélectionnez les plateformes qui seront configurées avec ce profil Windows Hello Entreprise, puis cliquez sur **Suivant**.

	![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. Dans la boîte de dialogue **Paramètres**, procédez comme suit :

	![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/05.png)

	a. Pour **Configurer Windows Hello Entreprise**, sélectionnez **Activé**.

	b. Pour **Utiliser un module de plateforme sécurisée (TPM)**, sélectionnez **Obligatoire**.

	c. En tant que **Méthode d’authentification, sélectionnez **Basée sur un certificat**.

	d. Cliquez sur **Suivant**.



2. Sur la page **Résumé**, cliquez sur **Suivant**.

2. Dans la boîte de dialogue **Exécution terminée**, cliquez sur **Fermer**.


2. Dans la barre d’outils en haut, cliquez sur **Déployer**.

	![Configurer Windows Hello Entreprise](./media/active-directory-azureadjoin-passport-deployment/06.png)



## Configurer le profil de certificat pour utiliser le certificat d’enregistrement Windows Hello Entreprise dans Configuration Manager


Si vous utilisez l’authentification basée sur un certificat pour l’authentification locale, vous devez configurer et déployer un profil de certificat. Pour ce faire, vous devez configurer un serveur NDES et le rôle de site Point d’enregistrement de certificat dans System Center Configuration Manager. Pour plus d’informations, consultez [Configuration requise pour les profils de certificat dans Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Ouvrez **System Center Configuration Manager**, puis accédez à **Actifs et compatibilité > Paramètres de compatibilité > Accès aux ressources de l’entreprise > Profils de certificat**.


2. Sélectionnez un modèle qui dispose d’une ouverture de session avec carte à puce EKU.

Sur la page **Enregistrement SCEP** du profil de certificat, vous devez choisir **Installer dans Passport for Work, sinon mettre en échec** en tant que **Fournisseur de stockage de clés**.



## Étapes suivantes
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->