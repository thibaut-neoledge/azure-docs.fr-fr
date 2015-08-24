<properties 
	pageTitle="Présentation d'Azure RemoteApp" 
	description="Découvrez Azure RemoteApp." 
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
	ms.date="08/05/2015" 
	ms.author="elizapo"/>

# Présentation d'Azure RemoteApp

Azure RemoteApp offre les fonctionnalités du programme Microsoft RemoteApp local dans Azure, grâce aux services Bureau à distance. Azure RemoteApp vous permet de proposer un accès à distance sécurisé aux applications sur de nombreux appareils utilisateur.

Lorsque vous placez RemoteApp dans Azure, vous profitez des capacités de stockage, d'extensibilité et de portée globale d'Azure sans avoir à vous soucier d'une configuration locale complexe. Microsoft se charge de la maintenance d'Azure, en assurant ainsi sa fiabilité, ce qui vous permet de vous consacrer à des questions plus importantes, par exemple la création d'applications de qualité pour votre entreprise. Un autre avantage d'Azure RemoteApp est son accessibilité : vos utilisateurs peuvent accéder aux programmes RemoteApp à partir de Windows, iOS, Mac OS X et Android. Ils peuvent utiliser vos applications dans l'environnement qu'ils préfèrent et vous pouvez gérer ces applications depuis le portail de gestion Azure.

Lisez la suite pour plus d'informations sur RemoteApp, ou, si vous êtes déjà convaincu, [essayez-le maintenant](http://azure.microsoft.com/services/remoteapp/).

Vous avez des questions sur Azure RemoteApp ? Consultez notre [FAQ](remoteapp-faq.md).

