---
title: "Migration et publication d’une application web sur un service cloud Azure à partir de Visual Studio | Microsoft Docs"
description: "Découvrez comment migrer et publier une application web sur un service cloud Azure à partir de Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 01623fa76175091439d5a571fb8b8f96aee01c4c
ms.openlocfilehash: a30859610aed55c1ffa22e821de3702d265a7fa7


---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Migration et publication d’une application web sur un service cloud Azure à partir de Visual Studio
Pour tirer parti des services d’hébergement et de l’extensibilité d’Azure, vous pouvez migrer et publier votre application web sur un service cloud Azure. Vous pouvez exécuter une application web dans Azure avec un minimum de modifications sur votre application existante.

> [!NOTE]
> Cette rubrique concerne le déploiement sur des services cloud, pas sur des sites web. Pour plus d’informations sur le déploiement sur des sites web, consultez [Déployer une application web dans Azure App Service](app-service-web/web-sites-deploy.md).
>
>

Pour obtenir la liste des modèles spécifiques pris en charge par Visual C# et Visual Basic, consultez la section **Modèles de projet pris en charge** plus loin dans cette rubrique.

Vous devez d'abord activer votre application web pour Azure à partir de Visual Studio. L'illustration suivante montre les étapes clés pour publier votre application web existante en ajoutant un projet Azure à utiliser pour le déploiement. Ce processus ajoute à votre solution un projet Azure avec le rôle web nécessaire. Selon le type de votre projet web, les propriétés du projet pour les assemblys sont également mises à jour si le package de services requiert des assemblys supplémentaires pour le déploiement.

![Publication d’une application web dans Microsoft Azure](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC748917.png)

> [!NOTE]
> La commande **Convertir**, **Convertir en projet service cloud Azure** s’affiche uniquement pour le projet web dans votre solution. Par exemple, la commande n'est pas disponible pour un projet Silverlight dans votre solution.
> Lorsque vous créez un package de services ou publiez votre application dans Azure, des avertissements ou des erreurs peuvent se produire. Ces avertissements et ces erreurs peuvent vous aider à résoudre les problèmes avant le déploiement vers Azure. Par exemple, vous pouvez recevoir un avertissement signalant un assembly manquant. Pour plus d’informations sur la façon de traiter les avertissements comme des erreurs, consultez [Configurer un projet de service cloud Azure avec Visual Studio](vs-azure-tools-configuring-an-azure-project.md). Si vous générez votre application, exécutez celle-ci localement à l’aide de l’émulateur de calcul ou publiez celle-ci sur Azure, vous pouvez voir l’erreur suivante dans la fenêtre **Liste d’erreurs** : **Le chemin d’accès spécifié, le nom de fichier ou les deux sont trop longs**. Cette erreur se produit car le nom qualifié complet du projet Azure est trop long. La longueur du nom du projet, y compris le chemin d’accès complet, ne peut pas dépasser 146 caractères. Voici, par exemple, le nom de projet complet, y compris le chemin d’accès au fichier d’un projet Azure créé pour une application Silverlight : `c:\users\<user name>\documents\visual studio 2015\Projects\SilverlightApplication4\SilverlightApplication4.Web.Azure.ccproj`. Afin de réduire la longueur du nom complet du projet, vous devrez peut-être déplacer votre solution vers un autre répertoire avec un chemin plus court.
>
>

Pour migrer et publier une application web vers Azure à partir de Visual Studio, suivez ces étapes :

