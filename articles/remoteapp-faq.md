<properties title="Azure RemoteApp FAQ" pageTitle="FAQ Azure RemoteApp" description="Découvrez Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="mbaldwin" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#FAQ Azure RemoteApp
Les questions suivantes sont celles qui nous ont été posées sur Azure RemoteApp. Vous avez d'autres questions ? Visitez les [forums RemoteApp](https://social.msdn.microsoft.com/Forums/azure/fr-fr/home?forum=AzureRemoteApp) et indiquez-nous ce que vous souhaitez savoir.

##Présentation d'Azure RemoteApp##


- **Qu'est-ce qu'Azure RemoteApp ?** RemoteApp est un service Azure qui fournit les fonctionnalités de l'application Microsoft RemoteApp locale, utilisant les Services Bureau à distance, dans Azure. RemoteApp vous permet de proposer un accès à distance sécurisé aux applications sur de nombreux appareils utilisateur. En savoir plus sur [Azure RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-whatis/).
- **Quels sont les deux types d'options de déploiement ?** Il existe deux types de déploiements RemoteApp (ou collections) : cloud et hybride. Déterminez l'[option de déploiement](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-whatis/) qui convient le mieux à votre organisation.

##Configurations prises en charge ##


- **Les applications métier personnalisées sont-elles prises en charge ?** Oui. Pour utiliser une application personnalisée dans Azure RemoteApp, créez une [image de modèle personnalisée](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/), puis téléchargez-la dans la collection RemoteApp.
- **Mon application métier personnalisée fonctionnera-t-elle dans Azure RemoteApp ?** Le meilleur moyen de le savoir est de la tester. Consultez les [conditions de compatibilité des applications](http://www.microsoft.com/fr-fr/download/details.aspx?id=18704) et le [Centre de compatibilité Bureau à distance](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Quelle méthode de déploiement (cloud ou hybride) convient le mieux à mon organisation ?** Les collections hybrides fournissent l'expérience la plus complète pour une intégration totale de l'authentification unique et de la connectivité de réseau local sécurisée. Les collections cloud offrent un moyen souple et facile d'isoler votre déploiement à l'aide de plusieurs méthodes d'authentification. En savoir plus sur les [options de déploiement](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-whatis/).
- **La collection hybride requiert un réseau virtuel. Est-il possible d'utiliser notre réseau existant ?** Pas pour l'instant, mais nous sommes conscients que vous aimeriez l'utiliser. En attendant, vous pouvez connecter votre réseau virtuel existant au réseau virtuel Azure RemoteApp en suivant [ces instructions](http://blogs.msdn.com/b/rds/archive/2014/07/21/how-to-link-azure-remoteapp-to-an-existing-vnet.aspx).
- **Puis-je utiliser un cloud ou une machine virtuelle existante comme modèle pour ma collection RemoteApp ?** Pas pour l'instant, mais il s'agit d'une demande récurrente sur notre [site de commentaires](http://feedback.azure.com/forums/247748-azure-remoteapp/suggestions/5923679-allow-creation-of-custom-hybrid-image-completely-w).
- **Nous disposons d'une base de données SQL ou autre localement ou dans Azure. Quel type de déploiement doit-on utiliser ?** Cela dépend de l'emplacement de votre base de données SQL ou principale. Si la base de données se trouve dans un réseau privé, utilisez la collection hybride. Si la base de données est disponible sur Internet et autorise des connexions client, vous pouvez utiliser la collection cloud.
- **Qu'en est-il du mappage de lecteurs, des ports USB et série, du partage du Presse-papiers et de la redirection d'imprimantes ?** Toutes ces fonctionnalités sont prises en charge dans Azure RemoteApp. Le partage du Presse-papiers et la redirection d'imprimantes sont activés par défaut. [Contactez-nous ](mailto:remoteappforum@microsoft.com? subject=Azure%20RemoteApp%20enable%20Redirection%20request) pour activer le mappage de lecteurs ou la redirection des ports USB et série. (Nous travaillons à l'ajout de cette prise en charge dans le portail, mais elle n'existe pas encore).
- **Qu'en est-il de l'authentification ? Quelles sont les méthodes prises en charge ?** La collection cloud prend en charge les comptes Microsoft et Azure Active Directory, qui sont également des comptes Office 365. La collection hybride prend en charge uniquement les comptes Azure Active Directory synchronisés (à l'aide d'un outil tel que DirSync) à partir d'un déploiement de Windows Server Active Directory. Plus précisément, ils doivent être synchronisés avec l'option de synchronisation de mot de passe ou la fédération des services ADFS doit être configurée. Vous pouvez également configurer l'[authentification multifacteur](http://azure.microsoft.com/fr-fr/documentation/services/multi-factor-authentication/).

	**Remarque :** les utilisateurs Azure Active Directory doivent se trouver dans le locataire associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l'onglet **Paramètres** du portail. Consultez [Modification du locataire Azure Active Directory utilisé par RemoteApp](http://msdn.microsoft.com/fr-fr/3d6c4fd1-c981-4c57-9402-59fe31b11883) pour plus d'informations.)

