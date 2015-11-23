<properties 
   pageTitle="Ajouter Azure Storage à l’aide des services connectés dans Visual Studio | Microsoft Azure"
   description="Ajouter le stockage Azure à votre application à l’aide de la boîte de dialogue Ajouter des services connectés de Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="08/12/2015"
   ms.author="tarcher" />

# Ajout de stockage Azure à l’aide des services connectés de Visual Studio

## Vue d’ensemble

Avec Visual Studio 2015, vous pouvez connecter n’importe quel service cloud C#, service mobile principal .NET, service ou site web ASP.NET, service ASP.NET 5 ou service de tâche web Azure au stockage Azure à l’aide de la boîte de dialogue **Ajouter des services connectés**. La fonctionnalité de service connecté ajoute l’ensemble des références et du code de connexion nécessaires, et modifie vos fichiers de configuration de manière appropriée. La boîte de dialogue vous permet également d’accéder à la documentation qui vous indique quelles sont les étapes suivantes pour démarrer le stockage d’objets BLOB, les files d’attente et les tables.

## Types de projet pris en charge

Vous pouvez utiliser la boîte de dialogue Services connectés pour vous connecter au stockage Azure dans les types de projets suivants.

- Projets web ASP.NET

- Projets ASP.NET 5

- Projets de rôle de travail et de rôle web de service cloud .NET

- Projets de services mobiles .NET

- Projets de tâche web Azure


## Connexion au stockage Azure à l’aide de la boîte de dialogue Services connectés

1. Assurez-vous que vous disposez d’un compte Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](http://go.microsoft.com/fwlink/?LinkId=518146). Une fois que vous disposez d’un compte Azure, vous pouvez créer des comptes de stockage, créer des services mobiles et configurer Azure Active Directory.

1. Ouvrez votre projet dans Visual Studio, ouvrez le menu contextuel du nœud **Références** dans l’Explorateur de solutions, puis choisissez **Ajouter un service connecté**.

    ![Ajout d’un service connecté](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Dans la boîte de dialogue **Ajouter un service connecté**, choisissez **Azure Storage**, puis choisissez le bouton **Configurer**. Si vous n’êtes pas encore connecté à Azure, vous serez invité à le faire.

    ![Boîte de dialogue Ajouter des services connectés - Stockage](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. Dans la boîte de dialogue **Stockage Azure**, choisissez un compte de stockage existant, puis cliquez sur **Ajouter**.

    Si vous devez créer un compte de stockage, passez à l’étape suivante. Sinon, passez à l’étape 6.

    ![Boîte de dialogue Stockage Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. Pour créer un compte de stockage :

    1. Choisissez le bouton **Créer un compte de stockage** au bas de la boîte de dialogue Stockage Azure.

    1. Remplissez la boîte de dialogue **Créer un Compte de stockage**, puis choisissez le bouton **Créer**.
    
        ![Boîte de dialogue Stockage Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Quand vous revenez à la boîte de dialogue **Stockage Azure**, le nouveau stockage s’affiche dans la liste.

    1. Sélectionnez le nouveau stockage dans la liste et cliquez sur **Ajouter**.

1. Le service connecté de stockage s’affiche sous le nœud Références de service de votre projet de tâche web.

    ![Stockage Azure dans un projet de tâches web](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Dans la page Prise en main qui s’affiche, examinez de quelle manière votre projet a été modifié. Cette page s’affiche dans votre navigateur quand vous ajoutez un service connecté. Vous pouvez parcourir les étapes suggérées et les exemples de code fournis, ou bien afficher la page Que s’est-il passé ? pour voir quelles références ont été ajoutées à votre projet et quelles modifications ont été apportées à vos fichiers de code et de configuration.

## Modifications apportées à votre projet

Quand vous avez terminé la boîte de dialogue, Visual Studio ajoute des références et modifie certains fichiers de configuration. Les modifications spécifiques varient selon le type de projet.

 - Pour les projets ASP.NET, consultez [Que s’est-il passé ? – Projets ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
 - Pour les projets ASP.NET 5, consultez [Que s’est-il passé ? – Projets ASP.NET 5](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
 - Pour les projets de service cloud (rôles web et de travail), consultez [Que s’est-il passé ? – Projets de service cloud](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
 - Pour les projets de tâche web, consultez [Que s’est-il passé ? – Projets de tâche web](vs-storage-webjobs-what-happened/).

## Étapes suivantes

1. En vous basant sur les exemples de code de la page Prise en main, créez le type de stockage que vous voulez, puis démarrez l’écriture de code pour accéder à votre compte de stockage !

1. Posez des questions et obtenez de l’aide
     - [Forum MSDN : Stockage Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Stockage sur azure.microsoft.com](http://azure.microsoft.com/services/storage)

     - [Documentation du stockage sur azure.microsoft.com](http://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=Nov15_HO3-->