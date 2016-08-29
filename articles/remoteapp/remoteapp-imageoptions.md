<properties
    pageTitle="Créer une image Azure RemoteApp | Microsoft Azure"
    description="En savoir plus sur les options disponibles pour la création d’images pour Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# Création d’une image Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

Azure RemoteApp utilise des images pour conserver les applications que vous partagez avec vos utilisateurs. (Nous prenons votre image et nous l’utilisons pour créer des machines virtuelles auxquelles les utilisateurs accéderont lorsqu’ils se connecteront à Azure RemoteApp.) Pour créer une collection Azure RemoteApp avec un choix d’applications, de type cloud ou hybride, commencez par créer une image à partir des applications installées. Créez ensuite une collection utilisant cette image, affectez des utilisateurs à la collection et publiez des applications pour ces utilisateurs.

Plusieurs choix s’offrent à vous pour créer ou utiliser des images. L’exécution de Windows Server 2012 R2 et l’installation du rôle Hôte de session Bureau à distance (RDSH) constituent les [exigences](remoteapp-imagereqs.md) de base pour le fonctionnement d’une image. La procédure commence à devenir intéressante au moment de choisir le mode de configuration.

Dans le cas des images, vous disposez des options suivantes :

- Vous pouvez importer et utiliser une [image basée sur une machine virtuelle Azure](remoteapp-image-on-azurevm.md). Cette fonction est utile pour les applications cœur de métier nécessitant des paramètres personnalisés. Vous pouvez personnaliser l’image à utiliser pour l’application.
- Vous pouvez [créer et charger une image personnalisée](remoteapp-create-custom-image.md). Cette fonction est utile si vous disposez déjà d’une image que vous utilisez pour le déploiement local de vos services Bureau à distance.
- Vous pouvez utiliser l’une des [images de modèle](remoteapp-images.md) incluses dans votre abonnement RemoteApp. Ces images sont créées et gérées par l’équipe RemoteApp et contiennent certaines applications standard (telles que la suite Office) que vous pouvez mettre à la disposition de vos utilisateurs. Notez que seule l’image d’Office 365 Pro Plus peut être utilisée dans un environnement de production.

Quel que soit le mode de création ou la provenance de votre image, vérifiez que vous comprenez les [exigences de l’application](remoteapp-appreqs.md) afin de vous assurer que celle-ci fonctionne dans RemoteApp. L’étape suivante consiste à créer une collection [cloud](remoteapp-create-cloud-deployment.md) ou [hybride](remoteapp-create-hybrid-deployment.md).

<!---HONumber=AcomDC_0817_2016-->