## <a name="enable-a-web-application-for-deployment-to-azure"></a>Activation d’une application web pour un déploiement vers Azure
### <a name="to-enable-a-web-application-for-deployment-to-azure"></a>Activer une application web pour un déploiement vers Azure
1. Pour activer votre application web en vue d’un déploiement vers Azure, ouvrez le menu contextuel d’un projet web dans votre solution et choisissez la commande permettant d’ajouter un projet de déploiement Azure.

    Les actions suivantes se produisent :

   * Un projet Azure appelé `<name of the web project>.Azure` est ajouté à la solution pour votre application.
   * Un rôle web pour le projet web est ajouté à ce projet Azure.
   * La propriété **Copie locale** est définie sur vrai pour tous les assemblys qui sont nécessaires pour MVC 2, MVC 3, MVC 4 et Silverlight Business Applications. Ceci ajoute ces assemblys au package de services qui est utilisé pour le déploiement.

   > [!IMPORTANT]
   > Si vous avez d’autres assemblys ou fichiers qui sont requis pour cette application web, vous devez définir manuellement les propriétés de ces fichiers. Pour plus d’informations sur la configuration de ces propriétés, consultez la section **Inclure des fichiers dans le package de services** plus loin dans cet article.
   >
   > [!NOTE]
   > S’il existe déjà un rôle web pour un projet web spécifique dans un projet Azure de la solution, la commande **Convertir**, **Convertir en projet de service cloud Azure** n’est pas affichée dans le menu contextuel pour ce projet web.
   >
   >

   Si vous avez plusieurs projets web dans votre application web et que vous souhaitez créer des rôles web pour chacun, vous devez effectuer les étapes de cette procédure pour chaque projet web. Ceci crée des projets Azure distincts pour chaque rôle web. Chaque projet web peut être publié séparément. Alternativement, vous pouvez ajouter manuellement un autre rôle web à un projet Azure existant dans votre application web. Pour ce faire, ouvrez le menu contextuel du dossier **Rôles** dans votre projet Azure et choisissez **Ajouter**, **Projet de rôle web dans la solution**, puis choisissez le projet à ajouter en tant que rôle web et, enfin, cliquez sur le bouton **OK**.

## <a name="use-an-azure-sql-database-for-your-application"></a>Utilisation d’une base de données SQL Azure pour votre application
Si vous avez une chaîne de connexion pour votre application web qui utilise une base de données SQL Server sur site, vous devez modifier cette chaîne de connexion pour qu’elle utilise une instance de base de données SQL hébergée par Azure à la place.