- **Pourquoi ne puis-je pas donner accès à mon compte Azure Active Directory ?** Les utilisateurs Azure Active Directory doivent se trouver dans l'annuaire associé à votre abonnement. Vous pouvez afficher ou modifier cet annuaire sous l'onglet Paramètres du portail. Consultez [Modification du locataire Azure Active Directory utilisé par RemoteApp](http://msdn.microsoft.com/fr-fr/3d6c4fd1-c981-4c57-9402-59fe31b11883) pour plus d'informations.
- **Quels sont les appareils et les systèmes d'exploitation pris en charge par les applications clientes ?** Les applications clientes sont disponibles pour Windows 8.1, Windows 8, Windows 7 Service Pack 1, iOS, Mac OS X, Windows RT, les appareils Android et Windows Phone. Nous prenons également en charge la version préliminaire de Windows 10.
 
	[Téléchargez](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) un client RemoteApp dès maintenant.
- **Azure RemoteApp prend-il en charge les clients légers ?** Oui, les clients légers Windows Embedded suivants sont pris en charge :
	- Windows Embedded Standard 7 avec Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **Quelle version de Windows Server est prise en charge pour l'hôte de session Bureau à distance (RDSH) ?** Windows Server 2012 R2.

##Support et commentaires

- **Puis-je essayer ce service gratuitement ?** Oui. Une version d'évaluation gratuite est disponible pendant 30 jours. À la fin de la période d'essai, vous pouvez passer à un compte payant (utilisable en production) ou arrêter d'utiliser le service. Démarrez votre version d'évaluation gratuite en accédant à [manage.windowsazure.com](http://manage.windowsazure.com), créer une nouvelle instance de RemoteApp. Avec la version d'évaluation gratuite, vous pouvez générer 2 instances de RemoteApp avec 10 utilisateurs par instance. N'oubliez pas que cette version d'évaluation ne dure que 30 jours.
- **Quel est le plan de support de RemoteApp ?** Le support pour la facturation et la gestion des abonnements est proposé gratuitement. Le support technique est disponible via les [plans de service Azure](http://azure.microsoft.com/support/plans/). Vous pouvez également bénéficier gratuitement du support de la communauté via notre [forum de discussion Azure](http://social.msdn.microsoft.com/Forums/windowsazure/fr-fr/home?forum=AzureRemoteApp).
- **Combien coûte RemoteApp ?** Consultez les [Informations de tarification d'Azure RemoteApp](http://azure.microsoft.com/fr-fr/pricing/details/remoteapp/).
- **Comment envoyer des commentaires ?** Visitez le [forum des commentaires](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **À qui puis-je m'adresser pour en savoir plus sur Azure RemoteApp ?** Outre notre [forum de discussion](http://social.msdn.microsoft.com/Forums/windowsazure/fr-fr/home?forum=AzureRemoteApp), qui est l'endroit idéal pour poser des questions, vous pouvez participer au webinaire hebdomadaire [Demandez aux experts](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), qui aborde tout ce qui concerne RemoteApp.
- **Qu'en est-il de la documentation RemoteApp ?** Merci d'avoir posé la question. Outre le contenu du volet d'aide du portail (cliquez simplement sur le symbole **?** d'une page du portail), les articles suivants vous expliquent tout ce que vous devez savoir sur RemoteApp :
	- **Prise en main :**
		- [Présentation de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-whatis/)
		- [Que contiennent les images de modèle RemoteApp ?](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-images/)
		- [Comment fonctionne la gestion de licences ?](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-licensing/)
		- [Comment RemoteApp et Office fonctionnent ensemble ?](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-o365/)
	- **Déploiement :**
		- [Création d'une image de modèle personnalisée](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/)
		- [Création d'une collection hybride](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-hybrid-deployment/)
		- [Création d'une collection cloud](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-cloud-deployment/)
		- [Configuration d'Azure Active Directory pour RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-ad/)
		- [Publication d'une application dans RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-publish/)
	- **Gestion :**
		- [Ajouter des utilisateurs](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-user/)
		- [Meilleures pratiques pour la configuration et l'utilisation de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-bestpractices/)	

	Vidéos Nous vous proposons également un certain nombre de vidéos sur RemoteApp. Certaines présentent le service ([Présentation d'Azure RemoteApp](http://azure.microsoft.com/fr-fr/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) et d'autres vous guident dans le déploiement ([Déploiement cloud](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) et [Déploiement hybride](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Consultez-les !


<!--HONumber=35.2-->
