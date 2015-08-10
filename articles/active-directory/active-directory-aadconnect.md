<properties 
	pageTitle="Intégration de vos identités locales avec Azure Active Directory" 
	description="Cette page décrit Azure AD Connect et vous explique pourquoi l’utiliser." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="billmath"/>

# Intégration de vos identités locales avec Azure Active Directory



Aujourd'hui, les utilisateurs veulent pouvoir accéder à des applications à la fois en local et dans le cloud. Ils veulent pouvoir le faire à partir de n’importe quel appareil, qu’il s’agisse d’un ordinateur portable, d’un smartphone ou d’une tablette. C’est pourquoi vous et votre organisation devez être en mesure de leur fournir une méthode pour accéder à ces applications. Cependant, le basculement intégral vers le cloud n’est pas toujours une option possible.

<center>![What is Azure AD Connect](./media/active-directory-aadconnect/arch.png)</center>

Avec l’introduction d’Azure Active Directory Connect, l’accès à ces applications et le basculement vers le cloud sont devenus plus faciles. Azure AD Connect offre les avantages suivants :

- Vos utilisateurs peuvent se connecter avec une identité commune à la fois dans le cloud et en local. Ils n’ont pas besoin de mémoriser plusieurs mots de passe ou comptes et les administrateurs n’ont plus à se soucier de la surcharge supplémentaire due à une multiplication des comptes.
- Un outil unique et une utilisation encadrée facilitent la connexion à vos annuaires locaux avec Azure Active Directory. Une fois installé, l’Assistant déploie et configure tous les composants requis pour le démarrage et le bon fonctionnement de votre intégration d’annuaire, notamment les services de synchronisation, la synchronisation des mots de passe ou AD FS, ainsi que les prérequis tels que le module Azure AD PowerShell.



<center>![What is Azure AD Connect](./media/active-directory-aadconnect/azuread.png)</center>




## Pourquoi utiliser Azure AD Connect 

L’intégration de vos annuaires locaux avec Azure AD améliore la productivité de vos utilisateurs en leur fournissant une identité commune pour accéder aux ressources cloud et locales. Cette intégration procure aux utilisateurs et aux organisations les avantages suivants :
	
* Les organisations peuvent fournir aux utilisateurs une identité hybride commune entre différents services locaux ou cloud exploitant Windows Server Active Directory et la connexion à Azure Active Directory. 
* Les administrateurs peuvent fournir un accès conditionnel basé sur des ressources d’application, des identités d’appareil et d’utilisateur, un emplacement réseau et une authentification multifacteur.
* Les utilisateurs peuvent exploiter leur identité commune via des comptes dans d’Azure AD vers Office 365, Intune, des applications SaaS et des applications tierces.  
* Les développeurs peuvent créer des applications qui exploitent le modèle d’identité commune en intégrant des applications dans une version locale d’Active Directory ou des applications cloud Azure.

Azure AD Connect facilite cette intégration et simplifie la gestion de votre infrastructure d’identité locale et cloud.



----------------------------------------------------------------------------------------------------------
## Téléchargez Azure AD Connect

