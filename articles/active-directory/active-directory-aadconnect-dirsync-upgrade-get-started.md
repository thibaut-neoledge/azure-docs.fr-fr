<properties 
   pageTitle="Microsoft Azure AD Connect : mise à niveau à partir de l’outil de synchronisation Windows Azure AD (synchronisation d’annuaires)" 
   description="Découvrez comment mettre à niveau la synchronisation d’annuaires vers Azure AD Connect. Cet article décrit les étapes de la mise à niveau de votre outil de synchronisation Windows Azure AD (synchronisation d’annuaires) vers Azure AD Connect." 
   services="active-directory" 
   documentationCenter="" 
   authors="shoatman" 
   manager="terrylanfear" 
   editor="billmath"/>

<tags 
   ms.service="active-directory" 
   ms.workload="identity" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="05/26/2015" 
   ms.author="shoatman"/>

# Mise à niveau de la synchronisation Windows Azure Active Directory (synchronisation d’annuaires) vers Azure Active Directory Connect

Cette documentation vous aidera à mettre à niveau votre installation existante de la synchronisation d’annuaires vers Azure AD Connect

## Téléchargez Azure AD Connect

Pour commencer à utiliser Azure AD Connect, vous pouvez télécharger la dernière version à l’adresse suivante : [Télécharger la version préliminaire publique d’Azure AD Connect](http://connect.microsoft.com/site1164/program8612)

## Avant d’installer Azure AD Connect
Avant d’installer Azure AD Connect et d’effectuer la mise à niveau à partir de la synchronisation d’annuaires, voici ce dont vous aurez besoin.

- Le mot de passe du compte d’administrateur général de votre instance Azure AD. (Le programme d’installation vous rappellera de quel compte il s’agit.)
- Un compte d’administrateur d’entreprise pour votre Active Directory local
- Facultatif : si vous avez configuré la synchronisation d’annuaires pour qu’elle utilise une version complète de SQL Server, les informations de cette instance de base de données.

### Déploiement en parallèle

Si synchronisez actuellement plus de 50 000 objets, une option vous permet d’effectuer un déploiement en parallèle. Le déploiement en parallèle nécessite un serveur distinct ou un ensemble de serveurs distinct (si vous avez besoin d’un serveur distinct pour SQL Server). L’avantage de ce déploiement en parallèle est qu’il permet d’éviter d’interrompre le service de synchronisation. L’installation d’Azure AD Connect va tenter d’estimer le temps d’arrêt attendu, mais si vous avez déjà mis à niveau la synchronisation d’annuaires, votre propre expérience est sans doute plus fiable.

## Installation d’Azure AD Connect

Téléchargez Azure AD Connect et copiez-le vers votre serveur de synchronisation d’annuaires actuel.

1. Accédez à AzureADConnect.msi et double-cliquez sur ce fichier.
2. Suivez les étapes de l’Assistant

Pour une mise à niveau sur place, les principales étapes sont les suivantes :

1. Bienvenue dans Azure AD Connect
2. Analyse de la configuration de la synchronisation d’annuaires
3. Collecte du mot de passe administrateur global Azure AD
4. Collecte des informations d’identification pour le compte d’administrateur entreprise (utilisé uniquement lors de l’installation d’Azure AD Connect)
5. Installation d’Azure AD Connect
    * Désinstallation de la synchronisation d’annuaires
	* Installation d’Azure AD Connect
	* Synchronisation, si nécessaire

Étapes/informations supplémentaires nécessaires dans les cas suivants :

* Vous utilisez actuellement un serveur SQL Server complet, local ou distant
* Vous avez plus de 50 000 objets à synchroniser

## Mise à niveau sur place - moins de 50 000 objets - SQL Express (Procédure pas à pas)

0. Lancez le programme d’installation d’Azure AD Connect (MSI)

1. Lisez et acceptez les termes du contrat de licence et la déclaration de confidentialité.

![Bienvenue dans Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)

2. Cliquez sur le bouton Suivant pour analyser votre installation de synchronisation d’annuaires

![Analyse de l’installation existante de la synchronisation d’annuaires](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)

3. Lorsque l’analyse est terminée, nous proposons des recommandations sur la marche à suivre. Dans ce scénario (moins de 50 000 objets avec SQL Express), l’écran suivant s’affiche.

![Analyse terminée, mise à niveau à partir de la synchronisation d’annuaires prête](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)

4. Indiquez le mot de passe du compte que vous utilisez actuellement pour vous connecter à Azure AD.

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)

