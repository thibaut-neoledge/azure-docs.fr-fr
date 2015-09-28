<properties 
	pageTitle="FAQ Azure RemoteApp" 
	description="Forum aux questions sur Azure RemoteApp." 
	services="remoteapp" 
	documentationCenter="" 
	authors="lizap" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="remoteapp" 
	ms.workload="compute" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/06/2015" 
	ms.author="elizapo"/>

# FAQ Azure RemoteApp
Les questions suivantes sont celles qui nous ont été posées sur Azure RemoteApp. Vous avez d'autres questions ? Visitez les [forums RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) et indiquez-nous ce que vous souhaitez savoir.

## Présentation d'Azure RemoteApp ##


- **Présentation d'Azure RemoteApp** RemoteApp est un service Azure qui vous permet de proposer un accès à distance sécurisé aux applications sur de nombreux appareils utilisateur. En savoir plus sur [Azure RemoteApp](remoteapp-whatis.md).
- **Quels sont les deux types d'options de déploiement ?** Il existe deux types de déploiements RemoteApp (ou collections) : cloud et hybride. Déterminez quelle [option de déploiement](remoteapp-whatis.md) convient le mieux à votre organisation.

## Configurations prises en charge ##

- **Quelles sont les limites du service ?** Vous pouvez en savoir plus sur les paramètres par défaut et les limites du service Azure RemoteApp dans [Abonnement Azure et limites, quotas et contraintes du service](azure-subscription-service-limits.md). N’hésitez pas à nous contacter si vous avez d'autres questions.
- **Combien d'utilisateurs dois-je avoir ?** Il existe un minimum de 20 utilisateurs. Il s’agit bien de la valeur MINIMALE. Vous serez facturé pour 20 utilisateurs. 
- **Les applications métier personnalisées sont-elles prises en charge ?** Oui. Pour utiliser une application personnalisée dans Azure RemoteApp, créez une [image de modèle personnalisée](remoteapp-create-custom-image.md), puis téléchargez-la dans la collection RemoteApp.
- **Mon application métier personnalisée fonctionnera-t-elle dans Azure RemoteApp ?** La meilleure façon de le savoir consiste à la tester. Examinez les [conditions de compatibilité des applications](http://www.microsoft.com/download/details.aspx?id=18704) et consultez le [Centre de compatibilité du Bureau à distance](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Quelle méthode de déploiement (cloud ou hybride) convient le mieux à mon organisation ?** Les collections hybride offrent une expérience plus riche si vous voulez une intégration complète avec authentification unique (SSO) et connectivité au réseau local sécurisée. Le collections cloud offrent un moyen souple et simple d'isoler votre déploiement à l'aide de plusieurs méthodes d'authentification. En savoir plus sur les [options de déploiement](remoteapp-whatis.md).
- **La collection hybride requiert un réseau virtuel. Est-il possible d'utiliser notre réseau virtuel existant ?** Oui, si le réseau virtuel existant est un réseau virtuel Azure. Consultez la section « Étape 1 : Configuration de votre réseau virtuel » dans les [instructions sur les collections hybrides](remoteapp-create-hybrid-deployment.md) pour plus d'informations.
- **Puis-je utiliser un cloud ou une machine virtuelle existante comme modèle pour ma collection RemoteApp ?** Oui. Vous pouvez créer une image basée sur une machine virtuelle Azure, utiliser une des images incluses dans votre abonnement ou créer une image personnalisée. Découvrez les [options d'images RemoteApp](remoteapp-imageoptions.md).
- **Nous disposons d'une base de données SQL ou autre localement ou dans Azure. Quel type de déploiement devons-nous utiliser ?** Cela dépend de l'emplacement de votre base de données SQL ou principale. Si la base de données se trouve dans un réseau privé, utilisez la collection hybride. Si la base de données est disponible sur Internet et autorise des connexions client, vous pouvez utiliser la collection cloud.
- **Qu'en est-il du mappage du lecteurs, des ports USB et série, du partage du Presse-papiers et de la redirection d'imprimante ?** Toutes ces fonctionnalités sont prises en charge dans Azure RemoteApp. Le partage du Presse-papiers et la redirection d'imprimante sont activés par défaut. Plus d'informations sur la redirection [ici](remoteapp-redirection.md). 


- **Qu'en est-il de l'authentification ? Quelles sont les méthodes prises en charge ?** La collection cloud prend en charge les comptes Microsoft et Azure Active Directory, qui sont également des comptes Office 365. La collection hybride prend en charge uniquement les comptes Azure Active Directory synchronisés (à l'aide d'un outil tel que [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) à partir d'un déploiement de Windows Server Active Directory. Plus précisément, ils doivent être synchronisés avec l'option de synchronisation de mot de passe ou la fédération des services ADFS doit être configurée. Vous pouvez également configurer l'[authentification multifacteur](../../services/multi-factor-authentication/).

	**Remarque :** les utilisateurs Azure Active Directory doivent provenir du locataire associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l'onglet **Paramètres** du portail. Consultez [Modifier le locataire Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) pour plus d'informations.)

- **Pourquoi ne puis-je pas donner l'accès à mon compte Azure Active Directory ?** Les utilisateurs Azure Active Directory doivent provenir de l'annuaire associé à votre abonnement. Vous pouvez afficher ou modifier cet annuaire sous l'onglet Paramètres du portail. Consultez [Modifier le locataire Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) pour plus d'informations.)
- **Quels appareils et systèmes d'exploitation sont pris en charge par les applications clientes ?** Les applications clientes sont disponibles pour Windows 8.1, Windows 8, Windows 7 Service Pack 1, iOS, Mac OS X, Windows RT, les appareils Android et Windows Phone. Nous prenons également en charge la version d'évaluation de Windows 10.
 
	[Téléchargez](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) un client RemoteApp dès maintenant.
- **Azure RemoteApp prend-il en charge les clients légers ?** En effet, les clients légers Windows Embedded suivants sont pris en charge :
	- Windows Embedded Standard 7 avec Service Pack 1
	- Windows Embedded POSReady7 
	- Windows Embedded Thin PC 
	- Windows Embedded 8.1 Industry

- **Quelle version de Windows Server est prise en charge pour l'hôte de session Bureau à distance (RDSH) ?** Windows Server 2012 R2.

##Support et commentaires

- **Puis-je essayer ce service gratuitement ?** Oui. Une version d'évaluation gratuite est disponible pendant 30 jours. Après la fin de l'essai, vous pouvez passer à un compte payant (que vous pouvez utiliser en production) ou arrêter d'utiliser le service. Commencez votre évaluation gratuite en accédant à [manage.windowsazure.com](http://manage.windowsazure.com) et créez une instance de RemoteApp. Avec la version d'évaluation gratuite, vous pouvez créer deux instances de RemoteApp avec 10 utilisateurs par instance. N'oubliez pas que cette version d'évaluation n'est utilisable que pendant 30 jours.
- **Quel est le plan de prise en charge pour RemoteApp ?** La gestion de la facturation et des abonnements est fournie gratuitement. Le support technique est disponible via les [plans de service Azure](../../../support/plans/). Vous pouvez également bénéficier du support gratuit de la communauté via notre [forum de discussion Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Combien coûte RemoteApp ?** Consultez les [tarifs détaillés d'Azure RemoteApp](../../../pricing/details/remoteapp/).
- **Comment envoyer des commentaires ?** Visitez le [forum prévu à cet effet](http://feedback.azure.com/forums/247748-azure-remoteapp).
- **À qui puis-je m'adresser pour en savoir plus sur Azure RemoteApp ?** Outre notre [forum de discussion](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), qui est l'endroit idéal pour poser des questions, vous pouvez participer au webinaire hebdomadaire [Demandez aux experts](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), qui aborde tout ce qui concerne RemoteApp.
- **Qu'en est-il de la documentation RemoteApp ?** Merci d'avoir posé la question. Outre le contenu du volet d'aide du portail (cliquez simplement sur le symbole **?** d'une page du portail), les articles suivants vous expliquent tout ce que vous devez savoir sur RemoteApp :
	- **Mise en route**
		- [Présentation de RemoteApp](remoteapp-whatis.md)
		- [Que contiennent les images de modèle RemoteApp ?](remoteapp-images.md)
		- [Comment fonctionne la gestion de licences ?](remoteapp-licensing.md)
		- [Comment RemoteApp et Office fonctionnent ensemble ?](remoteapp-o365.md)
		- [Comment la redirection fonctionne-t-elle dans RemoteApp ?](remoteapp-redirection.md)
	- **Déploiement**
		- [Création d'une image de modèle personnalisée](remoteapp-create-custom-image.md)
		- [Création d'une collection hybride](remoteapp-create-hybrid-deployment.md)
		- [Création d’une collection cloud](remoteapp-create-cloud-deployment.md)
		- [Configuration d'Azure Active Directory pour RemoteApp](remoteapp-ad.md)
		- [Publication d'une application dans RemoteApp](remoteapp-publish.md)
	- **Gestion**
		- [Ajout d'utilisateurs](remoteapp-user.md)
		- [Meilleures pratiques pour la configuration et l'utilisation de RemoteApp](remoteapp-bestpractices.md)	

	Vidéos Nous vous proposons également un certain nombre de vidéos sur RemoteApp. Certaines présentent le service ([Présentation d'Azure RemoteApp](http://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) et d'autres vous guident dans le déploiement ([Déploiement cloud](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) et [Déploiement hybride](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Consultez-les !

 

<!---HONumber=Sept15_HO3-->