Pour commencer à utiliser Azure AD Connect, vous pouvez télécharger la dernière version à l’adresse suivante : [Télécharger Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

----------------------------------------------------------------------------------------------------------

## Fonctionnement d’Azure AD Connect


Azure Active Directory Connect est constitué de trois parties principales. Il s’agit des services de synchronisation, de la partie facultative Active Directory Federation Services. La partie surveillance est effectuée à l’aide d’[Azure AD Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png) </center>

- Synchronisation : cette partie est constituée des composants et des fonctionnalités précédemment publiés sous le nom de synchronisation d’annuaires et AAD Sync. Il s’agit de la partie chargée de créer les utilisateurs et les groupes. Elle est également chargée de s’assurer que les informations relatives aux utilisateurs et aux groupes dans votre environnement local correspondent à celles qui se trouvent dans le cloud.
- AD FS : ceci est une partie facultative d’Azure AD Connect, qui peut être utilisée pour configurer un environnement hybride à l’aide d’une infrastructure AD FS locale. Cette partie permet aux organisations de faire face à des déploiements complexes, par exemple l’authentification unique de jonction de domaine, l’application de la stratégie de connexion AD ou l’utilisation de cartes à puce ou d’une solution tierce d’authentification multifacteur. Pour plus d’informations sur la configuration de l’authentification unique, consultez [DirSync avec authentification unique](https://msdn.microsoft.com/library/azure/dn441213.aspx).
- Analyse d’intégrité : pour les déploiements complexes avec AD FS, Azure AD Connect peut assurer la surveillance de vos serveurs de fédération et offrir l’accès à un emplacement central dans le portail Azure pour afficher ces activités. Pour plus d’informations, consultez [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx).


### Composants de prise en charge d’Azure AD Connect

Voici une liste de prérequis et de composants de prise en charge qu’Azure AD Connect installe sur le serveur sur lequel vous configurez Azure AD Connect. Cette liste est destinée à une installation Express de base. Si vous choisissez d’utiliser un autre serveur SQL Server sur la page d’installation des services de synchronisation, les composants SQL Server 2012 ci-dessous ne sont pas installés.

- Connecteur Azure AD d’Azure AD Connect
- Utilitaires de ligne de commande Microsoft SQL Server 2012
- Client natif Microsoft SQL Server 2012
- Base de données locale Microsoft SQL Server 2012 Express
- Module Azure Active Directory pour Windows PowerShell
- Assistant de connexion Microsoft Online Services pour les professionnels de l’informatique
- Package de redistribution Microsoft Visual C++ 2013






## Prise en main d’Azure AD Connect



La documentation suivante vous aide à faire vos premiers pas avec Azure Active Directory Connect. Cette documentation traite de l’installation rapide d’Azure AD Connect. Pour plus d'informations sur une installation personnalisée, consultez [Installation personnalisée pour Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Pour plus d’informations sur la mise à niveau depuis DirSync vers Azure AD Connect, consultez [Mise à niveau de DirSync pour Azure Active Directory Connect.](active-directory-aadconnect-dirsync-upgrade-get-started.md)


### Avant d’installer Azure AD Connect
Avant d’installer Azure AD Connect avec les paramètres Express, voici ce dont vous aurez besoin.


 
- Un abonnement Azure ou un [abonnement d'essai Azure](http://azure.microsoft.com/pricing/free-trial/). Cette condition est nécessaire uniquement pour accéder au portail Azure mais pas pour l'utilisation d’Azure AD Connect. Si vous utilisez PowerShell ou Office 365, vous n’avez pas besoin d’un abonnement Azure pour utiliser Azure AD Connect.
- Le compte d’administrateur global Azure AD du locataire Azure AD que vous souhaitez intégrer
- Azure Active Directory Connect doit être installé sur Windows Server 2008 ou version ultérieure. Ce serveur peut être un contrôleur de domaine ou un serveur membre.
- La version de schéma Active Directory et le niveau de forêt doit être Windows Server 2003 ou version ultérieure. Les contrôleurs de domaine peuvent exécuter n’importe quelle version aussi longtemps que les exigences relatives au schéma et le niveau de forêt sont remplies.
- Si les services de fédération Active Directory sont dépoloyés, les serveurs sur lesquels AD FS doivent être installés doivent être Windows Server 2012 ou version ultérieure.
- Un compte d’administrateur d’entreprise pour votre Active Directory local
- Facultatif : un compte d’utilisateur test pour vérifier la synchronisation. 

#### Configuration matérielle requise pour Azure AD Connect
Le tableau ci-dessous présente la configuration minimale requise pour l’ordinateur Azure AD Connect.

| Nombre d’objets dans Active Directory | UC | Mémoire | Taille du disque dur |
| ------------------------------------- | --- | ------ | --------------- |
| Moins de 10 000 | 1,6 GHz | 4 Go | 70 Go |
| Entre 10 000 et 50 000 | 1,6 GHz | 4 Go | 70 Go |
| Entre 50 000 et 100 000 | 1,6 GHz | 16 Go | 100 Go |
| Dans les systèmes comportant 100 000 objets ou plus, la version complète de SQL Server est requise.| | | |
| Entre 100 000 et 300 000 | 1,6 GHz | 32 Go | 300 Go |
| Entre 300 000 et 600 000 | 1,6 GHz | 32 Go | 450 Go |
| Plus de 600 000 | 1,6 GHz | 32 Go | 500 Go |




Pour les options personnalisées telles que les forêts multiples ou des authentifications fédérées, découvrez les conditions requises supplémentaires [ici.](active-directory-aadconnect-get-started-custom.md)


### Installation rapide pour Azure AD Connect
La sélection des paramètres Express est l’option par défaut et s’applique à la plupart des scénarios. Ce faisant, Azure AD Connect déploie la synchronisation avec l’option de synchronisation du hachage de mot de passe. Cela concerne une seule forêt et permet aux utilisateurs d’utiliser leur mot de passe local pour se connecter au cloud. L’utilisation des paramètres Express lancera automatiquement une synchronisation une fois l’installation terminée (mais vous pouvez également choisir de ne pas le faire). Avec cette option, l’extension de votre répertoire local dans le cloud n’est plus qu’à quelques clics.

<center>![Bienvenu dans Azure AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)</center>

#### Pour installer Azure AD Connect à l'aide de paramètres Express
--------------------------------------------------------------------------------------------

1. Connectez-vous en tant qu’administrateur d’entreprise au serveur sur lequel vous souhaitez installer Azure AD Connect. Il doit s'agir du serveur que vous choisissez comme serveur de synchronisation.
2. Accédez à AzureADConnect.msi et double-cliquez sur ce fichier.
3. Sur l'écran d’accueil, sélectionnez la case pour accepter les termes du contrat de licence et cliquez sur **Continuer**.
4. Sur l'écran Paramètres Express, cliquez sur **Utiliser les paramètres Express**.
<center>![Bienvenue dans Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)</center>
6. Sur l’écran la connexion à l'écran d'Azure AD, entrez le nom d'utilisateur et un mot de passe d'administrateur global Azure pour votre instance Azure AD. Cliquez sur **Next**.
8. Sur l’écran Connexion à AD DS, entrez le nom d'utilisateur et le mot de passe d’un compte d'administrateur d’entreprise. Cliquez sur **Next**.
<center>![Bienvenu dans Azure AD Connect](./media/active-directory-aadconnect-get-started/install4.png)</center>
9. Sur l’écran Prêt à configurer, cliquez sur **Installer**.
	- Sur la page Prêt à configurer, vous pouvez éventuellement la case à cocher « \*\*Démarrer le processus de synchronisation dès que la configuration est terminée\*\* ». Si vous faites cela, l'assistant configurera la synchronisation, mais laissera la tâche désactivée afin qu'elle ne s’exécute pas avant que vous ne l'activiez manuellement dans le Planificateur de tâches. Une fois que la tâche est activée, la synchronisation s'exécute toutes les trois heures.
	- Éventuellement, vous pouvez également choisir de configurer les services de synchronisation pour le **déploiement hybride Exchange** en cochant la case à cocher correspondante. Si vous n'envisagez pas d'avoir des boîtes aux lettres Exchange dans le cloud et en local, vous n’avez pas besoin de cela.

<center>![Bienvenue dans Azure AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)</center>
8. Une fois l'installation terminée, cliquez sur **Quitter**.


<br> <br>

Suivez ce lien pour une vidéo sur l’utilisation de l'installation rapide :

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>



### Vérification de l’installation

Une fois que vous avez correctement installé Azure AD Connect vous pouvez vérifier que la synchronisation s’effectue en vous connectant au portail Azure et vérifiant l’heure de la dernière synchronisation.

1.  Connectez-vous au portail Azure.
2.  Sélectionnez Active Directory à gauche.
3.  Double-cliquez sur le répertoire que vous venez d'utiliser pour configurer Azure AD Connect.
4.  En haut, sélectionnez l'intégration de répertoires. Notez l’heure de la dernière synchronisation.

<center>![Installation rapide](./media/active-directory-aadconnect-get-started/verify.png)</center>

## Gestion d’Azure AD Connect 



Les rubriques suivantes relatives aux fonctionnalités avancées vous permettent de personnaliser Azure Active Directory Connect pour l’adapter aux besoins et aux spécifications de votre organisation.

### Attribution des licences aux utilisateurs Azure AD Premium et Enterprise Mobility

Maintenant que vos utilisateurs ont été synchronisés dans le cloud, vous devez leur attribuer une licence afin qu’ils puissent commencer à utiliser les applications de cloud telles qu’Office 365.

#### Pour l’attribution d’une licence Azure AD Premium ou Enterprise Mobility Suite
--------------------------------------------------------------------------------
1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez **Active Directory** à gauche.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page du répertoire, sélectionnez **Licences**.
5. Sur la page des licences, sélectionnez Active Directory Premium ou Enterprise Mobility Suite, puis cliquez sur **Assign**.
6. Dans la boîte de dialogue, sélectionnez les utilisateurs auxquels vous souhaitez attribuer des licences, puis cliquez sur l’icône de coche pour enregistrer les modifications.


### Vérification de la tâche de synchronisation planifiée
Si vous souhaitez vérifier l’état d’une synchronisation, vous pouvez le faire dans le portail Azure.

#### Pour la vérification de la tâche de synchronisation planifiée
--------------------------------------------------------------------------------

1. Connectez-vous au portail Azure en tant qu’administrateur.
2. Sélectionnez **Active Directory** à gauche.
3. Sur la page Active Directory, double-cliquez sur le répertoire qui contient les utilisateurs que vous souhaitez activer.
4. En haut de la page du répertoire, sélectionnez **Intégration du répertoire**.
5. Sous Intégration avec un répertoire actif local, notez l’heure de la dernière synchronisation.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

### Début de la tâche de synchronisation planifiée
Si vous devez exécuter une tâche de synchronisation, vous pouvez le faire en l’exécutant une nouvelle fois depuis l’Assistant Azure AD Connect. Vous devez fournir vos informations d’identification Azure AD. Dans l’Assistant, sélectionnez la tâche **Personnalisation des options de synchronisation** et cliquez sur Suivant dans l’Assistant. À la fin, vérifiez que la case **Démarrer le processus de synchronisation dès que la configuration initiale est terminée** est cochée.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>




### Tâches supplémentaires disponibles dans Azure AD Connect
Après l’installation initiale d’Azure AD Connect, vous pouvez toujours redémarrer l’Assistant depuis la page de démarrage ou le raccourci du bureau Azure AD Connect. Vous remarquerez qu’en passant à nouveau par l’Assistant de nouvelles options apparaissent sous forme de tâches supplémentaires.

Le tableau suivant fournit un résumé de ces tâches et une brève description de chacune d’elles.

<center>![Join Rule](./media/active-directory-aadconnect-whats-next/addtasks.png) </center>

Informations supplémentaires | Description 
------------- | ------------- |
Afficher le scénario sélectionné |Vous pouvez afficher votre solution Azure AD Connect actuelle. Cela inclut les paramètres généraux, les répertoires synchronisés, les paramètres de synchronisation, etc.
Personnaliser les options de synchronisation | Vous pouvez modifier la configuration actuelle, y compris l’ajout de forêts Active Directory supplémentaires à la configuration ou l’activation des options de synchronisation telles que l’utilisateur, le groupe, l’appareil ou le mot de passe en écriture différée.
Activation du mode intermédiaire | Cela vous permet d’organiser les informations qui seront synchronisées plus tard, mais rien ne sera exporté vers Azure AD ou Active Directory. Cela vous permet d’afficher un aperçu des synchronisations avant qu’elles ne s’effectuent.


 
### Documentation supplémentaire
Consultez les liens suivants pour obtenir de la documentation supplémentaire sur l’utilisation de Azure AD Connect :

- [Modification de la configuration par défaut de Azure AD Connect](active-directory-aadconnect-whats-next-change-default-config.md)
- [Utilisation de l’éditeur de règles de synchronisation Azure AD Connect](active-directory-aadconnect-whats-next-synch-rules-editor.md)
- [Utilisation de l’approvisionnement déclaratif](active-directory-aadconnect-whats-next-declarative-prov.md)

Une partie de la documentation qui a été créée pour Azure AD Sync est toujours adaptée et s’applique à Azure AD Connect. Bien que nous nous efforcions de transférer cette documentation sur Azure.com, une partie se trouve encore dans la bibliothèque MSDN. Pour plus de documentation, consultez [Azure AD Connect sur MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx) et [Azure AD Sync sur MSDN](https://msdn.microsoft.com/library/azure/dn790204.aspx).


**Ressources supplémentaires**



Présentation d’Ignite 2015 sur l'extension de vos répertoires locaux dans le cloud.

[AZURE.VIDEO microsoft-ignite-2015-extending-on-premises-directories-to-the-cloud-made-easy-with-azure-active-directory-connect]

[Synchronisation d’annuaires de plusieurs forêts avec le scénario d’authentification unique](https://msdn.microsoft.com/library/azure/dn510976.aspx) : intégrez plusieurs annuaires à Azure AD.

[Azure AD Connect Health](active-directory-aadconnect-health.md) : analysez le fonctionnement de votre infrastructure AD FS locale.

[FAQ Azure D Connect](active-directory-aadconnect-faq.md) - Forum aux questions concernant Azure AD Connect.






 

<!---HONumber=July15_HO5-->