Azure RemoteApp fait partie de [Microsoft Virtual Desktop Infrastructure (VDI)](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nouveau !** Vous voulez en savoir plus sur Azure RemoteApp ? Vous êtes prêt à appliquer RemoteApp à grande échelle ? Participez à notre [webinaire hebdomadaire avec des experts](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## Collections RemoteApp
Il existe deux types de collection RemoteApp :


- Une **collection cloud** est hébergée dans le cloud Azure et stocke toutes les données des programmes qui y figurent. Les utilisateurs peuvent accéder aux applications en se connectant avec leur compte Microsoft ou leurs informations d'identification d'entreprise, synchronisées ou fédérées avec Azure Active Directory.
- Une **collection hybride** est hébergée dans le cloud Azure et y stocke également les données, mais elle permet aussi aux utilisateurs d'accéder aux données et aux ressources stockées sur votre réseau local. Les utilisateurs peuvent accéder aux applications en se connectant avec leurs informations d'identification d'entreprise, synchronisées ou fédérées avec Azure Active Directory.

### Collection cloud

La [collection cloud RemoteApp](remoteapp-create-cloud-deployment.md) est un moyen autonome d'héberger des applications dans le cloud. Une collection cloud existe uniquement dans le cloud Azure et ne se connecte pas à votre réseau local.

Dans le cadre de la version d'évaluation de RemoteApp, nous vous fournissons les applications Office 365 ProPlus ou Office 2013 préinstallées et prêtes à être partagées avec vos utilisateurs. Si vous choisissez d'utiliser les logiciels disponibles, vous pouvez rapidement configurer votre service.

Un autre avantage de l'utilisation de la collection cloud avec les applications Office est que les applications et le système d'exploitation (sur lequel votre service est généré) sont toujours mis à jour via des mises à jour régulières, et la protection de point de terminaison anti-logiciel malveillant de Microsoft offre une protection continue. Vos utilisateurs finaux utilisent leur compte Microsoft ou les informations d'identification d'entreprise pour accéder aux applications. Tout ce dont vous, en tant qu'administrateur, devez vous soucier est de déterminer qui doit avoir accès à quelles applications.

Vous pouvez également créer une collection cloud pour partager une application personnalisée ou un ensemble d'applications avec vos utilisateurs. Pour ce faire, vous devez [créer une image personnalisée](remoteapp-imageoptions.md) (ce qui permet de publier des applications dans RemoteApp) et choisir simplement cette image (au lieu de l'image Office 2013) quand vous créez votre collection.

#### Quand choisir la collection cloud

Choisissez une collection cloud quand l'application que vous souhaitez partager ne nécessite pas de connexion à des ressources sur le réseau privé de votre société (par exemple, via un périphérique VPN). Si l'application utilise des ressources sur Internet, OneDrive ou Azure, une collection cloud vous conviendra. C’est également l’option la plus rapide à mettre en place.


### Collection hybride
La [collection hybride RemoteApp](remoteapp-create-hybrid-deployment.md) vous permet de fournir à la fois un ensemble personnalisé d'applications à vos utilisateurs et un accès aux données et aux ressources de votre réseau local. Contrairement à une image personnalisée utilisée avec la collection cloud, l'image que vous créez pour une collection hybride exécute des applications dans un environnement joint à un domaine, ce qui permet d'accorder un accès complet à votre réseau local et vos données.

Grâce à l'intégration d'Active Directory à Azure Active Directory (avec DirSync), les utilisateurs peuvent se servir de leurs informations d'identification d'entreprise pour accéder aux applications et aux données. Lorsque vous utilisez un compte professionnel dans Active Directory, vous pouvez appliquer vos stratégies d'entreprise dans le cloud pour contrôler les applications que vous proposez via RemoteApp.

Tant que vous générez votre image de modèle sur Windows Server 2012 R2 avec le service de rôle Hôte de session Bureau à distance, il existe quelques limites pour les applications que vous pouvez publier pour vos utilisateurs. Si les applications fonctionnent correctement dans cet environnement d'image de modèle, vos utilisateurs finaux peuvent y accéder via RemoteApp.

#### Quand choisir la collection hybride

Choisissez une collection hybride si vous avez besoin d'une connexion à des ressources sur le réseau privé de votre société. Par exemple, si l'application nécessite un accès à l’une des ressources suivantes :

- Serveurs de fichiers situés sur votre intranet
- Quicken
- Bases de données derrière un pare-feu

Cette option s'avère généralement plus utile aux grandes entreprises qui possèdent beaucoup de ressources sur leurs réseaux privés qu'elles ne peuvent pas déplacer vers le cloud.

### Mise à jour de votre collection
L'une des principales différences entre les collections hybride et cloud est la façon dont sont gérées les mises à jour. Avec une collection cloud qui utilise l'image préinstallée d'Office 365 ProPlus ou d'Office 2013, vous n'avez pas à vous soucier des mises à jour. Le service gère lui-même les mises à jour et les déploie en continu, aussi bien pour les applications que pour le système d'exploitation.

Pour les collections hybrides, ainsi que pour les collections cloud qui utilisent une image de modèle personnalisée, vous êtes responsable de la maintenance de l'image et des applications. Pour les images jointes à un domaine, vous pouvez contrôler les mises à jour à l'aide de certains outils comme Windows Update, les stratégies de groupe ou encore System Center.

Après la mise à jour de votre image de modèle personnalisée, téléchargez la nouvelle image dans le cloud Azure, puis mettez à jour la collection pour qu'elle utilise la nouvelle image. (Vous pouvez le faire dans la page de **démarrage rapide** de RemoteApp ou dans le tableau de bord.)

Consultez la section [Mise à jour de votre collection](remoteapp-update.md) pour plus d'informations.

## Clients RemoteApp pris en charge
Azure RemoteApp est pris en charge par les applications clientes RemoteApp pour Windows et Windows RT, ainsi que par les applications Bureau à distance Microsoft pour Mac, iOS et Android. Vos utilisateurs peuvent utiliser ces applications sur leurs appareils mobiles ou de traitement pour accéder aux nouveau programmes RemoteApp.

Consultez la section [Accès à vos applications dans Azure RemoteApp](remoteapp-clients.md) pour plus d'informations sur les clients.

## Étapes suivantes
OK Faites un essai. Ces articles vous aident à prendre en main RemoteApp :

- [Création d’une image RemoteApp](remoteapp-imageoptions.md)
- [Création d'une collection cloud de RemoteApp](remoteapp-create-cloud-deployment.md)
- [Création d'une collection hybride de RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Fonctionnement des licences dans RemoteApp](remoteapp-licensing.md)
- [Meilleures pratiques pour l'utilisation d'Azure RemoteApp](remoteapp-bestpractices.md)
- [FAQ Azure RemoteApp](remoteapp-faq.md)
 

<!---HONumber=August15_HO7-->