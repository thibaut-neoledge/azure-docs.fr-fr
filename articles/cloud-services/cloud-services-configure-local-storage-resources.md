<properties
pageTitle="Configuration des ressources de stockage local dans les services cloud Azure"
description=""
services="cloud-services"
documentationCenter=""
authors="cristy"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="06/11/2015"
ms.author="cristyg"/>

# Configuration des ressources de stockage local

Une ressource de stockage local est un répertoire réservé dans le système de fichiers de la machine virtuelle dans lequel s’exécute l’instance d’un rôle. Vous pouvez stocker des informations dans votre instance de machine virtuelle afin que le code qui s'exécute dans l'instance puisse accéder à la ressource de stockage local lorsqu'il a besoin d'écrire dans un fichier ou de lire un fichier. Par exemple, une ressource de stockage local peut être utilisée pour mettre en cache les données devant être accessibles de nouveau pendant que le service s'exécute dans Azure. Vous pouvez également configurer une ressource de stockage local pour stocker des fichiers au démarrage. Pour plus d'informations sur la configuration des ressources de stockage local pour le démarrage, consultez [Utilisation du stockage local pour stocker des fichiers au démarrage](https://msdn.microsoft.com/library/azure/hh974419.aspx).

Une ressource de stockage local est déclarée dans le fichier de définition de service. Vous pouvez déclarer plusieurs ressources de stockage local pour un rôle. Chaque ressource de stockage local est réservée pour chaque instance de ce rôle. La quantité minimale d'espace disque que vous pouvez allouer à une ressource de stockage local est de 1 Mo. La quantité maximale que vous pouvez allouer pour une ressource locale donnée dépend de la taille de la machine virtuelle spécifiée pour le rôle. La taille de chaque machine virtuelle a une allocation de stockage total correspondante et l'espace total alloué pour toutes les ressources de stockage local déclarées pour un rôle ne peut pas dépasser la taille maximale allouée pour cette machine virtuelle. Pour plus d'informations sur la quantité maximale d'espace disque local alloué pour chaque taille de machine virtuelle, consultez [Configuration des tailles pour les services cloud](https://msdn.microsoft.com/library/azure/ee814754.aspx).

> [AZURE.NOTE]
>
-   N'oubliez pas qu'il est de la responsabilité du développeur de s'assurer que la quantité d'espace disque nécessaire à une ressource de stockage local ne dépasse pas la quantité maximale allouée pour une machine virtuelle. Si vous configurez une ressource de stockage local pour qu'elle soit supérieure au maximum autorisé, aucune erreur ne se produira jusqu'à ce que vous tentiez d'exécuter une opération d'écriture qui dépasse le maximum autorisé. Dans ce cas, l'opération d'écriture échouera et un message d'erreur d'espace disque insuffisant s'affichera. Le modèle de traitement pour Azure est tentative/échec. Si vous recevez une erreur d'espace disque insuffisant, vous pouvez gérer l'erreur et libérer de l'espace disque. Vous pouvez ensuite retenter l'opération d'écriture.
-   Vous pouvez spécifier qu'une ressource de stockage local soit conservée lorsqu'une instance est recyclée. Toutefois, les données qui sont enregistrées dans le système de fichiers local de la machine virtuelle ne sont pas garanties comme durables. Si votre rôle nécessite des données durables, il est recommandé d'utiliser un lecteur Azure pour stocker les données de fichier. Les lecteurs Azure sont soutenus par le service Blob Azure, donc ils sont assurément durables.  
>


## Ajout d'une ressource de stockage local

Pour déclarer une ressource de stockage local dans le fichier de définition de service, ajoutez l'élément **LocalResources** comme enfant d'un élément **WebRole** ou d'un élément **WorkerRole**. Ajoutez ensuite un élément **LocalStorage** pour représenter la ressource. L'élément **LocalStorage** prend trois attributs :

-   *name*
-   *sizeInMB* : spécifie la taille souhaitée pour cette ressource de stockage local
-   *cleanOnRoleRecycle* : spécifie si la ressource de stockage local doit être effacée quand une instance de rôle est recyclée, ou si elle doit être persistante dans le cycle de vie du rôle. La valeur par défaut est **true**.

Le fichier de définition de service suivant illustre deux ressources de stockage local déclarées pour un rôle web :

	<?xml version="1.0" encoding="utf-8"?>
    <ServiceDefinition xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" name="MyService">
      <WebRole name="MyService_WebRole" vmsize="Medium">
        <InputEndpoints>
          <InputEndpoint name="HttpIn" port="80" protocol="http" />
        </InputEndpoints>
        <ConfigurationSettings>
          <Setting name="SimpleConfigSetting" />
        </ConfigurationSettings>
        <LocalResources>
          <LocalStorage name="localStoreOne" sizeInMB="10" />
          <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
        </LocalResources>
      </WebRole>
    </ServiceDefinition>

Pour plus d'informations sur le fichier de définition de service, consultez [Schéma de définition du service Azure (fichier .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx).

> [AZURE.NOTE]Si vous utilisez Azure Tools pour Microsoft Visual Studio, vous pouvez définir une ressource de stockage local dans les pages **Propriétés** du rôle. Pour plus d'informations, consultez [Configuration de l'application Azure avec Visual Studio](https://msdn.microsoft.com/library/ee405486.aspx).

## Accès par programme à une ressource de stockage local

Pour accéder à la ressource de stockage local, l'application doit extraire le chemin d'accès à partir de la méthode [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Vous pouvez utiliser les opérations de lecture et d'écriture de fichier standard pour lire et écrire le contenu de la ressource de stockage local. L'exemple suivant indique comment lire le contenu d'un fichier nommé **MyTest.txt** à partir de la ressource de stockage local et comment l'afficher sur la page d'accueil d'une application MVC 3 :

    using Microsoft.WindowsAzure.ServiceRuntime;
    using System;
    using System.Text;
    using System.Web.Mvc;

    namespace StartupExercise.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                string SlsPath = RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

                string s = System.IO.File.ReadAllText(SlsPath + "\\MyTest.txt");

                ViewBag.Message = "Contents of MyTest.txt = " + s;

                return View();
            }
        }
    }

