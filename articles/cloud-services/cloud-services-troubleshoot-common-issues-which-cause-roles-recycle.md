<properties
   pageTitle="Causes courantes de recyclage des rôles de service cloud | Microsoft Azure"
   description="Le recyclage soudain d’un rôle de service cloud peut entraîner d’importants temps d’arrêt. Voici quelques problèmes courants entraînant le recyclage des rôles, qui peuvent vous aider à réduire les temps d’arrêt."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Problèmes courants provoquant le recyclage des rôles

Cet article examine certaines des causes courantes entraînant des problèmes de déploiement, et indique des conseils de dépannage pour vous aider à résoudre ces problèmes. Une instance de rôle qui ne démarre pas ou qui est recyclée entre les états Initialisation, Occupée et Arrêt indique un problème au niveau de l’application.

## Contacter le Support technique Azure

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/).

Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez la [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Dépendances d'exécution manquantes

Si un rôle dans votre application repose sur un assembly qui ne fait pas partie du.NET Framework ou de la bibliothèque gérée Azure, vous devez inclure explicitement cet assembly dans le package d'application. N'oubliez pas que les autres infrastructures Microsoft ne sont pas disponibles par défaut dans Azure. Si votre rôle repose sur une telle infrastructure, vous devez ajouter ces assemblys au package d'application.

Avant de générer et de mettre en package votre application, vérifiez les éléments suivants :

- Si vous utilisez Visual studio, assurez-vous que la propriété **Copie locale** est définie sur **True** pour chaque assembly référencé dans votre projet ne faisant pas partie du Kit de développement logiciel (SDK) Azure ni du .NET Framework.

- Assurez-vous que le fichier web.config ne fait pas référence à des assemblys inutilisés dans l’élément compilation.

- La propriété **Build Action** de chaque fichier .cshtml est définie sur **Content**. Cela garantit que les fichiers s’affichent correctement dans le package et permet à d’autres fichiers référencés d’apparaître dans le package.

## L’assembly cible la mauvaise plateforme

Azure est un environnement 64 bits. Par conséquent, les assemblys .NET compilés pour une cible 32 bits ne fonctionneront pas sur Azure.

## Le rôle génère des exceptions non gérées lors de l'initialisation ou de l'arrêt

Les exceptions levées par les méthodes de la classe [RoleEntryPoint], y compris les méthodes [OnStart], [OnStop] et [Run], sont des exceptions non prises en charge. Si une exception non gérée se produit dans une de ces méthodes, le rôle sera recyclé. Si le rôle est recyclé à plusieurs reprises, il peut lever une exception non prise en charge chaque fois qu’il tente de démarrer.

## Le rôle est renvoyé à partir de la méthode Run

La méthode [Run] est destinée à être exécutée indéfiniment. Si votre code remplace la méthode [Run], il devrait être en veille indéfiniment. Si la méthode [Run] est renvoyée, le rôle est recyclé.

## Chaîne DiagnosticsConnectionString incorrecte

Si l’application utilise les diagnostics Azure, votre fichier de configuration de service doit spécifier le paramètre de configuration `DiagnosticsConnectionString`. Ce paramètre doit spécifier une connexion HTTPS à votre compte de stockage dans Azure.

Pour vous assurer que votre paramètre `DiagnosticsConnectionString` est correct avant de déployer votre package d'application dans Azure, vérifiez les éléments suivants :

- Le paramètre `DiagnosticsConnectionString` pointe vers un compte de stockage valide dans Azure. Par défaut, ce paramètre pointe vers le compte de stockage émulé, et vous devez donc modifier explicitement ce paramètre avant de déployer votre package d'application. Si vous ne modifiez pas ce paramètre, une exception est générée lorsque l'instance de rôle tente de démarrer le moniteur de diagnostic. L'instance de rôle risque alors d’être recyclée indéfiniment.

- La chaîne de connexion est spécifiée dans le [format](../storage/storage-configure-connection-string.md) suivant. (Le protocole HTTPS doit être spécifié.) Remplacez *MyAccountName* par le nom de votre compte de stockage et *MyAccountKey* par votre clé d’accès :

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Si vous développez votre application à l’aide d’Azure Tools pour Microsoft Visual Studio, vous pouvez utiliser les [pages de propriétés](https://msdn.microsoft.com/library/ee405486) pour définir cette valeur.

## Le certificat exporté ne contient aucune clé privée

Pour exécuter un rôle web sous SSL, vous devez vous assurer que votre certificat de gestion exporté inclut la clé privée. Si vous utilisez le *gestionnaire de certificats Windows* pour exporter le certificat, veillez à sélectionner **Oui** pour l’option **Exporter la clé privée**. Le certificat doit être exporté au format PFX, l’unique format actuellement pris en charge.

## Étapes suivantes

Affichez plus d’[articles de résolution des problèmes](..\?tag=top-support-issue&service=cloud-services) liés aux services cloud.

Affichez d’autres scénarios de recyclage des rôles dans la [série du blog de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).




[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx

<!---HONumber=AcomDC_0204_2016-->