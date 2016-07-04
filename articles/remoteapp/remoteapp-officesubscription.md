
<properties 
    pageTitle="Utilisation de votre abonnement Office 365 avec Azure RemoteApp | Microsoft Azure"
	description="Découvrez comment vous pouvez utiliser votre abonnement Office 365 dans Azure RemoteApp pour partager des applications Office."
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/13/2016" 
    ms.author="elizapo" />



# Utilisation de votre abonnement Office 365 avec Azure RemoteApp

Saviez-vous que vous pouvez utiliser votre abonnement Office 365 existant dans Azure RemoteApp pour partager des applications Office à partir du cloud ? Continuez votre lecture pour en savoir plus sur vos options Office 365 + Azure RemoteApp, notamment pour consulter des liens vers des articles sur Office 365 qui vous aideront à tirer le meilleur parti de votre abonnement.

## Comment utiliser des comptes Office 365 pour Azure RemoteApp ?
Pour des informations détaillées, consultez le nouvel article de Peter : [comment utiliser Azure RemoteApp avec des comptes d’utilisateur Office 365](remoteapp-o365user.md)

## Puis-je utiliser mon abonnement Office 365 pour exécuter des applications Office dans Azure RemoteApp ?

Oui. En fait, l’utilisation de votre abonnement Office 365 est la seule façon d’importer vos applications Office dans Azure RemoteApp.