5. Indiquez un compte d’administrateur entreprise pour Active Directory.

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)

6. Vous êtes prêt à passer à la configuration. Lorsque vous cliquez sur Suivant, la synchronisation d’annuaires est désinstallée et Azure AD Connect est configuré et lance la synchronisation.  

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## Mise à niveau sur place - plus de 50 000 objets
À l’étape 3, vous voyez un autre message si vous avez plus de 50 000 objets à synchroniser. Un écran semblable à celui-ci s’affiche :

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Dans ce cas, nous vous recommandons d’envisager une mise à niveau en parallèle sur un serveur distinct. La raison de cette recommandation est qu’en fonction de la taille de votre organisation, une mise à niveau sur place peut avoir un impact sur les contrats de niveau de service de votre entreprise relatifs à la vitesse à laquelle les modifications apportées dans la version locale d’Active Directory sont répercutées dans Azure AD/Office 365. Nous tentons d’estimer la durée que peut prendre la première synchronisation avec Azure AD Connect. Comme mentionné ci-dessus, votre propre expérience de l’installation initiale de la synchronisation d’annuaires ou de sa mise à niveau vous donne une idée plus précise.

Le déploiement en parallèle nécessite un serveur distinct ou un ensemble de serveurs distinct (si vous devez exécuter SQL Server sur un autre serveur que celui d’Azure AD Connect). Pour cette raison, il est raisonnable d’envisager une mise à niveau sur place si elle peut être planifiée de façon à éviter tout impact sur votre organisation.

Pour procéder à une mise à niveau sur place, activez la case à cocher en regard du message : « Continuer la mise à niveau de la synchronisation d’annuaires sur cet ordinateur ».

## Mise à niveau sur place - SQL Server complet

À l’étape 3, vous obtenez un autre message si votre installation de la synchronisation d’annuaires utilise une version complète locale ou distante de SQL Server. Un écran semblable à celui-ci s’affiche :

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)

Les informations concernant la base de données SQL Server utilisée par la synchronisation d’annuaires s’affichent. Apportez des modifications, si nécessaire. Cliquez sur Suivant pour continuer l’installation.

## Déploiement en parallèle : plus de 50 000 objets

À l’étape 3, si vous avez plus de 50 000 objets, l’installation d’Azure AD Connect recommande un déploiement en parallèle. Consultez la rubrique Mise à niveau en place - plus de 50 000 objets ci-dessus pour plus d’informations et savoir si vous devez choisir un déploiement sur place ou un déploiement en parallèle d’Azure AD Connect. Un écran semblable à celui-ci s’affiche :

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Si vous souhaitez appliquer un déploiement en parallèle, vous devez effectuer les opérations suivantes :

- Cliquez sur le bouton Paramètres d’exportation. Lorsque vous installez Azure AD Connect sur un autre serveur, ces paramètres sont importés pour migrer tous les paramètres de votre synchronisation d’annuaires actuelle vers votre nouvelle installation AD Connect.

Une fois vos paramètres exportés, vous pouvez quitter l’Assistant Azure AD Connect sur le serveur de synchronisation d’annuaires.

### Installation d’Azure AD Connect sur un serveur distinct

Lorsque vous installez Azure AD Connect sur un nouveau serveur, il ne trouve pas la synchronisation d’annuaires et suppose que vous souhaitez effectuer une nouvelle installation d’Azure AD Connect. Cette opération comporte quelques étapes spécifiques :

1. Lancez le programme d’installation d’Azure AD Connect (MSI)
2. Lorsque vous voyez l’écran Bienvenue à Azure AD Connect. Quittez l’Assistant en cliquant sur le X en haut à droite de la fenêtre.
3. Ouvrez une invite de commandes
4. À partir de l’emplacement d’installation d’Azure AD Connect (par défaut C:\Program Files\Microsoft Azure Active Directory Connect), exécutez la commande suivante :
    * AzureADConnect.exe /migrate

