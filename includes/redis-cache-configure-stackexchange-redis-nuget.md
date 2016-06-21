Les applications .NET peuvent utiliser le client de cache **StackExchange.Redis**, qui peut être configuré dans Visual Studio à l’aide d’un package NuGet simplifiant la configuration des applications clientes de cache.

>[AZURE.NOTE] Pour plus d’informations, consultez la page github [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) et la [documentation du client de cache StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).

Pour configurer une application cliente dans Visual Studio avec le package NuGet StackExchange.Redis, cliquez avec le bouton droit sur le projet dans l'**Explorateur de solutions** et choisissez **Gérer les packages NuGet**.

![Manage NuGet packages](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Tapez **StackExchange.Redis** ou **StackExchange.Redis.StrongName** dans la zone de texte de recherche, sélectionnez la version souhaitée dans la liste des résultats, puis cliquez sur **Installer**.

>[AZURE.NOTE] Si vous préférez utiliser une version avec nom fort de la bibliothèque du client **StackExchange.Redis**, choisissez **StackExchange.Redis.StrongName**. Sinon, choisissez **StackExchange.Redis**.

![StackExchange.Redis NuGet package](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Le package NuGet télécharge et ajoute les références d'assembly nécessaires pour que votre application cliente puisse accéder à Cache Redis Azure avec le client du cache StackExchange.Redis.

<!---HONumber=AcomDC_0615_2016-->