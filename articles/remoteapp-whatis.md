<properties title="What is RemoteApp?" pageTitle="Présentation de RemoteApp" description="Learn about Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/05/2014" ms.author="elizapo" ms.manager="kathyw" />

#Présentation d'Azure RemoteApp

Azure RemoteApp offre les fonctionnalités du programme Microsoft RemoteApp local dans Azure, grâce aux services Bureau à distance. Azure RemoteApp vous permet d'offrir un accès à distance sécurisé aux applications à partir de nombreux appareils.

Lorsque vous placez RemoteApp dans Azure, vous profitez des capacités de stockage, d'extensibilité et de portée globale d'Azure sans avoir à vous soucier d'une configuration locale complexe. Microsoft se charge de la maintenance d'Azure, en assurant ainsi sa fiabilité, ce qui vous permet de vous consacrer à des questions plus importantes, par exemple la création d'applications de qualité pour votre entreprise. Un autre avantage d'Azure RemoteApp est son accessibilité : vos utilisateurs peuvent accéder aux programmes RemoteApp à partir de Windows, iOS, Mac OS X et Android. Ils peuvent utiliser vos applications dans l'environnement qu'ils préfèrent et vous pouvez gérer ces applications depuis le portail de gestion Azure. 

Lisez la suite pour plus d'informations sur RemoteApp, ou, si vous êtes déjà convaincu, [essayez-le maintenant](http://azure.microsoft.com/fr-fr/services/remoteapp/).

Azure RemoteApp fait partie de [Microsoft Virtual Desktop Infrastructure (VDI)](http://www.microsoft.com/fr-fr/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nouveau** Vous voulez en savoir plus sur Azure RemoteApp ? Vous êtes prêt à appliquer RemoteApp à grande échelle ? Participez à notre [webinaire hebdomadaire avec des experts](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html).

##Options de déploiement de RemoteApp
Il existe deux types de déploiement RemoteApp :


- Le **déploiement sur le cloud** est hébergé et stocke toutes les données des programmes dans le cloud Azure. Les utilisateurs peuvent accéder aux applications en se connectant avec leur compte Microsoft ou leurs informations d'identification d'entreprise, synchronisées ou fédérées avec Azure Active Directory.
- Le **déploiement hybride** est hébergé dans le cloud Azure et il y stocke les données. Il permet également aux utilisateurs d'accéder aux données et aux ressources stockées sur votre réseau local. Les utilisateurs peuvent accéder aux applications en se connectant avec leurs informations d'identification d'entreprise, synchronisées ou fédérées avec Azure Active Directory.

###Déploiement sur le cloud

Le [déploiement sur le cloud de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-cloud-deployment/) permet d'héberger des applications dans le cloud de façon autonome. Ce déploiement sur le cloud existe uniquement dans le cloud Azure, sans connexion à votre réseau local.

Dans le cadre de la version préliminaire de RemoteApp, nous vous fournissons les applications Office 2013 préinstallées et prêtes à être partagées avec vos utilisateurs. Si vous choisissez d'utiliser les logiciels disponibles, vous pouvez rapidement configurer votre service.

Un autre avantage supplémentaire du déploiement cloud avec les applications Office 2013 est que les applications et le système d'exploitation (sur lesquels votre service est basé) sont toujours tenus à jour via des mises à jour régulières et la protection des points de terminaison Microsoft Anti-Malware leur offre une protection continue. Vos utilisateurs finaux utilisent leur compte Microsoft ou les informations d'identification d'entreprise pour accéder aux applications. Tout ce dont vous, en tant qu'administrateur, devez vous soucier est de déterminer qui doit avoir accès à quelles applications.

Vous pouvez également créer un déploiement cloud pour partager avec vos utilisateurs une application personnalisée ou un jeu d'applications. Pour ce faire, vous devez [créer une image de modèle personnalisée](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/) (la manière dont sont publiées les applications sur RemoteApp). Ensuite, il suffit de choisir cette image (au lieu de l'image Office 2013) lorsque vous créez votre déploiement. 

###Déploiement hybride
Le déploiement [hybride de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-hybrid-deployment/) vous permet de proposer à la fois un ensemble personnalisé d'applications à vos utilisateurs et un accès aux données et ressources de votre réseau local. Contrairement à une image personnalisée utilisée avec le déploiement cloud, l'image que vous créez pour un déploiement hybride exécute les applications dans un environnement joint à un domaine, qui offre un accès complet à votre réseau et à vos données locales.

Grâce à l'intégration d'Active Directory à Azure Active Directory (avec DirSync), les utilisateurs peuvent se servir de leurs informations d'identification d'entreprise pour accéder aux applications et aux données. Lorsque vous utilisez un compte professionnel dans Active Directory, vous pouvez appliquer vos stratégies d'entreprise dans le cloud pour contrôler les applications que vous proposez via RemoteApp.

Tant que vous générez votre image de modèle sur Windows Server 2012 R2 avec le service de rôle Hôte de session Bureau à distance, il existe quelques limites pour les applications que vous pouvez publier pour vos utilisateurs. Si les applications fonctionnent correctement dans cet environnement d'image de modèle, vos utilisateurs peuvent y accéder via RemoteApp. 

###Mise à jour de votre déploiement
Une des principales différences entre les déploiements hybride et cloud est la gestion des mises à jour logicielles. Avec un déploiement cloud qui utilise l'image Office 2013 préinstallée, vous n'avez pas à vous soucier des mises à jour. Le service gère lui-même les mises à jour et les déploie en continu, aussi bien pour les applications que pour le système d'exploitation.

Pour les déploiements hybrides, ainsi que pour les déploiements cloud qui utilisent une image de modèle personnalisée, vous êtes responsable de la maintenance de l'image et des applications. Pour les images jointes à un domaine, vous pouvez contrôler les mises à jour à l'aide de certains outils comme Windows Update, les stratégies de groupe ou encore System Center.

Une fois l'image de modèle personnalisée mise à jour, téléchargez la nouvelle image vers le cloud Azure et mettez à jour le déploiement pour qu'il utilise cette nouvelle image. Vous pouvez le faire dans la page de démarrage rapide de RemoteApp ou dans le tableau de bord.

##Clients RemoteApp pris en charge
Dans le cadre de la version préliminaire d'Azure RemoteApp, nous avons publié une nouvelle application cliente Microsoft RemoteApp pour Windows et Windows RT, ainsi que les mises à jour pour les applications de Bureau à distance Microsoft pour iOS et Android. Vos utilisateurs peuvent utiliser ces applications sur leur appareil mobile ou sur leur ordinateur pour accéder aux nouveaux programmes RemoteApp.

##Étapes suivantes
OK Faites un essai. Ces articles vous aident à prendre en main RemoteApp :

- [Création d'une image de modèle personnalisée pour RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/)
- [Création d'un déploiement cloud de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-cloud-deployment/)
- [Création d'un déploiement hybride de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-hybrid-deployment/)
