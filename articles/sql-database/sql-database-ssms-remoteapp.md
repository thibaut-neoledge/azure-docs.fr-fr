---
title: "Se connecter à la base de données SQL avec SQL Server Management Studio dans Azure RemoteApp | Microsoft Docs"
description: "Utilisez ce didacticiel pour apprendre à utiliser SQL Server Management Studio dans Azure RemoteApp pour la sécurité et les performances lors de la connexion à la base de données SQL"
services: sql-database
documentationcenter: 
author: adhurwit
manager: jhubbard
ms.assetid: 1052c83c-e7f5-4736-922f-216194d8874b
ms.service: sql-database
ms.custom: overview
ms.workload: data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: adhurwit
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9916eb7deaae6acb784cac1b7b64d93a03f3c590
ms.lasthandoff: 03/25/2017


---
# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Connexion à une base de données SQL à l’aide de SQL Server Management Studio dans Azure RemoteApp

> [!IMPORTANT]
> Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
>

## <a name="introduction"></a>Introduction
Ce didacticiel vous montre comment utiliser SQL Server Management Studio (SSMS) dans Azure RemoteApp pour vous connecter à une base de données SQL. Il vous guide tout au long de la configuration de SQL Server Management Studio dans Azure RemoteApp, vous en décrit les avantages, et indique les fonctionnalités de sécurité que vous pouvez utiliser dans Azure Active Directory.

**Durée estimée :** 45 minutes

## <a name="ssms-in-azure-remoteapp"></a>SSMS dans Azure RemoteApp
Azure RemoteApp est un service RDS d’Azure, qui fournit des applications. Vous pouvez obtenir plus d’informations sur ce service ici : [Qu’est-ce que RemoteApp ?](../remoteapp/remoteapp-whatis.md)

L’exécution de SSMS dans Azure RemoteApp et l’exécution locale de SSMS vous donnent la même expérience.

![Capture d’écran illustrant SSMS en cours d’exécution dans Azure RemoteApp][1]

## <a name="benefits"></a>Avantages
Les avantages liés à l’utilisation de SSMS dans Azure RemoteApp sont nombreux :

* Le port 1433 sur Azure SQL Server ne doit pas être exposé en externe (en dehors d’Azure).
* Il est inutile de continuer à ajouter et à supprimer des adresses IP dans le pare-feu d’Azure SQL Server.
* Toutes les connexions Azure RemoteApp ont lieu via HTTPS sur le port 443 à l’aide du protocole RDP (Remote Desktop Protocol) chiffré.
* SSMS est multi-utilisateur et évolutif.
* Posséder SSMS dans la même région que la base de données SQL permet d’obtenir des gains de performances.
* Vous pouvez auditer l’utilisation d’Azure RemoteApp avec l’édition Premium d’Azure Active Directory qui propose des rapports d’activité utilisateur.
* Vous pouvez activer l’authentification multifacteur (MFA).
* Vous pouvez accéder à SSMS où que vous soyez à l’aide de l’un des clients Azure RemoteApp pris en charge, notamment iOS, Android, Mac, Windows Phone et PC Windows.

## <a name="create-the-azure-remoteapp-collection"></a>Créer la collection Azure RemoteApp
Voici la procédure à suivre pour créer votre collection Azure RemoteApp avec SSMS :

### <a name="1-create-a-new-windows-vm-from-image"></a>1. Créer une machine virtuelle Windows à partir d’une image
Utilisez l’image « Windows Server 2012 R2 Hôte de session Bureau à distance Windows Server » de la galerie pour créer votre machine virtuelle.

