<properties
	pageTitle="Azure AD Connect : Prise en main à l’aide de paramètres express | Microsoft Azure"
	description="Découvrez comment télécharger, installer et exécuter l’Assistant d’installation d’Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/18/2016"
	ms.author="billmath;andkjell"/>

# Prise en main d’Azure AD Connect à l’aide de paramètres express
La documentation suivante vous aide à faire vos premiers pas avec Azure Active Directory Connect. Cette documentation traite de l’installation rapide d’Azure AD Connect.

## Documentation connexe
Si vous n’avez pas lu la documentation sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md), le tableau suivant fournit des liens vers des rubriques connexes. Les deux premières rubriques en gras doivent absolument être lues avant l'installation.

| Rubrique | |
| --------- | --------- |
| **Téléchargez Azure AD Connect** | [Téléchargez Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) |
| **Matériel et conditions préalables** | [Azure AD Connect : matériel et conditions préalables](active-directory-aadconnect-prerequisites.md) |
| Installation à l’aide des paramètres personnalisés | [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Effectuer une mise à niveau à partir de DirSync | [Effectuer une mise à niveau à partir de l’outil de synchronisation Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |
| Après l’installation | [Vérification de l’installation et affectation des licences ](active-directory-aadconnect-whats-next.md) |
| Comptes utilisés pour l’installation | [Informations supplémentaires sur les autorisations et les comptes Azure AD Connect](active-directory-aadconnect-accounts-permissions.md) |


## Installation rapide pour Azure AD Connect
La sélection des paramètres Express est l’option par défaut et s’applique à la plupart des scénarios. Ce faisant, Azure AD Connect déploie la synchronisation avec l’option de synchronisation de mot de passe. Cela concerne une seule forêt et permet aux utilisateurs d’utiliser leur mot de passe local pour se connecter au cloud. L’utilisation des paramètres Express lancera automatiquement une synchronisation une fois l’installation terminée (mais vous pouvez également choisir de ne pas le faire). Avec cette option, l’extension de votre répertoire local dans le cloud n’est plus qu’à quelques clics.

![Bienvenue dans Azure AD Connect](./media/active-directory-aadconnect-get-started-express/welcome.png)

### Pour installer Azure AD Connect à l'aide de paramètres Express

1. Connectez-vous en tant qu’administrateur local au serveur sur lequel vous souhaitez installer Azure AD Connect. Il doit s'agir du serveur que vous choisissez comme serveur de synchronisation.
2. Accédez à AzureADConnect.msi et double-cliquez sur ce fichier.
3. Sur l'écran d’accueil, sélectionnez la case pour accepter les termes du contrat de licence et cliquez sur **Continuer**.
4. Sur l'écran Paramètres Express, cliquez sur **Utiliser les paramètres Express**. ![Bienvenue dans Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. Sur l’écran la connexion à l'écran d'Azure AD, entrez le nom d'utilisateur et un mot de passe d'administrateur global Azure pour votre instance Azure AD. Cliquez sur **Next**. ![Se connecter à AAD](./media/active-directory-aadconnect-get-started-express/connectaad.png) Si vous recevez une erreur et rencontrez des problèmes de connectivité, consultez l’article [Résoudre les problèmes de connectivité](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Sur l’écran Connexion à AD DS, entrez le nom d'utilisateur et le mot de passe d’un compte d'administrateur d’entreprise. Cliquez sur **Suivant**. ![Bienvenue dans Azure AD Connect](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. Sur l’écran Prêt à configurer, cliquez sur **Installer**.
	- Dans la page Prêt à configurer, vous pouvez éventuellement décocher la case **Démarrer le processus de synchronisation dès que la configuration est terminée**. Si vous faites cela, l'assistant configurera la synchronisation, mais laissera la tâche désactivée afin qu'elle ne s’exécute pas avant que vous ne l'activiez manuellement dans le Planificateur de tâches. Une fois la tâche activée, la synchronisation s’exécute toutes les 30 minutes.
	- Éventuellement, vous pouvez également choisir de configurer les services de synchronisation pour le **déploiement hybride Exchange** en cochant la case à cocher correspondante. Si vous n’envisagez pas de disposer de boîtes aux lettres Exchange dans le cloud et en local, vous n’avez pas besoin de cela. ![Bienvenue dans Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
8. Une fois l'installation terminée, cliquez sur **Quitter**.
9. Une fois l’installation terminée, déconnectez-vous puis reconnectez-vous à Windows avant d’utiliser le gestionnaire Synchronization Service Manager ou l’éditeur de règles de synchronisation.

Suivez ce lien pour une vidéo sur l’utilisation de l'installation rapide :

[AZURE.VIDEO azure-active-directory-connect-express-settings]

## Étapes suivantes
Notez que lorsque vous avez Azure AD Connect installé, vous pouvez [vérifier l'installation et affecter des licences ](active-directory-aadconnect-whats-next.md).

En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0224_2016-->