> [!IMPORTANT]
> Votre abonnement vous permet d’utiliser la base de données SQL. Si vous accédez à votre abonnement à partir du [Portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), vous pouvez déterminer quels services vos abonnements fournissent. Les instructions suivantes s’appliquent au [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885). Si vous utilisez le [portail Azure](http://portal.microsoft.com), passez à la procédure suivante.|
>
>

### <a name="to-use-a-sql-database-instance-in-your-web-role-for-your-connection-string"></a>Utiliser une instance de base de données SQL dans votre rôle web pour votre chaîne de connexion
1. Pour créer une instance de base de données SQL dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), suivez les étapes décrites dans l’article suivant : [Créer un serveur de base de données SQL](http://go.microsoft.com/fwlink/?LinkId=225109).

   > [!NOTE]
   > Quand vous configurez les règles de pare-feu pour votre instance de base de données SQL, vous devez cocher la case **Autoriser l’accès d’autres services Azure à ce serveur** .
   >
   >
2. Pour créer une instance de base de données SQL à utiliser pour votre chaîne de connexion, suivez les étapes décrites dans la section suivante de cet article : [Créer une base de données SQL](http://go.microsoft.com/fwlink/?LinkId=225110).
3. Pour copier la chaîne de connexion ADO.NET à utiliser pour votre chaîne de connexion, effectuez les opérations suivantes dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).  

   1. Choisissez le bouton **Base de données** , puis ouvrez le nœud pour l’abonnement que vous avez utilisé afin de créer votre instance de base de données SQL.
   2. Pour afficher les instances disponibles de la base de données SQL, choisissez le nœud **Bases de données SQL** .
   3. Pour afficher les propriétés de la base de données, sélectionnez la base de données. La vue **Propriétés** apparaît.

      > [!NOTE]
      > Si la vue **Propriétés** ne s’affiche pas, vous devrez peut-être l’ouvrir en utilisant le diviseur.
      >
      >
   4. Pour afficher les chaînes de connexion, choisissez le bouton de sélection (...) en regard de l’option Affichage.

      La boîte de dialogue **Chaînes de connexion** s'affiche.
   5. Pour copier la chaîne de connexion ADO.NET, mettez le texte en surbrillance et appuyez sur les touches Ctrl+C.
   6. Pour fermer la boîte de dialogue, choisissez le bouton **Fermer** .
4. Pour remplacer la chaîne de connexion dans le fichier web.config et utiliser cette instance de la base de données SQL à la place, ouvrez le fichier web.config, mettez en surbrillance l’entrée de chaîne de connexion existante, puis appuyez sur les touches Ctrl+V. La chaîne de connexion ADO.NET pour l'instance de base de données SQL remplace la chaîne de connexion existante.
5. Vous devez également ajouter le paramètre `MultipleActiveResultSets=True` à la chaîne de connexion. La chaîne de connexion suit ce format :

    ```
    connectionString=”Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"
    ```
6. (Facultatif) Une autre méthode de modification de la chaîne de connexion directement dans le fichier web.config consiste à ajouter une section dans un des fichiers de transformation web.config, selon la configuration de build que vous utilisez pour créer votre package de services. Ouvrez le fichier Web.Debug.Config ou le fichier Web.Release.Config. Ajoutez la section suivante dans ce fichier :

    ```
    XMLCopy<connectionStrings><addname="DefaultConnection"connectionString="Server=tcp:<database_server>.database.windows.net,1433;Database=<database_name>;User ID=<user_name>@<database_server>;Password=<myPassword>;Trusted_Connection=False;Encrypt=True;MultipleActiveResultSets=True"xdt:Transform="SetAttributes"xdt:Locator="Match(name)"/></connectionStrings>
    ```
7. Enregistrez le fichier que vous avez modifié et republiez votre application.

### <a name="to-use-an-instance-of-sql-database-by-using-the-azure-classic-portal"></a>Pour utiliser une instance de base de données SQL à l’aide du Portail Azure Classic
1. Dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885), choisissez le nœud Base de données SQL.

   * Si l'instance de base de données SQL que vous souhaitez utiliser s'affiche, ouvrez-la.
   * Si vous n'avez créé aucune instance, cliquez sur le lien approprié, puis créez une instance.
2. Après avoir ouvert ou créé une instance de base de données, cliquez sur le lien **Chaînes de connexion** .
3. En bas de la page, cliquez sur le lien pour configurer les paramètres du pare-feu et acceptez les valeurs par défaut, ou configurez les valeurs dont vous avez besoin.
4. Copiez la chaîne de connexion ADO.NET, collez-la dans votre fichier web.config sur l’ancienne chaîne de connexion pour la base de données locale, puis veillez à ajouter `MultipleActiveResultSets=True`.

## <a name="publish-a-web-application-to-azure"></a>Publication d’une application web dans Azure
### <a name="to-publish-a-web-application-to-azure"></a>Publier une application web dans Azure
1. Pour tester l’application dans l’environnement de développement local à l’aide de l’émulateur de calcul Azure, ouvrez le menu contextuel du projet Azure pour le rôle web, puis choisissez **Définir comme projet de démarrage**. Sélectionnez ensuite **Déboguer**, **Démarrer le débogage** (clavier : **F5**).

    La boîte de dialogue de **démarrage de l’environnement de débogage Azure** s’ouvre et l’application démarre dans le navigateur. Pour des informations spécifiques sur le démarrage de chaque type d'application web dans l'émulateur de calcul, consultez le tableau dans cette section.
