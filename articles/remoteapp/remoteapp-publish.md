---
title: Publier une application dans Azure RemoteApp | Microsoft Docs
description: "Découvrez comment publier des applications et des ressources dans Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c7e1a2cd-8e1f-4a33-bd43-8032ec9ac952
ms.service: remoteapp
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0b81ef8425e44a9813fc0220f0ab314a3110f265


---
# <a name="how-to-publish-an-app-in-remoteapp"></a>Publication d'une application dans RemoteApp
> [!IMPORTANT]
> Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Après avoir créé votre collection RemoteApp, vous devez publier les applications ou les ressources que vous souhaitez rendre disponibles pour vos utilisateurs. Les images de modèle fournies avec votre abonnement ne contiennent que certaines applications publiées par défaut. Pour partager d’autres applications, vous devez les publier.

> [!NOTE]
> Vous devez mettre à jour une application ? Il vous faut d’abord [mettre à jour l’image](remoteapp-update.md) .
> 
> 

Sous l'onglet **Publication ** du portail, cliquez sur **Publier**. Vous pouvez ajouter une application à partir du menu **Démarrer** de votre image de modèle ou indiquer le chemin d'accès de son répertoire d'installation dans l'image de modèle. Si vous choisissez d’ajouter une application à partir du menu **Démarrer** , sélectionnez l’application à publier dans la liste. Si vous choisissez d'indiquer le chemin d'accès à l'application, entrez un nom pour l'application et son chemin d'accès. Utilisez des variables dans le chemin d'accès, par exemple, « %lecteur_système% » au lieu de "c: \".

> [!NOTE]
> Si vous voulez ajouter votre application à partir du menu **Démarrer**, vous devez avoir *ajouté cette application au menu **Démarrer** sur votre image de modèle.* Sinon, RemoteApp ne voit que ce qui *figure* dans le menu **Démarrer** et cela peut prêter à confusion. 
> 
> Pour vous assurer que votre application figure dans le menu **Démarrer**, placez un fichier de raccourci **.lnk** dans le dossier %systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs.
> 
> Si vous avez oublié d'ajouter l'application au menu **Démarrer** au moment de la création du modèle, choisissez d'ajouter le chemin d'accès à l'application. (Ou recréez votre image de modèle, mais c'est un peu plus de travail.)
> 
> 




<!--HONumber=Nov16_HO3-->


