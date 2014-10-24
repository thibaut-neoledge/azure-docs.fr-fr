<properties linkid="mobile-services-dotnet-backend-how-to-troubleshoot" urlDisplayName="Troubleshoot the Mobile Services .NET Backend" pageTitle="Troubleshoot the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn how to diagnose and fix issues with your mobile services using the .NET backend" metaCanonical="" services="" documentationCenter="Mobile" title="Troubleshoot the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Résolution des problèmes du service principal .NET de Mobile Services

Le développement avec Mobile Services est généralement simple et facile, mais, même dans ces conditions, les choses peuvent parfois aller de travers. Ce didacticiel aborde certaines techniques qui vous permettent de résoudre des problèmes courants susceptibles de se produire avec le service principal .NET de Mobile Services.

1.  [Débogage HTTP][Débogage HTTP]
2.  [Débogage du runtime][Débogage du runtime]
3.  [Analyse des journaux de diagnostic][Analyse des journaux de diagnostic]
4.  [Débogage de la résolution d'assembly dans le cloud][Débogage de la résolution d'assembly dans le cloud]
5.  [Dépannage des migrations Entity Framework][Dépannage des migrations Entity Framework]

<a name="HttpDebugging"></a>

## Débogage HTTP

Lorsque vous développez des applications avec Mobile Services, vous tirez généralement parti du Kit de développement logiciel (SDK) client Mobile Services pour la plateforme dont vous vous servez (Windows Store, iOS, Android, etc.). Parfois, cependant, il est intéressant de descendre jusqu'au niveau HTTP et d'observer les appels bruts lorsqu'ils surviennent sur le réseau. Cette approche est particulièrement utile pour déboguer les problèmes de connectivité et de sérialisation. Avec le service principal .NET de Mobile Services, vous pouvez utiliser cette approche en combinaison avec le débogage local et à distance de Visual Studio (pour plus d'informations à ce sujet, reportez-vous à la section suivante) pour vous faire une idée complète de tout le chemin que parcourt un appel HTTP avant d'appeler le code de votre service.

Vous pouvez utiliser n'importe quel débogueur HTTP pour envoyer et inspecter du trafic HTTP. [Fiddler][Fiddler] est un outil populaire, couramment utilisé par les développeurs à cet effet. Pour faciliter la vie des développeurs, Mobile Services intègre un débogueur HTTP basé sur le web (également appelé « client test ») directement à votre service mobile, réduisant ainsi le besoin d'outils externes. Lorsque votre service mobile est hébergé localement, il est accessible à partir d'un URI similaire à [][]<http://localhost:59233></a> ; lorsqu'il est hébergé dans le cloud, l'URI prend la forme suivante : [][1]<http://todo-list.azure-mobile.net></a>. Les étapes suivantes fonctionnent de la même manière quel que soit l'emplacement où le service est hébergé :

1.  Partez d'un projet de serveur Mobile Services ouvert dans **Visual Studio 2013 Update 2** ou une version ultérieure. Si vous n'avez pas de projet sous la main, vous pouvez en créer un en sélectionnant **Fichier**, **Nouveau**, **Projet**, puis en sélectionnant le nœud **Cloud** et ensuite le modèle **Windows Azure Mobile Services**.
2.  Appuyez sur **F5** pour créer et exécuter le projet. Sur la page de démarrage, sélectionnez **faire un essai**.

    > [WACOM.NOTE]
    > Si le service est hébergé localement, un clic sur le lien vous permet d'accéder à la page suivante. Mais si l'hébergement se fait dans le cloud, un ensemble d'informations d'identification vous est demandé. Le but est de veiller à ce que les utilisateurs non authentifiés n'aient pas accès aux informations sur votre API et vos charges utiles. Pour afficher la page, vous devez vous connecter avec un **nom d'utilisateur vide** et votre **clé d'application** comme mot de passe. Vous pouvez accéder à votre clé d'application dans le **portail de gestion Azure**, sous l'onglet **Tableau de bord** de votre service mobile, en sélectionnant **Gérer les clés**.
    >
    > ![Authentication prompt to access help page][Authentication prompt to access help page]