### <a name="2-install-ssms-from-sql-express"></a>2. Installer SSMS à partir de SQL Express
Accédez à la nouvelle machine virtuelle et à cette page de téléchargement : [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

Une option permet de télécharger uniquement SSMS. À l’issue du téléchargement, accédez au répertoire d’installation et exécutez le programme d’installation de SSMS.

Vous devez également installer SQL Server 2014 Service Pack 1. Vous pouvez le télécharger ici : [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 inclut des fonctionnalités essentielles pour l’utilisation de la base de données SQL Azure.

### <a name="3-run-validate-script-and-sysprep"></a>3. Exécuter le script de validation et Sysprep
Un script PowerShell de validation figure sur le Bureau de la machine virtuelle. Exécutez-le en double-cliquant dessus. Il vérifie que la machine virtuelle est prête à être utilisée pour l’hébergement distant des applications. À l’issue de la vérification, il demande d’exécuter Sysprep. Choisissez de l’exécuter.

Lorsque l’exécution de Sysprep est terminée, il arrête la machine virtuelle.

Pour en savoir plus sur la création d'une image Azure RemoteApp, consultez la page [Création d'une image de modèle RemoteApp dans Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)

### <a name="4-capture-image"></a>4. Capturer l’image
Lorsque l’exécution de la machine virtuelle est arrêtée, recherchez-la dans le portail actuel et capturez-la.

Pour en savoir plus sur la capture d’une image, consultez [Capturer l’image d’une machine virtuelle Windows Azure créée avec le modèle de déploiement classique](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="5-add-to-azure-remoteapp-template-images"></a>5. Ajouter des images de modèle à Azure RemoteApp
Dans la section Azure RemoteApp du portail actuel, cliquez sur l’onglet Images de modèle, puis sur Ajouter. Dans la zone contextuelle, sélectionnez Importez une image depuis votre bibliothèque de machines virtuelles, puis choisissez l’image que vous venez de créer.

### <a name="6-create-cloud-collection"></a>6. Créer une collection cloud
Dans le portail actuel, créez une collection cloud Azure RemoteApp. Choisissez l’image de modèle que vous venez d’importer avec SSMS installé dessus.

![Créer une collection cloud][2]

### <a name="7-publish-ssms"></a>7. Publier SSMS
Dans l’onglet Publication de votre nouvelle collection cloud, sélectionnez Publier une application dans le menu Démarrer, puis choisissez SSMS dans la liste.

![Publier une application][5]

### <a name="8-add-users"></a>8. Ajouter des utilisateurs
Dans l’onglet Accès utilisateur, vous pouvez sélectionner les utilisateurs qui auront accès à cette collection Azure RemoteApp qui inclut uniquement SSMS.

![Ajouter un utilisateur][6]

### <a name="9-install-the-azure-remoteapp-client-application"></a>9. Installer l’application cliente Azure RemoteApp
Vous pouvez télécharger et installer un client Azure RemoteApp ici : [Télécharger | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)

## <a name="configure-azure-sql-server"></a>Configuration d’Azure SQL Server
La seule configuration nécessaire est de s’assurer qu’Azure Services est activé pour le pare-feu. Si vous utilisez cette solution, vous n’avez pas besoin d’ajouter des adresses IP pour ouvrir le pare-feu. Le trafic réseau autorisé vers le serveur SQL Server provient d’autres services Azure.

![Autoriser dans Azure][4]

## <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)
La MFA peut être activée spécifiquement pour cette application. Accédez à l’onglet Applications d’Azure Active Directory. Vous trouverez une entrée correspondant à Microsoft Azure RemoteApp. Si vous cliquez sur cette application et que vous la configurez, la page suivante s’affiche et vous permet d’activer la MFA pour cette application.

![Activer la MFA][3]

## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Auditer l’activité utilisateur avec Azure Active Directory Premium
Si vous ne possédez pas Azure AD Premium, vous devez alors l’activer dans la section Licences de votre annuaire. L’édition Premium étant activée, vous pouvez affecter des utilisateurs au niveau Premium.

Lorsque vous accédez à un utilisateur dans Azure Active Directory, vous pouvez cliquer sur l’onglet Activité pour afficher les informations de connexion pour Azure RemoteApp.

## <a name="next-steps"></a>Étapes suivantes
Après avoir effectué toutes les étapes ci-dessus, vous serez en mesure d’exécuter le client Azure RemoteApp et de vous connecter avec un utilisateur affecté. SSMS est présenté comme étant l’une de vos applications, et vous pouvez l’exécuter comme s’il était installé sur votre ordinateur avec un accès à Azure SQL Server.

Pour plus d'informations sur l'établissement d'une connexion avec la base de données SQL, consultez la page [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).

C’est tout pour le moment. Vous n’avez plus qu’à l’utiliser !

<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
