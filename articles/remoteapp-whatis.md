<properties title="What is RemoteApp?" pageTitle="Présentation de RemoteApp ?" description="Découvrez Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo" manager="kathyw" />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" ms.manager="mbaldwin" />

#Présentation d'Azure RemoteApp
 
Azure RemoteApp fournit les fonctionnalités du programme Microsoft RemoteApp local, utilisant les services Bureau à distance, dans Azure. Azure RemoteApp vous permet de proposer un accès à distance sécurisé aux applications sur de nombreux appareils utilisateur.

Quand vous déplacez RemoteApp dans Azure, vous bénéficiez du stockage, de l'extensibilité et de l'envergure d'Azure sans configuration locale complexe. Microsoft se charge de la maintenance d'Azure, ce qui garantit sa fiabilité et vous permet de vous concentrer sur des tâches plus importantes, comme la création des applications qui conviennent le mieux à votre entreprise. Un autre avantage d'Azure RemoteApp est l'accessibilité. Vos utilisateurs peuvent accéder aux programmes RemoteApp à partir de Windows, iOS, Mac OS X et des appareils Android. Ils peuvent utiliser vos applications dans l'environnement qu'ils préfèrent, et le portail de gestion Azure vous permet de gérer ces applications. 

Continuez votre lecture pour en savoir plus sur RemoteApp ou, si nous vous avons déjà convaincu, [essayez-le maintenant](http://azure.microsoft.com/fr-fr/services/remoteapp/).

Vous avez des questions sur Azure RemoteApp ? Consultez notre [FAQ](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-faq/).

Azure RemoteApp fait partie de l'[infrastructure VDI (Microsoft Virtual Desktop Infrastructure)](http://www.microsoft.com/fr-fr/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nouveau !** Vous voulez en savoir plus sur Azure RemoteApp ? Vous êtes prêt à utiliser RemoteApp à grande échelle ? Participez à notre webinaire hebdomadaire [Demandez aux experts](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html).

##Options de déploiement de RemoteApp
Il existe deux types de collection RemoteApp :


- Une **collection cloud** est hébergée dans le cloud Azure et stocke toutes les données des programmes qui y figurent. Les utilisateurs peuvent accéder aux applications en se connectant avec leur compte Microsoft ou leurs informations d'identification d'entreprise synchronisées ou fédérées avec Azure Active Directory.
- Une **collection hybride** est hébergée dans le cloud Azure et y stocke également les données, mais elle permet aussi aux utilisateurs d'accéder aux données et aux ressources stockées sur votre réseau local. Les utilisateurs peuvent accéder aux applications en se connectant avec leurs informations d'identification d'entreprise synchronisées ou fédérées avec Azure Active Directory.

###Collection cloud

La [collection cloud RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-cloud-deployment/) est un moyen autonome d'héberger des applications dans le cloud. Une collection cloud existe uniquement dans le cloud Azure et ne se connecte pas à votre réseau local.

Dans le cadre de la version d'évaluation de RemoteApp, nous vous fournissons les applications Office 365 ProPlus ou Office 2013 préinstallées et prêtes à être partagées avec vos utilisateurs. Si vous choisissez d'utiliser les logiciels disponibles, vous pouvez configurer rapidement votre service.

Un autre avantage de l'utilisation de la collection cloud avec les applications Office est que les applications et le système d'exploitation (sur lequel votre service est généré) sont toujours mis à jour via des mises à jour régulières, et la protection de point de terminaison anti-logiciel malveillant de Microsoft offre une protection continue. Vos utilisateurs finaux utilisent leur compte Microsoft ou leurs informations d'identification d'entreprise pour accéder aux applications. L'administrateur n'a plus qu'à déterminer les utilisateurs qui peuvent accéder à telle ou telle application.

Vous pouvez également créer une collection cloud pour partager une application personnalisée ou un ensemble d'applications avec vos utilisateurs. Pour ce faire, vous devez [créer une image de modèle personnalisée](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/) (ce qui permet de publier des applications dans RemoteApp) et choisir simplement cette image (au lieu de l'image Office 2013) quand vous créez votre collection. 

###Collection hybride
La [collection hybride de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-hybrid-deployment/) vous permet de fournir à la fois un ensemble personnalisé d'applications à vos utilisateurs et l'accès aux données et aux ressources de votre réseau local. Contrairement à une image personnalisée utilisée avec la collection cloud, l'image que vous créez pour une collection hybride exécute des applications dans un environnement joint à un domaine, ce qui permet d'accorder un accès complet à votre réseau local et vos données.

En intégrant Active Directory à Azure Active Directory (à l'aide de DirSync), vos utilisateurs peuvent se servir de leurs informations d'identification d'entreprise pour accéder aux applications et aux données. Quand vous utilisez un compte professionnel dans Active Directory, vous pouvez utiliser vos stratégies d'entreprise dans le cloud pour contrôler les applications que vous proposez via RemoteApp.

Si vous générez votre image de modèle dans Windows Server 2012 R2 avec le service de rôle Hôte de session Bureau à distance, les applications que vous pouvez publier pour vos utilisateurs présentent quelques limites. Si les applications fonctionnent correctement dans cet environnement d'image de modèle, vos utilisateurs finaux peuvent y accéder via RemoteApp. 

###Mise à jour de votre collection
L'une des principales différences entre les collections hybride et cloud est la façon dont sont gérées les mises à jour. Avec une collection cloud qui utilise l'image préinstallée d'Office 365 ProPlus ou d'Office 2013, vous n'avez pas à vous soucier des mises à jour. Le service gère lui-même les mises à jour et les déploie de manière continue, à la fois pour les applications et pour le système d'exploitation.

Pour les collections hybrides, ainsi que pour les collections cloud qui utilisent une image de modèle personnalisée, vous êtes responsable de la maintenance de l'image et des applications. Pour les images jointes à un domaine, vous pouvez contrôler les mises à jour à l'aide d'outils tels que Windows Update, la stratégie de groupe ou System Center.

Après la mise à jour de votre image de modèle personnalisée, téléchargez la nouvelle image dans le cloud Azure, puis mettez à jour la collection pour qu'elle utilise la nouvelle image. (Vous pouvez le faire dans la page Démarrage rapide de RemoteApp ou à partir du tableau de bord.)

##Clients RemoteApp pris en charge
Azure RemoteApp est pris en charge par les applications clientes RemoteApp pour Windows et Windows RT, ainsi que par les applications Bureau à distance Microsoft pour Mac, iOS et Android. Vos utilisateurs peuvent utiliser ces applications sur leurs appareils mobiles ou de traitement pour accéder aux nouveau programmes RemoteApp.

##Étapes suivantes
C'est parti ! Essayez-le ! Ces articles vous aider à prendre en main RemoteApp :

- [Création d'une image de modèle personnalisée pour RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-custom-image/)
- [Création d'une collection cloud de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-cloud-deployment/)
- [Création d'une collection hybride de RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-create-hybrid-deployment/)
- [Fonctionnement des licences dans RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-licensing/)
- [Meilleures pratiques pour l'utilisation d'Azure RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-bestpractices/)
- [FAQ Azure RemoteApp](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-faq/)

<!--HONumber=35.2-->
