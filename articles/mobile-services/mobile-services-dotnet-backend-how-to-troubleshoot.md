<properties
	pageTitle="Résolution des problèmes du backend .NET Mobile Services | Microsoft Azure"
	description="Découvrez comment diagnostiquer et résoudre les problèmes liés à vos services mobiles à l'aide du backend .NET"
	services="mobile-services"
	documentationCenter=""
	authors="wesmc7777"
	manager="erikre"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/13/2016" 
	ms.author="wesmc;ricksal"/>

# Résolution des problèmes du backend .NET Mobile Services

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


Le développement avec Mobile Services est généralement simple et facile, mais, même dans ces conditions, les choses peuvent parfois aller de travers. Ce didacticiel aborde certaines techniques qui vous permettent de résoudre des problèmes courants susceptibles de se produire avec le backend .NET Mobile Services.

1. [Débogage HTTP](#HttpDebugging)
2. [Débogage du runtime](#RuntimeDebugging)
3. [Analyse des journaux de diagnostic](#Logs)
4. [Débogage de la résolution d'assembly dans le cloud](#AssemblyResolution)
5. [Dépannage des migrations Entity Framework](#EFMigrations)

<a name="HttpDebugging"></a>
## Débogage HTTP

Lorsque vous développez des applications avec Mobile Services, vous tirez généralement parti du Kit de développement logiciel (SDK) client Mobile Services pour la plateforme dont vous vous servez (Windows Store, iOS, Android, etc.). Parfois, cependant, il est intéressant de descendre jusqu'au niveau HTTP et d'observer les appels bruts lorsqu'ils surviennent sur le réseau. Cette approche est particulièrement utile pour déboguer les problèmes de connectivité et de sérialisation. Avec le backend .NET Mobile Services, vous pouvez utiliser cette approche en combinaison avec le débogage local et à distance de Visual Studio (pour plus d'informations à ce sujet, reportez-vous à la section suivante) pour vous faire une idée complète de tout le chemin que parcourt un appel HTTP avant d'appeler le code de votre service.

Vous pouvez utiliser n'importe quel débogueur HTTP pour envoyer et inspecter du trafic HTTP. [Fiddler](http://www.telerik.com/fiddler) est un outil populaire, couramment utilisé par les développeurs à cet effet. Pour faciliter la vie des développeurs, Mobile Services intègre un débogueur HTTP basé sur le web (également appelé « client test ») directement à votre service mobile, réduisant ainsi le besoin d'outils externes. Lors de l'hébergement de votre service mobile localement, il sera disponible sur un URI semblable à `http://localhost:59233`. Lors de l'hébergement dans le nuage, l'URI aura la forme suivante : `http://todo-list.azure-mobile.net`. Les étapes suivantes fonctionnent de la même manière quel que soit l'emplacement où le service est hébergé :

1. Partez d'un projet de serveur Mobile Services ouvert dans **Visual Studio 2013 Update 2** ou une version ultérieure. Si vous n'avez pas de projet sous la main, vous pouvez en créer un en sélectionnant **Fichier**, **Nouveau**, **Projet**, puis en sélectionnant le nœud **Cloud** et ensuite le modèle **Microsoft Azure Mobile Services**.
2. Appuyez sur **F5** pour créer et exécuter le projet. Sur la page de démarrage, sélectionnez **faire un essai**.

    >[AZURE.NOTE]
    Si le service est hébergé localement, un clic sur le lien vous permet d'accéder à la page suivante. Mais si l'hébergement se fait dans le cloud, un ensemble d'informations d'identification vous est demandé. Le but est de veiller à ce que les utilisateurs non authentifiés n'aient pas accès aux informations sur votre API et vos charges utiles. Pour afficher la page, vous devez vous connecter avec un **nom d'utilisateur vide** et votre **clé d'application** comme mot de passe. Vous pouvez accéder à votre clé d'application dans le portail Azure Classic, sous l'onglet **Tableau de bord** de votre service mobile, en sélectionnant **Gérer les clés**.
    >
    > ![Authentication prompt to access help page][HelpPageAuth]

3. La page qui apparaît (désignée comme la « page d'aide ») affiche une liste de toutes les API HTTP que votre service mobile met à disposition. Sélectionnez l'une des API (si vous avez commencé avec le modèle de projet Mobile Services dans Visual Studio, **GET tables/TodoItem** est normalement affiché).

    ![Help page][HelpPage]

4. La page qui apparaît alors affiche la documentation et des exemples JSON de la requête et de la réponse qu'attend l'API. Cliquez sur le bouton **faire un essai**.

    ![Test page for an API][HelpPageApi]

5. Voici le « client test », qui vous permet d'envoyer une requête HTTP pour faire un essai avec votre API. Sélectionnez **envoyer**.

    ![Test client][TestClient]

6. La réponse HTTP renvoyée par votre service mobile s'affiche directement dans la fenêtre du navigateur.

    ![Test client with HTTP response][TestClientResponse]

Vous êtes maintenant prêt à explorer les différentes API HTTP exposées par votre service mobile en profitant de la commodité de votre navigateur web.

<a name="RuntimeDebugging"></a>
## Débogage du runtime

L'une des caractéristiques essentielles du backend .NET est la possibilité de déboguer le code du service localement, mais également de déboguer le code qui s'exécute en ligne dans l'environnement cloud. Procédez comme suit :

1. Ouvrez le projet de service mobile que vous souhaitez déboguer dans **Visual Studio 2013 Update 2** ou version ultérieure.
2. Configurez le chargement des symboles. Accédez au menu **Déboguer** et sélectionnez **Options et paramètres**. Assurez-vous que l'option **Activer Uniquement mon code** est désactivée et l'option **Activer le support du serveur source** activée.

    ![Configure symbol loading][SymbolLoading]

3. Sélectionnez le nœud **Symboles** sur la gauche et ajoutez une référence au serveur [SymbolSource] avec l'URI `http://srv.symbolsource.org/pdb/Public`. Les symboles pour le service principal .NET de Mobile Services sont disponibles à cet emplacement avec chaque nouvelle version.

    ![Configure symbol server][SymbolServer]

4. Définissez un point d'arrêt dans le bloc de code que vous voulez déboguer. Par exemple, définissez un point d'arrêt dans la méthode **GetAllTodoItems()** de la classe **TodoItemController** qui est fournie avec le modèle de projet Mobile Services dans Visual Studio.
5. Déboguez le service localement en appuyant sur **F5**. La première charge peut être lente pendant que Visual Studio télécharge les symboles pour le backend .NET Mobile Services.
6. Comme décrit dans la précédente section sur le débogage HTTP, utilisez le client test pour envoyer une requête HTTP à la méthode où vous avez défini le point d'arrêt. Vous pouvez, par exemple, envoyer une requête à la méthode **GetAllTodoItems()** en sélectionnant **GET tables/TodoItem** sur la page d'aide, puis en sélectionnant **faire un essai** et ensuite **envoyer**.
7. Visual Studio doit s'arrêter au point d'arrêt que vous avez défini et une trace de la pile complète avec le code source doit être disponible dans la fenêtre **Pile des appels** de Visual Studio.

    ![Hitting a breakpoint][Breakpoint]

8. Vous pouvez à présent publier le service dans Azure et nous serons en mesure d'utiliser le débogage exactement comme nous l'avons fait plus haut. Publiez le projet en cliquant avec le bouton droit dans l'**Explorateur de solutions** et en sélectionnant **Publier**.
9. Sous l'onglet **Paramètres** de l'Assistant Publication, sélectionnez la configuration **Débogage**. Cette option garantit que les symboles de débogage appropriés sont publiés avec votre code.

    ![Publish debug][PublishDebug]

10. Une fois le service publié, ouvrez l'**Explorateur de serveurs** et développez les nœuds **Azure** et **Mobile Services**. Connectez-vous si nécessaire.
11. Cliquez avec le bouton droit sur le service mobile que vous venez de publier et sélectionnez **Attacher le débogueur**.

    ![Attach debugger][AttachDebugger]

12. En procédant exactement comme à l'étape 6, envoyez une requête HTTP pour appeler la méthode où vous avez défini le point d'arrêt. Cette fois, utilisez la page d'aide et le client test pour le service mobile hébergé dans Azure.
13. Visual Studio s'arrête au point d'arrêt.

Vous avez maintenant accès à toute la puissance du débogueur Visual Studio lorsque vous développez localement et pour votre service mobile en ligne dans Azure.

<a name="Logs"></a>
## Analyse des journaux de diagnostic

Lorsque votre service mobile traite les demandes de vos clients, il génère différentes informations de diagnostic utiles et capture également toutes les exceptions rencontrées. En outre, vous pouvez associer au code de votre contrôleur des journaux supplémentaires en tirant parti de la propriété [**Log**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.apiservices.log.aspx) disponible sur la propriété [**Services**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.services.aspx) de chaque classe [**TableController**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx).

Lorsque le débogage s'effectue localement, les journaux apparaissent dans la fenêtre **Sortie** de Visual Studio.

![Logs in Visual Studio Output window][LogsOutputWindow]

Une fois votre service publié dans Azure, vous pouvez accéder aux journaux de l'instance de service exécutée dans le cloud en cliquant avec le bouton droit sur le service mobile dans l'**Explorateur de serveurs** de Visual Studio, puis en sélectionnant **Afficher les journaux**.

![Logs in Visual Studio Server Explorer][LogsServerExplorer]

Ces mêmes journaux sont également disponibles dans le portail Azure Classic sous l'onglet **Journaux** de votre service mobile.

![Logs in Azure classic portal][LogsPortal]

<a name="AssemblyResolution"></a>
## Débogage de la résolution d'assembly dans le cloud

Lorsque vous publiez votre service mobile dans Azure, il est chargé par l'environnement d'hébergement de Mobile Services, qui garantit des mises à niveau et des correctifs transparents pour le pipeline HTTP qui héberge le code de votre contrôleur. Cela inclut tous les assemblys référencés par les [packages NuGet du backend .NET](http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22) : l'équipe met continuellement à jour le service pour utiliser les dernières versions de ces assemblys.

Il est parfois possible d'introduire des conflits de versions en référençant *différentes versions principales* des assemblys requis (différentes versions *mineures* sont autorisées). Le problème se produit souvent quand NuGet vous invite à effectuer une mise à niveau vers la dernière version de l’un des packages utilisés par le backend .NET Mobile Services.

>[AZURE.NOTE] Mobile Services est compatible uniquement avec ASP.NET 5.1 ; ASP.NET 5.2 n'est pas pris en charge actuellement. La mise à niveau de vos packages NuGet ASP.NET vers 5.2.* peut provoquer une erreur après le déploiement.

Si vous mettez à niveau l'un de ces packages, lorsque vous publiez le service mis à jour dans Azure, une page d'avertissement vous signale le conflit :

![Help page indicating assembly loading conflict][HelpConflict]

Cette page est accompagnée de l'enregistrement dans vos journaux de service d'un message d'exception similaire au suivant :

    Found conflicts between different versions of the same dependent assembly 'Microsoft.ServiceBus': 2.2.0.0, 2.3.0.0. Please change your project to use version '2.2.0.0' which is the one currently supported by the hosting environment.

Ce problème est facile à corriger : revenez simplement à une version prise en charge de l'assembly requis et republiez votre service.

<a name="EFMigrations"></a>
## Dépannage des migrations Entity Framework

Lorsque vous utilisez le backend .NET Mobile Services avec une base de données SQL, Entity Framework (EF) est utilisé comme technologie d'accès aux données vous permettant d'interroger la base de données et de rendre persistants les objets qu'elle contient. Un aspect important traité par EF pour le compte du développeur concerne la façon dont les colonnes de la base de données (autrement dit le *schéma* de la base de données) changent en même temps que les classes de modèle spécifiées dans le code. Ce processus est appelé [migrations Code First](http://msdn.microsoft.com/data/jj591621).

Les migrations peuvent être complexes et nécessitent que l'état de la base de données reste synchronisé avec le modèle EF pour réussir. Pour des instructions sur la façon de gérer les migrations avec votre service mobile et sur les erreurs qui peuvent survenir, consultez la page [Modifications des modèles de données pour un service mobile de backend .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

<!-- IMAGES -->

[HelpPageAuth]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/6.png
[HelpPage]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/7.png
[HelpPageApi]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/8.png
[TestClient]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/9.png
[TestClientResponse]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/10.png
[SymbolLoading]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/1.png
[SymbolServer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/2.png
[Breakpoint]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/3.png
[PublishDebug]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/4.png
[AttachDebugger]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/5.png
[LogsOutputWindow]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/11.png
[LogsServerExplorer]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/12.png
[LogsPortal]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/13.png
[HelpConflict]: ./media/mobile-services-dotnet-backend-how-to-troubleshoot/14.png


<!-- Links -->
[SymbolSource]: http://www.symbolsource.org/Public

<!---HONumber=AcomDC_0720_2016-->