2. Pour configurer les services de sorte que votre application soit publiée sur Azure, vous devez disposer d’un compte Microsoft et d’un abonnement Azure. Utilisez les étapes de la rubrique suivante pour configurer vos services : [Préparer la publication ou le déploiement d’une application Azure à partir de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
3. Pour publier l’application web sur Azure, ouvrez le menu contextuel du projet web et choisissez **Publier sur Azure**.

    La boîte de dialogue **Publication d’application Azure** s’ouvre et Visual Studio démarre le processus de déploiement. Pour plus d’informations sur la publication de l’application, consultez la section **Publication d’une application Azure à partir de Visual Studio** dans [Publication d’un service cloud à l’aide de Microsoft Azure Tools](vs-azure-tools-publishing-a-cloud-service.md).

   > [!NOTE]
   > Vous pouvez également publier l'application web à partir du projet Azure. Pour cela, ouvrez le menu contextuel du projet Azure, puis sélectionnez **Publier**.
   >
   >
4. Pour afficher la progression du déploiement, vous pouvez consulter la fenêtre **Journal des activités Azure** . Ce journal s'affiche automatiquement au démarrage du processus de déploiement. Vous pouvez développer l'élément de la ligne dans le journal des activités pour afficher des informations détaillées, comme indiqué dans l'illustration suivante :

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)
5. (Facultatif) Pour annuler le processus de déploiement, ouvrez le menu contextuel de cette ligne dans le journal des activités, puis choisissez **Annuler et supprimer**. Cela arrête le processus de déploiement et supprime l'environnement de déploiement d'Azure.

   > [!NOTE]
   > Pour supprimer cet environnement de déploiement après son déploiement, vous devez utiliser le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).
   >
   >
6. (Facultatif) Une fois vos instances de rôle démarrées, Visual Studio affiche automatiquement l’environnement de déploiement dans le nœud **Calcul Azure** de **Cloud Explorer** ou de l’**Explorateur de serveurs**. À partir de là, vous pouvez consulter l'état de chaque instance de rôle.

    L’illustration suivante montre les instances de rôle dans l’ **Explorateur de serveurs** toujours à l’état d’initialisation :

    ![VST_DeployComputeNode](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744134.png)
7. Pour accéder à votre application après le déploiement, cliquez sur la flèche en regard de votre déploiement quand le statut **Terminé** s’affiche dans le **journal des activités Azure**. Cela affiche l'URL de votre application web dans Azure. Consultez le tableau suivant pour plus d'informations sur le démarrage d'un type spécifique de l'application web à partir d’Azure.

    Le tableau suivant contient les informations sur le démarrage des applications web spécifiques à partir d’Azure, ainsi que sur l’exécution ou le débogage d’une application web localement à l’aide de l’émulateur de calcul Azure :

   | Type d’application web | Exécution/débogage localement à l'aide de l'émulateur de calcul | Exécution dans Azure |
   | --- | --- | --- |
   | Application Web ASP.NET |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Choisissez le lien URL affiché sous l’onglet **Déploiement** du **Journal des activités Azure** pour charger la page de démarrage dans le navigateur. |
   | Application Web ASP.NET MVC 2 |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Choisissez le lien URL affiché sous l’onglet **Déploiement** du **Journal des activités Azure** pour charger la page de démarrage dans le navigateur. |
   | Application Web ASP.NET MVC 3 |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Choisissez le lien URL affiché sous l’onglet **Déploiement** du **Journal des activités Azure** pour charger la page de démarrage dans le navigateur. |
   | Application Web ASP.NET MVC 4 |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Choisissez le lien URL affiché sous l’onglet **Déploiement** du **Journal des activités Azure** pour charger la page de démarrage dans le navigateur. |
   | Application Web vide ASP.NET |Vous devez ajouter une page .aspx dans votre application et la définir comme page de démarrage pour votre projet web. Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Si votre application contient une page .aspx par défaut, choisissez le lien URL affiché sous l’onglet **Déploiement** du **Journal des activités Azure** ; cette page est chargée dans le navigateur. Si vous avez une page .aspx différente, vous devez naviguer jusqu’à cette page spécifique en utilisant le format suivant pour votre URL : `<url for deployment>/<name of page>.aspx` |
   | Application Silverlight |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Vous devez accéder à la page spécifique de votre application en utilisant le format suivant pour votre URL : `<url for deployment>/<name of page>.aspx` |
   | Silverlight Business Application |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Vous devez accéder à la page spécifique de votre application en utilisant le format suivant pour votre URL : `<url for deployment>/<name of page>.aspx` |
   | Silverlight Navigation Application |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Vous devez accéder à la page spécifique de votre application en utilisant le format suivant pour votre URL :`<url for deployment>/<name of page>.aspx` |
   | WCF Service Application |Vous devez définir le fichier .svc comme page de démarrage de votre projet de service WCF. Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Vous devez accéder au fichier .svc de votre application en utilisant le format suivant pour votre URL : `<url for deployment>/<name of service file>.svc` |
   | WCF Workflow Service Application |Vous devez définir le fichier .svc comme page de démarrage de votre projet de service WCF. Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Vous devez accéder au fichier .svc de votre application en utilisant le format suivant pour votre URL : `<url for deployment>/<name of service file>.svc` |
   | ASP.NET Dynamic Entities |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Vous devez mettre à jour la chaîne de connexion (voir la section suivante). Vous devez également accéder à la page spécifique de votre application en utilisant le format suivant pour votre URL : `<url for deployment>/<name of page>.aspx` |
   | ASP.NET Dynamic Data Linq to SQL |Dans la barre de menus, choisissez **Déboguer**, **Démarrer le débogage** (clavier : appuyez sur la touche **F5**). |Vous devez suivre les étapes de cette procédure : Utilisation d’une base de données SQL Azure pour votre application (voir la section plus haut dans cette rubrique). Vous devez également accéder à la page spécifique de votre application en utilisant le format suivant pour votre URL : `<url for deployment>/<name of page>.aspx` |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Mise à jour d’une chaîne de connexion pour ASP.NET Dynamic Entities
