<properties
   pageTitle="Mise en cache du fournisseur de caches de sortie ASP.NET"
   description="Découvrez comment mettre en cache les sortie de pages ASP.NET à l’aide du Cache Redis Azure"
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="erikre"
   editor="tysonn" />
<tags
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="01/13/2016"
   ms.author="sdanie" />

# Fournisseur de caches de sortie ASP.NET pour le Cache Redis Azure

Le fournisseur de caches de sortie Redis est un mécanisme de stockage hors processus pour les données de cache de sortie. Ces données concernent spécialement les réponses HTTP complètes (mise en cache de la sortie de pages). Le fournisseur se connecte au nouveau point d'extension du fournisseur de caches de sortie introduit dans ASP.NET 4.

Pour utiliser le fournisseur de caches de sortie Redis, configurez d’abord votre cache, puis configurez votre application ASP.NET en utilisant le package NuGet du fournisseur de caches de sortie Redis. Cette rubrique fournit des conseils sur la configuration de votre application pour utiliser le fournisseur de caches de sortie Redis. Pour plus d’informations sur la création et la configuration d’une instance de Cache Redis Azure, consultez la section [Création d’un cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## Stockage de la sortie de pages ASP.NET dans le cache

Pour configurer une application cliente dans Visual Studio avec le package NuGet du fournisseur de caches de sortie Redis, cliquez avec le bouton droit sur l’**Explorateur de solutions** et choisissez **Gérer les packages NuGet**.

![Cache Redis Azure - Gérer les packages NuGet](./media/cache-asp.net-output-cache-provider/IC729541.png)

Entrez **RedisOutputCacheProvider** dans la zone de texte **Rechercher en ligne**, choisissez parmi les résultats et cliquez sur **Installer**.

![Fournisseur de caches de sortie ASP.NET du Cache Redis Azure](./media/cache-asp.net-output-cache-provider/IC751727.jpg)

Le package NuGet du fournisseur de caches de sortie Redis a une dépendance sur le package StackExchange.Redis.StrongName. Le package StackExchange.Redis.StrongName sera automatiquement installé s’il ne figure pas déjà dans votre projet. Notez qu’il existe, en plus du package StackExchange.Redis.StrongName avec nom fort, une version de StackExchange.Redis sans nom fort. Si votre projet utilise la version de StackExchange.Redis sans nom fort, vous devez la désinstaller avant ou après avoir installé le package NuGet du fournisseur de caches de sortie Redis. À défaut, vous risquez de rencontrer des conflits de noms dans votre projet. Pour plus d’informations sur ces packages, consultez la section [Configuration des clients de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Le package NuGet télécharge et ajoute les références d’assembly nécessaires et la section suivante au fichier web.config qui contient la configuration requise permettant à l’application ASP.NET d’utiliser le fournisseur de caches de sortie Redis.

    <caching>
      <outputCachedefaultProvider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache"type="Microsoft.Web.Redis.RedisOutputCacheProvider"host="127.0.0.1"accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

La section commentée fournit un exemple d’attributs et de paramétrage pour chacun de ces attributs.

Configurez les attributs avec les valeurs du panneau de votre cache sur le portail Microsoft Azure et configurez les autres valeurs selon votre choix. Pour obtenir des instructions sur l’accès aux propriétés de votre cache, consultez la section [Configuration des paramètres de cache Redis](cache-configure.md#configure-redis-cache-settings).

-	**host** : spécifiez le point de terminaison de votre cache.
-	**port** : utilisez votre port non SSL ou votre port SSL, selon les paramètres ssl.
-	**accessKey** : utilisez la clé primaire ou secondaire pour votre cache.
-	**ssl** : choisissez « true » si vous souhaitez sécuriser les communications cache/client avec ssl ; sinon, choisissez « false ». Veillez à spécifier le port approprié.
	-	Le port non SSL est désactivé par défaut pour les nouveaux caches. Spécifiez true pour utiliser le port SSL pour ce paramètre. Pour plus d’informations sur l’activation du port non SSL, consultez la section relative aux [ports d’accès](cache-configure.md#access-ports) dans la rubrique [Configuration d’un cache](cache-configure.md).
-	**databaseId** : spécifiez la base de données à utiliser pour les données de sortie du cache. Si ce champ n’est pas spécifié, la valeur 0 sera utilisée par défaut.
-	**applicationName** : les clés sont stockées dans redis sous <AppName>\_<SessionId>\_Data. Cela permet à plusieurs applications de partager la même clé. Ce paramètre est facultatif. Si vous n’indiquez aucune valeur, une valeur par défaut sera utilisée.
-	**connectionTimeoutInMilliseconds** : ce paramètre vous permet de remplacer le paramètre connectTimeout dans le client StackExchange.Redis. S’il n’est pas spécifié, le paramètre par défaut connectTimeout 5000 est utilisé. Pour plus d’informations, consultez le [modèle de configuration StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-	**operationTimeoutInMilliseconds** : ce paramètre vous permet de remplacer le paramètre syncTimeout dans le client StackExchange.Redis. S’il n’est pas spécifié, le paramètre par défaut syncTimeout 1000 est utilisé. Pour plus d’informations, consultez le [modèle de configuration StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Ajoutez une directive OutputCache à chaque page pour laquelle vous voulez mettre en cache la sortie.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Dans cet exemple, les données de page mises en cache resteront dans le cache pendant 60 secondes et une version différente de la page sera mise en cache pour chaque combinaison de paramètres. Pour plus d’informations sur la directive OutputCache, consultez [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Une fois ces étapes effectuées, votre application est configurée pour utiliser le fournisseur de caches de sortie Redis.

## Étapes suivantes

Consultez la page [ASP.NET Session State Provider for Azure Redis Cache](cache-aspnet-session-state-provider.md) (en anglais).

<!---HONumber=AcomDC_0427_2016-->