3.  La page qui apparaît (désignée comme la « page d'aide ») affiche une liste de toutes les API HTTP que votre service mobile met à disposition. Sélectionnez l'une des API (si vous avez commencé avec le modèle de projet Mobile Services dans Visual Studio, **GET tables/TodoItem** est normalement affiché).

    ![Help page][Help page]

4.  La page qui apparaît alors affiche la documentation et des exemples JSON de la requête et de la réponse qu'attend l'API. Cliquez sur le bouton **faire un essai**.

    ![Test page for an API][Test page for an API]

5.  Voici le « client test », qui vous permet d'envoyer une requête HTTP pour faire un essai avec votre API. Sélectionnez **envoyer**.

    ![Test client][Test client]

6.  La réponse HTTP renvoyée par votre service mobile s'affiche directement dans la fenêtre du navigateur.

    ![Test client with HTTP response][Test client with HTTP response]

Vous êtes maintenant prêt à explorer les différentes API HTTP exposées par votre service mobile en profitant de la commodité de votre navigateur web.

<a name="RuntimeDebugging"></a>

## Débogage du runtime

L'une des caractéristiques essentielles du service principal .NET est la possibilité de déboguer le code du service localement, mais également de déboguer le code qui s'exécute en ligne dans l'environnement cloud. Procédez comme suit :

1.  Ouvrez le projet de service mobile que vous souhaitez déboguer dans **Visual Studio 2013 Update 2** ou une version ultérieure.
2.  Configurez le chargement des symboles. Accédez au menu **Déboguer** et sélectionnez **Options et paramètres**. Assurez-vous que l'option **Activer Uniquement mon code** est désactivée et l'option **Activer le support du serveur source** activée.

    ![Configure symbol loading][Configure symbol loading]

3.  Sélectionnez le nœud **Symboles** sur la gauche et ajoutez une référence au serveur (SymbolSource)[<http://symbolsource.org>] avec l'URI [][2]<http://srv.symbolsource.org/pdb/Public></a>. Les symboles pour le service principal .NET de Mobile Services sont disponibles à cet emplacement avec chaque nouvelle version.

    ![Configure symbol server][Configure symbol server]

4.  Définissez un point d'arrêt dans le bloc de code que vous voulez déboguer. Par exemple, définissez un point d'arrêt dans la méthode **GetAllTodoItems()** de la classe **TodoItemController** qui est fournie avec le modèle de projet Mobile Services dans Visual Studio.
5.  Déboguez le service localement en appuyant sur **F5**. La première charge peut être lente pendant que Visual Studio télécharge les symboles pour le service principal .NET de Mobile Services.
6.  Comme décrit dans la précédente section sur le débogage HTTP, utilisez le client test pour envoyer une requête HTTP à la méthode où vous avez défini le point d'arrêt. Vous pouvez, par exemple, envoyer une requête à la méthode **GetAllTodoItems()** en sélectionnant **GET tables/TodoItem** sur la page d'aide, puis en sélectionnant **faire un essai** et ensuite **envoyer**.
7.  Visual Studio doit s'arrêter au point d'arrêt que vous avez défini et une trace de la pile complète avec le code source doit être disponible dans la fenêtre **Pile des appels** de Visual Studio.

    ![Hitting a breakpoint][Hitting a breakpoint]

8.  Vous pouvez à présent publier le service dans Azure et nous serons en mesure d'utiliser le débogage exactement comme nous l'avons fait plus haut. Publiez le projet en cliquant avec le bouton droit dans l'**Explorateur de solutions** et en sélectionnant **Publier**.
9.  Sous l'onglet **Paramètres** de l'Assistant Publication, sélectionnez la configuration **Débogage**. Cette option garantit que les symboles de débogage appropriés sont publiés avec votre code.

    ![Publish debug][Publish debug]

10. Une fois le service publié, ouvrez l'**Explorateur de serveurs** et développez les nœuds **Windows Azure** et **Mobile Services**. Connectez-vous si nécessaire.
11. Cliquez avec le bouton droit sur le service mobile que vous venez de publier et sélectionnez **Attacher le débogueur**.

    ![Attacher le débogueur][Attacher le débogueur]

12. En procédant exactement comme à l'étape 6, envoyez une requête HTTP pour appeler la méthode où vous avez défini le point d'arrêt. Cette fois, utilisez la page d'aide et le client test pour le service mobile hébergé dans Azure.
13. Visual Studio s'arrête au point d'arrêt.

Vous avez maintenant accès à toute la puissance du débogueur Visual Studio lorsque vous développez localement et pour votre service mobile en ligne dans Azure.

<a name="Logs"></a>

## Analyse des journaux de diagnostic

Lorsque votre service mobile traite les demandes de vos clients, il génère différentes informations de diagnostic utiles et capture également toutes les exceptions rencontrées. En outre, vous pouvez associer au code de votre contrôleur des journaux supplémentaires en tirant parti de la propriété [**Log**][**Log**] disponible sur la propriété [**Services**][**Services**] de chaque classe [**TableController**][**TableController**].

Lorsque le débogage s'effectue localement, les journaux apparaissent dans la fenêtre **Sortie** de Visual Studio.

![Logs in Visual Studio Output window][Logs in Visual Studio Output window]

Une fois votre service publié dans Azure, vous pouvez accéder aux journaux de l'instance de service exécutée dans le cloud en cliquant avec le bouton droit sur le service mobile dans l'**Explorateur de serveurs** de Visual Studio, puis en sélectionnant **Afficher les journaux**.

![Logs in Visual Studio Server Explorer][Logs in Visual Studio Server Explorer]

Ces mêmes journaux sont également disponibles dans le **portail de gestion Azure** sous l'onglet **Journaux** de votre service mobile.

![Logs in Azure Management Portal][Logs in Azure Management Portal]

<a name="AssemblyResolution"></a>

## Débogage de la résolution d'assembly dans le cloud

Lorsque vous publiez votre service mobile dans Azure, il est chargé par l'environnement d'hébergement de Mobile Services, qui garantit des mises à niveau et des correctifs transparents pour le pipeline HTTP qui héberge le code de votre contrôleur. Cela inclut tous les assemblys référencés par les [packages NuGet du service principal .NET][packages NuGet du service principal .NET] : l'équipe met sans cesse à jour le service pour utiliser les dernières versions de ces assemblys.

Il est parfois possible d'introduire des conflits de versions en référençant *différentes versions principales* des assemblys requis (différentes versions *mineures* sont autorisées). Le problème se produit souvent lorsque NuGet vous invite à effectuer une mise à niveau vers la dernière version de l'un des packages utilisés par le service principal .NET de Mobile Services.

    > [WACOM.NOTE] Mobile Services is currently compatible only with ASP.NET 5.1; ASP.NET 5.2 is not presently supported. Upgrading your ASP.NET NuGet packages to 5.2.* may result in an error after deployment.

Si vous mettez à niveau l'un de ces packages, lorsque vous publiez le service mis à jour dans Azure, une page d'avertissement vous signale le conflit :

![Help page indicating assembly loading conflict][Help page indicating assembly loading conflict]

Cette page est accompagnée de l'enregistrement dans vos journaux de service d'un message d'exception similaire au suivant :

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

Ce problème est facile à corriger : revenez simplement à une version prise en charge de l'assembly requis et republiez votre service.

<a name="EFMigrations"></a>

## Dépannage des migrations Entity Framework

Lorsque vous utilisez le service principal .NET de Mobile Services avec une base de données SQL, Entity Framework (EF) est utilisé comme technologie d'accès aux données vous permettant d'interroger la base de données et de rendre persistants les objets qu'elle contient. Un aspect important traité par EF pour le compte du développeur concerne la façon dont les colonnes de la base de données (autrement dit le *schéma* de la base de données) changent en même temps que les classes de modèle spécifiées dans le code. Ce processus est appelé [migrations Code First][migrations Code First].

Les migrations peuvent être complexes et nécessitent que l'état de la base de données reste synchronisé avec le modèle EF pour réussir. Pour des instructions sur la façon de gérer les migrations avec votre service mobile et sur les erreurs qui peuvent survenir, consultez la page [Modifications des modèles de données pour un service mobile principal .NET][Modifications des modèles de données pour un service mobile principal .NET].

<!-- IMAGES -->

  [Débogage HTTP]: #HttpDebugging
  [Débogage du runtime]: #RuntimeDebugging
  [Analyse des journaux de diagnostic]: #Logs
  [Débogage de la résolution d'assembly dans le cloud]: #AssemblyResolution
  [Dépannage des migrations Entity Framework]: #EFMigrations
  [Fiddler]: http://www.telerik.com/fiddler
  []: http://localhost:59233
  [1]: http://todo-list.azure-mobile.net
  [Authentication prompt to access help page]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
  [Help page]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
  [Test page for an API]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
  [Test client]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
  [Test client with HTTP response]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
  [Configure symbol loading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
  [2]: http://srv.symbolsource.org/pdb/Public
  [Configure symbol server]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
  [Hitting a breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
  [Publish debug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
  [Attacher le débogueur]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
  [**Log**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx
  [**Services**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx
  [**TableController**]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx
  [Logs in Visual Studio Output window]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
  [Logs in Visual Studio Server Explorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
  [Logs in Azure Management Portal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
  [packages NuGet du service principal .NET]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [Help page indicating assembly loading conflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png
  [migrations Code First]: http://msdn.microsoft.com/en-us/data/jj591621
  [Modifications des modèles de données pour un service mobile principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/