## Accès à une ressource de stockage local lors de l'exécution

La bibliothèque gérée Azure fournit des classes pour accéder à la ressource de stockage local à partir du code qui s'exécute dans une instance de rôle. La méthode [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) retourne une référence à un objet [LocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.aspx) nommé.

Étant donné que l'objet **LocalResource** représente un répertoire, vous pouvez le lire et y écrire des informations à l'aide des classes E/S de fichier .NET standard. Pour déterminer le chemin d'accès au répertoire de la ressource de stockage local, utilisez la propriété [LocalResource.RootPath](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.localresource.rootpath.aspx). Cette propriété retourne le chemin d'accès complet à la ressource de stockage local, y compris le répertoire de ressource nommé. Par exemple, si votre service s'exécute dans l'environnement de développement, la ressource de stockage local est définie dans votre système de fichiers local et la propriété **RootPath** retourne une valeur semblable à ce qui suit :


    C:\Users\myaccount\AppData\Local\dftmp\s0\deployment(1)\res\deployment(1).MyService.MyService_WebRole.0\directory\localStoreOne\

Lorsque votre service est déployé sur Azure, le chemin d'accès à la ressource de stockage local inclut l'ID de déploiement et la propriété **RootPath** retourne une valeur semblable à ce qui suit :


    C:\Resources\directory\f335471d5a5845aaa4e66d0359e69066.MyService_WebRole.localStoreOne\

Le code s'exécutant dans une instance de rôle peut accéder à une ressource de stockage local qui est définie pour ce rôle à partir du moment où l'instance est en ligne jusqu'au moment où elle est placée hors connexion.

## Étapes suivantes

- [Configuration d'un service cloud pour Azure](https://msdn.microsoft.com/library/azure/hh124108.aspx)

<!---HONumber=August15_HO7-->