### <a name="to-update-a-connection-string-for-aspnet-dynamic-entities"></a>Mettre à jour une chaîne de connexion pour ASP.NET Dynamic Entities
1. Pour créer une base de données SQL Azure qui peut être utilisée pour une application web ASP.NET Dynamic Entities, vous devez suivre les étapes de la procédure **Utilisation d’une base de données SQL Azure pour votre application** plus haut dans cette rubrique.
2. Ajoutez les tables et les champs dont vous avez besoin pour cette base de données à partir du [Portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).
3. La chaîne de connexion pour ce type d'application a le format suivant dans le fichier web.config :  

    ```
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Mettez à jour la valeur *connectionString* avec la chaîne de connexion ADO.NET pour votre base de données SQL Azure comme suit :

    ```
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```
4. Pour enregistrer le fichier web.config avec les modifications que vous avez apportées à la chaîne de connexion, dans la barre de menu, choisissez **Fichier**, **Enregistrer web.config**.

## <a name="supported-project-templates"></a>Modèles de projet pris en charge
Pour publier une application web sur Azure, l’application doit utiliser un des modèles de projet pour C# ou Visual Basic répertoriés dans le tableau ci-dessous.

| Groupe de modèles de projet | Modèle de projet |
| --- | --- |
| Web |Application Web ASP.NET |
| Web |Application Web ASP.NET MVC 2 |
| Web |Application Web ASP.NET MVC 3 |
| Web |Application Web ASP.NET MVC 4 |
| Web |Application Web vide ASP.NET |
| Web |Application Web vide ASP.NET MVC 2 |
| Web |Application Web ASP.NET Dynamic Data Entities |
| Web |Application Web ASP.NET Dynamic Data Linq to SQL |
| Silverlight |Application Silverlight |
| Silverlight |Silverlight Business Application |
| Silverlight |Silverlight Navigation Application |
| WCF |WCF Service Application |
| WCF |WCF Workflow Service Application |
| Workflow |WCF Workflow Service Application |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la publication, consultez [Préparer la publication ou le déploiement d’une application Azure à partir de Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md). Consultez également [Configuration des informations d’authentification nommées](vs-azure-tools-setting-up-named-authentication-credentials.md).



<!--HONumber=Dec16_HO2-->


