<properties 
	pageTitle="Qu’est-ce qu’Azure RemoteApp ? | Microsoft Azure" 
	description="Découvrez comment partager des applications et des ressources avec des appareils via Azure RemoteApp." 
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
	ms.date="08/15/2016" 
	ms.author="elizapo"/>

# Présentation d'Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

Azure RemoteApp offre les fonctionnalités du programme Microsoft RemoteApp local dans Azure, grâce aux services Bureau à distance. Azure RemoteApp vous permet de proposer un accès à distance sécurisé aux applications sur de nombreux appareils utilisateur. Azure RemoteApp héberge essentiellement des sessions Terminal Server non persistantes dans le cloud pour vous permettre de les utiliser et de les partager avec vos utilisateurs.

Azure RemoteApp vous permet de partager des applications et des ressources avec des utilisateurs sur presque n’importe quel appareil. Nous hébergeons vos applications dans le cloud, ce qui signifie que nous nous occupons du matériel et d'évolutivité pour répondre aux demandes des utilisateurs. Il ne vous reste qu’à télécharger les applications que vous souhaitez partager, puis à inviter vos utilisateurs à s’en servir. [Les utilisateurs conservent leurs propres appareils](remoteapp-clients.md), tandis que vous gérez toute l’infrastructure via le portail Azure. Vous pouvez même utiliser vos informations d'identification d'entreprise et ainsi garantir la sécurité des applications et des données.

Lisez la suite pour plus d'informations sur Azure RemoteApp, ou, si vous êtes déjà convaincu, [essayez-le maintenant](https://azure.microsoft.com/services/remoteapp/).

Vous avez des questions sur Azure RemoteApp ? Consultez notre [FAQ](remoteapp-faq.md).

Azure RemoteApp fait partie de [Microsoft Virtual Desktop Infrastructure (VDI)](http://www.microsoft.com/server-cloud/products/virtual-desktop-infrastructure/explore.aspx).

**Nouveau !** Vous voulez en savoir plus sur Azure RemoteApp ? Vous êtes prêt à appliquer Azure RemoteApp à grande échelle ? Participez à notre [webinaire hebdomadaire avec des experts](https://azureinfo.microsoft.com/AzureRemoteAppAskTheExperts-Registration-Page.html?ls=Website).

## Collections Azure RemoteApp
Il existe deux types de [collection Azure RemoteApp](remoteapp-collections.md) :


- Une **collection cloud** est hébergée dans le cloud Azure et stocke toutes les données des programmes qui y figurent. Les utilisateurs peuvent accéder aux applications en se connectant avec leur compte Microsoft ou leurs informations d'identification d'entreprise, synchronisées ou fédérées avec Azure Active Directory.

	Choisissez une collection cloud quand l'application que vous souhaitez partager ne nécessite pas de connexion à des ressources sur le réseau privé de votre société (par exemple, via un périphérique VPN). Si l'application utilise des ressources sur Internet, OneDrive ou Azure, une collection cloud vous conviendra. C’est également l’option la plus rapide à mettre en place.

- Une **collection hybride** est hébergée dans le cloud Azure et y stocke également les données, mais elle permet aussi aux utilisateurs d'accéder aux données et aux ressources stockées sur votre réseau local. Les utilisateurs peuvent accéder aux applications en se connectant avec leurs informations d'identification d'entreprise, synchronisées ou fédérées avec Azure Active Directory.

	Choisissez une collection hybride si vous avez besoin d'une connexion à des ressources sur le réseau privé de votre société. Par exemple, si l'application nécessite un accès à l’une des ressources suivantes :

	- Serveurs de fichiers situés sur votre intranet
	- Quicken
	- Bases de données derrière un pare-feu

	Cette option s'avère généralement plus utile aux grandes entreprises qui possèdent beaucoup de ressources sur leurs réseaux privés qu'elles ne peuvent pas déplacer vers le cloud.

Les différentes collections proposent diverses options, y compris en termes de réseaux. Vous devez donc de déterminer [la collection](remoteapp-collections.md) qui vous convient le mieux.


### Mise à jour de votre collection
L'une des principales différences entre les collections hybride et cloud est la façon dont sont gérées les mises à jour. Avec une collection cloud qui utilise l'image préinstallée d'Office 365 ProPlus ou d'Office 2013, vous n'avez pas à vous soucier des mises à jour. Le service gère lui-même les mises à jour et les déploie en continu, aussi bien pour les applications que pour le système d'exploitation.

Pour les collections hybrides, ainsi que pour les collections cloud qui utilisent une image de modèle personnalisée, vous êtes responsable de la maintenance de l'image et des applications. Pour les images jointes à un domaine, vous pouvez contrôler les mises à jour à l'aide de certains outils comme Windows Update, les stratégies de groupe ou encore System Center.

Après la mise à jour de votre image de modèle personnalisée, téléchargez la nouvelle image dans le cloud Azure, puis mettez à jour la collection pour qu'elle utilise la nouvelle image. (Vous pouvez le faire dans la page de **démarrage rapide** d’Azure RemoteApp ou dans le tableau de bord.)

Consultez la section [Mise à jour de votre collection](remoteapp-update.md) pour plus d'informations.

## Clients RemoteApp pris en charge
Azure RemoteApp est pris en charge par les applications clientes RemoteApp pour Windows et Windows RT, ainsi que par les applications Bureau à distance Microsoft pour Mac, iOS et Android. Vos utilisateurs peuvent se servir de ces applications sur leurs appareils mobiles ou de traitement pour accéder aux nouveaux programmes Azure RemoteApp.

Consultez la section [Accès à vos applications dans Azure RemoteApp](remoteapp-clients.md) pour plus d'informations sur les clients.

## Étapes suivantes
OK Faites un essai. Ces articles vous aident à prendre en main Azure RemoteApp :

- [Quel type de collection avez-vous besoin pour Azure RemoteApp ?](remoteapp-collections.md)
- [Création d’une image Azure RemoteApp](remoteapp-imageoptions.md)
- [Création d'une collection cloud d’Azure RemoteApp](remoteapp-create-cloud-deployment.md)
- [Création d'une collection hybride pour Azure RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Fonctionnement des licences dans Azure RemoteApp](remoteapp-licensing.md)
- [Meilleures pratiques pour l'utilisation d'Azure RemoteApp](remoteapp-bestpractices.md)
- [FAQ Azure RemoteApp](remoteapp-faq.md)
 

### Vos commentaires nous aideront à mieux vous servir 
Saviez-vous qu’en plus de noter cet article et de rédiger des commentaires ci-dessous, vous pouviez modifier l’article lui-même ? Il manque des informations ? Des informations sont erronées ? Certains passages ne sont pas clairs ? Faites défiler l’écran vers le haut et cliquez sur **Modifier sur GitHub** ou sur **Modifier** pour apporter des modifications. Nous les passerons en revue, puis, une fois celles-ci confirmées, vos modifications et améliorations s’afficheront ici.

<!---HONumber=AcomDC_0817_2016-->