Azure AD Connect se connecte et vous présente l’interface utilisateur suivante :

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)

5. Sélectionnez le fichier de paramètres exportés à partir de votre installation de la synchronisation d’annuaires.
6. Configurez les options avancées :
    * Un emplacement d’installation personnalisé pour Azure AD Connect
	* Une instance de SQL Server (par défaut, Azure AD Connect installe SQL Server 2012 Express)
	* Un compte de service utilisé pour la connexion à SQL Server. (Si votre base de données SQL Server est distante, ce compte doit être un compte de service du domaine)

Consultez ces options dans l’interface utilisateur :

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)

7. Cliquez sur Suivant. 
8. Sur la page Prêt à configurer, désactivez l’option Démarrer le processus de synchronisation dès que la configuration est terminée.
8. Cliquez sur Installer.

[AZURE.NOTE]Nous désactivons la case à cocher Démarrer la synchronisation afin de garantir que la synchronisation d’annuaires, qui est encore installée et en cours d’exécution, et Azure AD Connect n’essaient pas d’écrire dans Azure Active Directory en même temps.

### Vérifiez qu’Azure AD Connect est prêt à commencer la synchronisation

Afin de déterminer si Azure AD Connect est prêt à prendre le relais de la synchronisation d’annuaires, vous devrez ouvrir le gestionnaire Synchronization Service Manager Azure AD Connect. Recherchez « Synchronisation » dans le menu Démarrer de Windows pour arriver à cette application.

Dans l’application, vous devez afficher l’onglet Opérations. Sous cet onglet, vérifiez que les opérations suivantes ont été effectuées :

- Importation de l’agent de gestion Active Directory
- Importation de l’agent de gestion Azure Active Directory
- Synchronisation complète de l’agent de gestion Active Directory
- Synchronisation complète de l’agent de gestion Azure Active Directory

Une fois ces quatre opérations terminées, vous êtes prêt à désinstaller la synchronisation d’annuaires et à activer la synchronisation Azure AD Connect.

### Désinstaller la synchronisation d’annuaires (ancien serveur)

- Dans Ajouter ou supprimer des programmes, recherchez l’outil de synchronisation Windows Azure Active Directory.
- Désinstallez l’outil de synchronisation Windows Azure Active Directory.

### Ouvrez Azure AD Connect (nouveau serveur)
Après l’installation, lorsque vous rouvrez Azure AD Connect, vous aurez la possibilité de le configurer. Ouvrez Azure AD Connect.

Les éléments suivants doivent s’afficher :

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* Sélectionnez Configuration du mode de préproduction.
    * La mise à niveau à partir de la synchronisation d’annuaires à l’aide des paramètres exportés place automatiquement Azure AD Connect en mode de préproduction. Dans ce mode de préproduction, la synchronisation se produit dans Azure AD Connect, mais les modifications ne sont pas exportées vers Azure Active Directory ou Active Directory.
* Désactivez la phase intermédiaire en désactivant la case à cocher Mode de préproduction activé.

![Entrez vos informations d’identification Azure AD.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* Cliquez sur le bouton Installer

Félicitations, vous avez migré vers Azure AD Connect avec un déploiement en parallèle.

## Composants de prise en charge d’Azure AD Connect

Voici une liste de prérequis et de composants de prise en charge qu’Azure AD Connect installe sur le serveur sur lequel vous configurez Azure AD Connect. Cette liste est destinée à une installation Express de base. Si vous choisissez d’utiliser un autre serveur SQL Server sur la page d’installation des services de synchronisation, les composants SQL Server 2012 ci-dessous ne sont pas installés.

- Connecteur Forefront Identity Manager Azure Active Directory
- Utilitaires de ligne de commande Microsoft SQL Server 2012
- Client natif Microsoft SQL Server 2012
- Base de données locale Microsoft SQL Server 2012 Express
- Module Azure Active Directory pour Windows PowerShell
- Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique
- Package de redistribution Microsoft Visual C++ 2013


**Ressources supplémentaires**

* [Utiliser votre infrastructure d’identité locale dans le cloud](active-directory-aadconnect.md)
* [Fonctionnement d’Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Étapes suivantes d’Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [En savoir plus](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58--> 