(Remarque : si votre déploiement Azure RemoteApp est fourni par un partenaire d’hébergement, ce dernier peut éventuellement vous fournir des licences Office dans le cadre d’un [contrat de licence de fournisseur de services](http://www.microsoft.com/fr-FR/Licensing/licensing-programs/spla-program.aspx))


L’avantage de votre abonnement Office 365 est qu’il vous permet d’utiliser la même licence utilisateur dans de nombreux environnements et plateformes, y compris dans le cloud Azure. Lorsque vous utilisez des applications Office dans Azure RemoteApp, vous n’avez pas besoin d’acheter des licences supplémentaires ou de configurer vos licences existantes d’une manière particulière. Il vous suffit de disposer d’un abonnement Office 365 qui inclut [Office 365 ProPlus](https://technet.microsoft.com/library/Gg702619.aspx).

Office 365 ProPlus permet [l’activation d’ordinateurs partagés](https://technet.microsoft.com/library/Dn782860.aspx) : cette fonctionnalité permet l’activation temporaire basée sur les utilisateurs pour Office dans les environnements virtuels et cloud comme Azure RemoteApp (et les Services Bureau à distance).

Quels plans Office 365 incluent Office 365 ProPlus ? Consultez le tableau [Disponibilité des services de chaque plan](https://technet.microsoft.com/library/office-365-plan-options.aspx). Notez que les plans n’incluent pas tous Office 365 ProPlus (par exemple, le plan Office 365 Business). Si votre plan n’inclut pas Office 365 ProPlus, envisagez la mise à niveau vers un plan l’incluant (par exemple, Office 365 Éducation E3).

## Comment mes licences Office 365 ProPlus sont-elles utilisées avec Azure RemoteApp ?

Chaque licence utilisateur pour Office 365 ProPlus permet à un seul utilisateur d’activer des applications Office sur 5 ordinateurs, tablettes et téléphones maximum. Chaque activation est enregistrée pour l’utilisateur jusqu’à ce qu’il désactive Office sur l’appareil. (Les utilisateurs peuvent gérer leurs appareils dans le [portail Office 365](https://portal.office365.com/).)

Avec Azure RemoteApp, un même utilisateur peut se connecter à plusieurs ordinateurs le même jour sans s’en rendre compte. En effet, le service gère et met à l’échelle automatiquement les ressources du cloud, tandis que l’utilisateur ne voit que les applications et les programmes que vous avez partagés. Pour ce scénario, Office 365 ProPlus offre un mode d’activation d’ordinateur partagé : cela signifie que l’utilisateur n’a pas besoin d’effectuer de gestion de licences pour accéder à ces ressources et que les applications peuvent être activées sur un nombre d’ordinateurs individuels supérieur à la limite de 5 ordinateurs.

Tant que vous (l’administrateur) affectez des licences Office 365 ProPlus à vos utilisateurs, ceux-ci peuvent utiliser Office sur leurs appareils personnels, ainsi que par le biais de votre collection Azure RemoteApp.

## Quelles applications Office puis-je utiliser avec Office 365 et Azure RemoteApp ?

Vous pouvez utiliser votre abonnement Office 365 ProPlus pour partager Office 2013 et Office 2016 (après sa publication). Azure RemoteApp ne prend pas en charge les versions antérieures d’Office.

## Qu’en est-il de Visio Pro ou de Project Pro ?

L’image d’Office 365 ProPlus incluse dans votre abonnement RemoteApp comprend Visio Pro et Project Pro. Toutefois, vous ne pouvez pas utiliser votre abonnement Office 365 ProPlus pour activer ces programmes, ils ont chacun leur propre licence. Vous pouvez les activer dans le [portail Office 365](https://portal.office365.com/).

Vous n’êtes pas obligé d’acquérir des licences pour ces programmes si vous ne souhaitez pas les utiliser. Activez les programmes que vous souhaitez utiliser et ignorez les autres. Vous les verrez toujours dans l’image mais vous ne pourrez pas les utiliser.

## Comment prendre en main Office 365 et Azure RemoteApp ?

Maintenant que vous connaissez les informations relatives aux licences Office 365, nous allons vous aider à commencer à utiliser ces licences dans Azure RemoteApp. C’est très simple :

Quand vous créez votre collection Azure RemoteApp, utilisez l’image **Office 365 ProPlus (abonnement requis)**.

![Image Azure RemoteApp avec Office 365 ProPlus](./media/remoteapp-officesubscription/remoteapp-officeimage.png)


Cette image contient la dernière version de Windows Server et d’Office 365 ProPlus. Après avoir configuré votre collection (y compris les applications de publication), vos utilisateurs se connectent simplement à Azure RemoteApp (à l’aide de leur client RemoteApp) et fournissent leurs informations d’identification Office 365 pour les applications Office. Les licences sont automatiquement remises et ne nécessitent aucune configuration ou gestion.

## Puis-je créer une image personnalisée avec Office 365 ProPlus ?

Vous pouvez créer une image personnalisée pour votre collection qui contient Office 365 ProPlus. 2 choix s’offrent à vous : utiliser l’image de galerie Azure que nous fournissons ou créer votre propre image personnalisée et installer Office 365 ProPlus à cet endroit.

### Utilisation de l’image de galerie Azure

Pour déployer Office 365 ProPlus sur une collection, le plus simple est de [démarrer avec l’une des images de galerie Azure](remoteapp-image-on-azurevm.md) incluse dans votre abonnement Azure RemoteApp. Veillez à choisir l’image **Hôte de session Bureau à distance Windows Server avec Office 365 ProPlus préinstallé**. Ensuite, installez les autres applications souhaitées sur cette image, et vous êtes fin prêt.

### Utilisation d’une image personnalisée

Vous pouvez toujours créer une image personnalisée. Vous pouvez créer une [machine virtuelle Azure](remoteapp-image-on-azurevm.md) ou [créer l’image localement](remoteapp-create-custom-image.md) et la charger sur Azure. Dans les deux cas, veillez à installer Office 365 ProPlus à l’aide du nœud d’activation d’ordinateur partagé. Utilisez l’[outil Déploiement d’Office](http://blogs.technet.com/b/odsupport/archive/2014/07/11/using-the-office-deployment-tool.aspx) et suivez les [instructions](https://technet.microsoft.com/library/Dn782858.aspx) d’installation.

### Désactivation des mises à jour automatiques pour Office 365 ProPlus dans votre image personnalisée - IMPORTANT

Votre image personnalisée est utilisée par Azure RemoteApp comme modèle pour l’ajout de ressources supplémentaires au fur et à mesure que la demande de vos utilisateurs augmente. Pour éviter les retards et les problèmes de connexion, désactivez les mises à jour automatiques pour Office dans l’image. Si vous ne le faites pas, chaque ressource créée avec ce modèle sera automatiquement mise à jour lors de son démarrage. Utilisez plutôt le processus standard Azure RemoteApp pour mettre à jour votre image personnalisée. De cette façon, vous mettez à jour les applications Office une fois sur l’image du modèle et laissez Azure RemoteApp prendre soin d’envoyer les mises à jour à vos utilisateurs.

Pour désactiver les mises à jour automatiques, ajoutez le code suivant au fichier de configuration de l’outil Déploiement d’Office :

		<Updates Enabled="FALSE" />

À présent, votre fichier de configuration doit contenir les lignes suivantes :
	
		<Display Level="NONE" AcceptEULA="TRUE" />
		<Property Name="SharedComputerLicensing" Value="1" />
		<Updates Enabled="FALSE" />

## Comment puis-je mettre à jour une image avec Office 365 ProPlus ?

Il existe de nombreuses raisons pour mettre à jour l’image dans votre collection. En voici quelques-unes :

- Obtenir les dernières mises à jour Windows 
- Obtenir les dernières mises à jour des applications Office 365 ProPlus
- Mettre à jour votre application personnalisée
- Modifier d’autres paramètres de configuration pour l’image elle-même

Pour consulter la procédure de bout en bout de mise à jour de votre collection pour utiliser l’image mise à jour, cliquez [ici](remoteapp-update.md). Toutefois, pour savoir comment mettre à jour l’image et Office 365 ProPlus, consultez les informations suivantes.

Deux options s’offrent à vous pour la mise à jour de votre image : remplacer votre image par une nouvelle image ou mettre à jour manuellement votre image existante.

### Remplacement de votre image avec la dernière image de galerie Azure et ajout de personnalisations
Avec cette option, vous laissez Microsoft s’occuper des mises à jour de Windows Server et d’Office 365 ProPlus. Au lieu de mettre à jour votre image existante, vous allez créer une nouvelle image basée sur la dernière image de galerie. Ensuite, répétez toutes les étapes effectuées auparavant pour personnaliser l’image : installation des applications personnalisées, modification de la configuration de l’image, etc.

Les images de galerie sont régulièrement mises à jour. Vous avez donc l’esprit tranquille puisque vous avez l’assurance que vos applications Windows Server et Office 365 ProPlus sont à jour. Bien entendu, en contrepartie, vous devez appliquer vos personnalisations à chaque fois que vous obtenez une nouvelle image. Vous pouvez créer des scripts pour automatiser la configuration de vos personnalisations.

### Mise à jour manuelle de votre image existante

Avec cette option, vous utilisez les outils Windows standard pour appliquer des mises à jour à l’image. Pour Office 365 ProPlus, utilisez l’outil Déploiement d’Office pour télécharger et installer les dernières mises à jour ou versions d’Office 365 ProPlus.

> [AZURE.IMPORTANT] N’oubliez pas : désactivez les mises à jour automatiques d’Office 365 ProPlus.

Vous avez besoin de plus d’informations sur l’utilisation de l’outil Déploiement d’Office pour les mises à jour ?

- [Déployer des produits Office « Démarrer en un clic » pour Office 365 à l’aide de l’outil Déploiement d’Office](https://technet.microsoft.com/library/JJ219423.aspx)
- [Déploiement et mise à jour d’Office 365 ProPlus à l’aide de l’outil Déploiement d’Office](https://channel9.msdn.com/Events/Ignite/2015/BRK3168) (vidéo)
- [Définir les paramètres de mise à jour pour Office 365 ProPlus](https://technet.microsoft.com/library/dn761708.aspx)

<!---HONumber=AcomDC_0622_2016-->