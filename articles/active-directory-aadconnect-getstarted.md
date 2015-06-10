<properties 
	pageTitle="Prise en main d’Azure AD Connect" 
	description="Découvrez comment télécharger, installer et exécuter l’Assistant d’installation d’Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="azure-active-directory-connect" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Prise en main d’Azure AD Connect

La documentation suivante vous aide à faire vos premiers pas avec Azure Active Directory Connect.

## Téléchargez Azure AD Connect



Pour commencer à utiliser Azure AD Connect, vous pouvez télécharger la dernière version à l’adresse suivante : [Télécharger la version préliminaire publique d’Azure AD Connect](http://connect.microsoft.com/site1164/program8612)

## Avant d’installer Azure AD Connect
Avant d’installer Azure AD Connect, voici ce dont vous aurez besoin.

- Un abonnement Azure ou un [abonnement d’évaluation Azure](http://azure.microsoft.com/pricing/free-trial/)
- Azure AD Premium ou [Azure AD Premium en version d’évaluation](http://aka.ms/aadptrial)
- Le compte d’administrateur global Azure AD du locataire Azure AD que vous souhaitez intégrer
- Un contrôleur de domaine ou un serveur membre AD avec Windows Server 2008 ou version ultérieure
- Un compte administrateur d’entreprise pour votre Active Directory local
- Facultatif : un compte d’utilisateur test pour vérifier la synchronisation. 

Si vous prévoyez d’utiliser l’authentification unique avec l’option AD FS, vous aurez également besoin des éléments suivants :

- Les informations d’identification de l’administrateur local des serveurs de fédération.
- Les informations d’identification de l’administrateur local des serveurs des groupes de travail (ne faisant pas partie d’un domaine) sur lesquels vous avez l’intention de déployer le rôle de proxy d’application web.
- Un serveur Windows Server 2012 R2 pour le serveur de fédération.
- Un serveur Windows Server 2012 R2 pour le proxy d’application web.
-  Un fichier .pfx avec un certificat SSL pour le nom de votre service de fédération, par exemple fs.contoso.com.
- L’ordinateur sur lequel vous exécutez l’Assistant doit être en mesure de se connecter à n’importe quel autre ordinateur sur lequel vous souhaitez installer AD FS ou le proxy d’application web via Windows Remote Management.


## Installation d’Azure AD Connect

Une fois que vous avez téléchargé Azure AD Connect, utilisez les éléments suivants pour installer Azure AD Connect.

1. Connectez-vous en tant qu’administrateur d’entreprise au serveur sur lequel vous voulez installer Azure AD Connect.
2. Accédez à AzureADConnect.msi et double-cliquez sur ce fichier.
3. Suivez les étapes de l’Assistant en appliquant les paramètres Express ou les paramètres personnalisés.
4. Facultatif : utilisez votre compte d’utilisateur test pour vous connecter à un service cloud comme Office 365 pour le tester.

<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-getstarted/aadConnect_Welcome.png)</center>

## Configurations facultatives des services de synchronisation
Lorsque vous installez les services de synchronisation, vous pouvez laisser la section de configuration facultative de côté. Azure AD Connect configurera tout automatiquement. Cela comprend la configuration d’une instance de SQL Server 2012 Express et la création des groupes appropriés, avec attribution des autorisations. Si vous souhaitez modifier les valeurs par défaut, vous pouvez utiliser le tableau ci-dessous pour comprendre les différentes options de configuration facultatives.

Configuration facultative | Description 
------------- | ------------- |
Nom du serveur SQL Server |Permet de spécifier le nom du serveur SQL et le nom de l’instance. Choisissez cette option si vous avez déjà un serveur de base de données Active Directory que vous souhaitez utiliser.
Compte de service |Par défaut, Azure AD Connect crée le compte de service qu’utilisent les services de synchronisation. Le problème qui se pose est que le mot de passe est généré automatiquement et n’est pas connu de la personne qui installe Azure AD Connect. Dans la plupart des scénarios, cela ne pose aucun problème, mais si vous souhaitez appliquer certaines configurations avancées, par exemple choisir les unités organisationnelles à synchroniser, vous devez créer un compte et choisir votre mot de passe. |
Autorisations | Par défaut, Azure AD Connect crée quatre groupes lorsque les services de synchronisation sont installés. Ces groupes sont Administrateurs, Opérateurs, Parcourir et Réinitialisation du mot de passe. Si vous souhaitez spécifier vos propres groupes, vous pouvez le faire ici.
Paramètres d’importation |Utilisez cette option si vous importez des informations de configuration à partir de la synchronisation d’annuaires d’Azure AD Sync.|



## Installation Express
L’application des paramètres Express est l’option par défaut et s’applique à la plupart des scénarios. Ce faisant, Azure AD Connect déploie la synchronisation avec l’option de synchronisation du hachage de mot de passe. Cela concerne une seule forêt et permet aux utilisateurs d’utiliser leur mot de passe local pour se connecter au cloud. Avec l’installation Express, une synchronisation automatique est lancée une fois l’installation terminée. Avec cette option, l’extension de votre répertoire local vers le cloud se fait en six clics au plus.

<center>![Express Installation](./media/active-directory-aadconnect-getstarted/express.png)</center>

## Installation personnalisée

Avec l’installation personnalisée, vous pouvez sélectionner plusieurs options. Le tableau suivant décrit les pages de l’Assistant qui sont disponibles lorsque vous sélectionnez l’installation personnalisée.

Nom de la page | Description
-------------------    | ------------- | 
Connexion de l’utilisateur|Sur cette page, vous pouvez choisir s’il faut utiliser la synchronisation de mot de passe, la fédération avec AD FS ou aucune de ces deux options.
Connexion à vos annuaires|Sur cette page, vous pouvez ajouter un ou plusieurs annuaires auxquels se synchroniser.
Filtrage de la synchronisation| Ici, vous pouvez déterminer si vous souhaitez synchroniser tous les utilisateurs et les groupes ou si vous souhaitez spécifier un groupe par annuaire et ne synchroniser que ce groupe.
Identités locales|Ici, vous pouvez spécifier que les utilisateurs ne doivent exister qu’une seule fois dans tous les annuaires ajoutés à la page Connexion à vos annuaires ou bien s’ils peuvent exister dans plusieurs annuaires. Si les utilisateurs existent dans plusieurs annuaires, vous devez choisir un attribut qui identifie ces utilisateurs de façon unique dans les annuaires. Par exemple, l’attribut de messagerie, ObjectSID ou SAMAccountName, sont des attributs couramment utilisés pour identifier les utilisateurs de façon unique.
Identités Azure|Sur cette page, vous spécifiez le point d’ancrage de la source que vous souhaitez utiliser pour la fédération d’identités.
Fonctionnalités de l’option|Utilisez le tableau ci-dessous pour obtenir une brève description des fonctionnalités facultatives que vous pouvez sélectionner.

<center>![Express Installation](./media/active-directory-aadconnect-getstarted/of.png)</center>


Fonctionnalités facultatives | Description
-------------------    | ------------- | 
Déploiement Exchange hybride |La fonctionnalité de déploiement Exchange hybride permet la coexistence de boîtes aux lettres Exchange locales et dans Azure grâce à la synchronisation d’un jeu d’attributs spécifique d’Azure AD Connect dans votre répertoire local.
Application Azure AD et filtrage des attributs|En activant l’application Azure AD et le filtrage des attributs, l’ensemble des attributs synchronisés peut être adapté à un ensemble spécifique dans une page à venir de l’Assistant. Deux pages de configuration supplémentaires s’ouvrent dans l’Assistant.  
Écriture différée du mot de passe|En activant l’écriture différée du mot de passe, les modifications de mot de passe provenant d’Azure AD Connect sont réécrites dans votre annuaire local.
Écriture différée de l’utilisateur|En activant l’écriture différée de l’utilisateur, les utilisateurs créés dans Azure AD Connect sont réécrits dans votre annuaire local. Une page de configuration supplémentaire s’ouvre dans l’Assistant.  
Synchronisation de l’appareil|En activant la synchronisation des appareils, la configuration des appareils peut être écrite dans Azure AD.
Synchronisation des attributs des extensions d’annuaire|En activant la synchronisation des attributs des extensions d’annuaire, les attributs spécifiés seront synchronisés avec Azure AD. Une page de configuration supplémentaire s’ouvre dans l’Assistant.  

Pour plus d’options de configuration, comme la modification de la configuration par défaut, l’utilisation de l’éditeur de règles de synchronisation et l’approvisionnement déclaratif, voir [Gestion d’Azure AD Connect](active-directory-aadconnect-manage.md).

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
* [Fonctionnement d’Azure AD Connect](active-directory-aadconnect-howitworks.md)
* [Gestion d’Azure AD Connect](active-directory-aadconnect-manage.md)
* [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->