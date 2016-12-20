---
title: Authentification Windows et serveur Azure Multi-Factor Authentication
description: "Il s&quot;agit de la page d&quot;authentification multifacteur Azure qui facilite le déploiement de l’authentification Windows et du serveur Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 19a4043f-c4ce-43c0-80e7-2548ee92cb74
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 84b6df0b45548d3797528c3723f02bd1a124454f


---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Authentification Windows et serveur Azure Multi-Factor Authentication
La section Authentification Windows permet à l'administrateur d’activer et de configurer l'authentification Windows pour une ou plusieurs applications.  Voici une liste des éléments à prendre en compte avant de configurer l'authentification Windows.

* Un redémarrage est nécessaire afin d’activer l'authentification multifacteur Azure pour Services Terminal.
* Si la case de correspondance d’utilisateur Authentification multifacteur Azure requise est cochée et que vous ne figurez pas dans la liste des utilisateurs, vous ne pourrez pas vous à l'ordinateur après le redémarrage.
* Les adresses IP de confiance varient selon que l'application est en mesure de fournir l'IP du client avec l'authentification. Actuellement, seul Terminal Services est pris en charge.  

> [!NOTE]
> Cette fonctionnalité n'est pas prise en charge pour sécuriser Terminal Services sur Windows Server 2012 R2.
> 
> 

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Pour sécuriser une application avec l'authentification Windows, utilisez la procédure suivante.
1. Sur le serveur Azure Multi-Factor Authentication, cliquez sur l’icône Authentication Windows.
   ![Authentification Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Cochez la case Activer l'authentification Windows. Par défaut, cette case est désactivée.
3. L'onglet Applications permet à l'administrateur de configurer une ou plusieurs applications pour l'authentification Windows.
4. Sélectionner un serveur ou une application – permet de spécifier si le serveur ou l'application est activé. Cliquez sur OK.
5. Cliquez sur bouton Ajouter… .
6. L'onglet Adresses IP de confiance vous permet d'ignorer Azure Multi-Factor Authentication pour les sessions Windows provenant d'adresses IP spécifiques. Par exemple, si les employés utilisent l'application au bureau et à domicile, vous pouvez décider de que vous ne souhaitez pas que leurs téléphones sonnent pour Azure Multi-Factor Authentication au bureau. Pour ce faire, vous devez définir le sous-réseau du bureau comme adresse IP de confiance.
7. Cliquez sur bouton Ajouter… .
8. Sélectionnez Adresse IP unique si vous souhaitez ignorer une adresse IP unique.
9. Sélectionnez Plage d’adresses IP si vous souhaitez ignorer toute une plage d’adresses IP. Exemple 10.63.193.1-10.63.193.100.
10. Sélectionnez Sous-réseau si vous souhaitez spécifier une plage d'adresses IP à l'aide de la notation de sous-réseau. Entrez l'adresse IP de début du sous-réseau et choisissez le masque de réseau approprié dans la liste déroulante.
11. Cliquez sur le bouton OK.




<!--HONumber=Dec16_HO2-->


