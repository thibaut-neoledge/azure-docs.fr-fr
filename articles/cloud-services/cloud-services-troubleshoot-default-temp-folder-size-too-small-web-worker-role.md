<properties
   pageTitle="La taille du dossier TEMP par défaut est trop petite pour un rôle | Microsoft Azure"
   description="Un rôle de service cloud offre un espace limité pour le dossier TEMP. Cet article fournit quelques suggestions pour éviter de manquer d'espace."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="05/26/2016"
   ms.author="v-six" />

# La taille par défaut du dossier TEMP est trop petite pour un rôle web/de travail de service cloud

Le répertoire temporaire par défaut d'un rôle web ou de travail de service cloud a une taille maximale de 100 Mo, et risque d’être saturé à un moment donné. Cet article décrit comment éviter de manquer d'espace sur le répertoire temporaire.

>[AZURE.NOTE] Cela s’applique uniquement aux rôles web et de travail dans le Kit de développement logiciel (SDK) Azure 1.0 à 1.4.

## Contacter le Support technique Azure

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/).

Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](http://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Pourquoi l’espace devient-il insuffisant ?

Les variables d’environnement Windows standard TEMP et TMP sont accessibles au code exécuté dans votre application. TEMP et TMP pointent toutes les deux vers un répertoire unique d’une taille maximale de 100 Mo. Les données stockées dans ce répertoire ne sont pas conservées tout au long du cycle de vie du service cloud ; si les instances de rôle d’un service cloud sont recyclées, le répertoire est nettoyé.

## Suggestion pour corriger le problème

Implémentez l'une des alternatives suivantes :

- Configurez une ressource de stockage local et accédez-y directement au lieu d’utiliser TEMP ou TMP. Pour accéder à une ressource de stockage local à partir du code exécuté dans votre application, appelez la méthode [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Pour plus d’informations sur la configuration des ressources de stockage local, consultez [Configurer les ressources de stockage local](cloud-services-configure-local-storage-resources.md).

- Configurez une ressource de stockage local et pointez les répertoires TEMP et TMP vers le chemin d'accès de la ressource de stockage local. Cette modification doit être effectuée dans la méthode [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

L'exemple de code suivant montre comment modifier les répertoires cible TEMP et TMP dans la méthode OnStart :


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## Étapes suivantes

Lisez un blog expliquant [comment augmenter la taille du dossier temporaire ASP.NET du rôle web Azure](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Affichez plus d’[articles de résolution des problèmes](..\?tag=top-support-issue&service=cloud-services) liés aux services cloud.

Pour découvrir comment résoudre les problèmes de rôles de service cloud à l’aide des données de diagnostic de calcul PaaS Azure, consultez la [série de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0601_2016-->