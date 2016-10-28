<properties
   pageTitle="Mise à jour de votre collection Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment mettre à jour votre collection Azure RemoteApp"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# Mise à jour d’une collection dans Azure RemoteApp

> [AZURE.IMPORTANT]
Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).

À un moment ou un autre, vous aurez besoin de mettre à jour les applications ou l'image dans votre collection Azure RemoteApp. Si vous utilisez l’une des images incluses dans votre abonnement Azure RemoteApp, dans une collection cloud ou hybride, toutes les mises à jour sont gérées par Azure RemoteApp : vous avez ainsi l’esprit tranquille.

Cependant, si vous utilisez une image personnalisée (créée de toutes pièces ou à partir d’une de vos images), vous êtes responsable de la maintenance de l’image et des applications. Si vous avez besoin mettre à jour votre image ou l’une des applications qu’elle contient, vous devez créer une nouvelle version mise à jour de l’image, puis remplacer l’image existante dans votre collection par cette nouvelle image mise à jour.

Comment mettre à jour votre collection ? C’est simple :

1. Mettez à jour l'image que vous avez utilisée dans votre collection. Appliquez tous les correctifs et toutes les mises à jour nécessaires, puis enregistrez-la sous un nouveau nom.
2. [Téléchargez](remoteapp-uploadimage.md) ou [importez](remoteapp-image-on-azurevm.md) cette image dans RemoteApp.
3. Sur la page de la collection, cliquez sur **Mettre à jour**.
4. Sélectionnez la nouvelle image à partir de la liste d’**Images de modèle**.
4. Voici la partie la plus difficile : vous devez décider comment traiter les utilisateurs qui utilisent actuellement une application dans la collection. Vous pouvez choisir parmi les options suivantes :
	- **Donner 60 minutes aux utilisateurs après la mise à jour**. Dès que la mise à jour est terminée, Azure RemoteApp affiche un message à tous les utilisateurs actifs, les invitant à enregistrer leur travail, à se déconnecter et à se reconnecter. Une fois les 60 minutes écoulées, tous les utilisateurs actifs qui ne se sont pas déconnectés seront automatiquement déconnectés. Les utilisateurs peuvent se reconnecter immédiatement.
	- **Déconnecter immédiatement les utilisateurs**. Dès que la mise à jour est terminée, déconnectez automatiquement tous les utilisateurs, sans avertissement. Si vous choisissez cette option, les utilisateurs risquent de perdre des données. Cependant, ils peuvent se reconnecter immédiatement à l'application.

1. Cochez la case pour lancer la mise à jour.

<!---HONumber=AcomDC_0817_2016-->