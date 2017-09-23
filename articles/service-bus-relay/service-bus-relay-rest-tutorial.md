---
title: Didacticiel Service Bus REST utilisant Azure Relay | Microsoft Docs
description: "Créez une simple application hôte Azure Service Bus Relay présentant une interface de type REST."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 0db9dbd2d2743907e3f0b259228201d4f5d0c3c2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017

---
# <a name="azure-wcf-relay-rest-tutorial"></a>Didacticiel Azure WCF Relay REST

Ce didacticiel explique comment créer une simple application hôte Azure Relay qui expose une interface de type REST. REST permet à un client web, par exemple un navigateur web, d’accéder aux API Service Bus via des requêtes HTTP.

Le didacticiel utilise le modèle de programmation REST Windows Communication Foundation (WCF) pour construire un service REST sur Service Bus. Pour plus d’informations, consultez les rubriques [Modèle de programmation REST WCF](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) et [Conception et implémentation de services](/dotnet/framework/wcf/designing-and-implementing-services) dans la documentation WCF.

## <a name="step-1-create-a-namespace"></a>Étape 1 : Création d’un espace de noms

Pour commencer à utiliser les fonctionnalités de relais dans Azure, vous devez d’abord créer un espace de noms de service. Un espace de noms fournit un conteneur d’étendue pour l’adressage des ressources Azure au sein de votre application. Suivez les [instructions fournies ici](relay-create-namespace-portal.md) pour créer un espace de noms Relay.

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Étape 2 : Définition d’un contrat de service REST WCF à utiliser avec Azure Relay

Lorsque vous créez un service de type REST WCF, vous devez définir le contrat. Le contrat spécifie les opérations prises en charge par l'hôte. Une opération de service peut être considérée comme une méthode de service web. Les contrats sont créés en définissant une interface C++, C# ou Visual Basic. Chaque méthode dans l'interface correspond à une opération de service spécifique. L’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) doit être appliqué à chaque interface et l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) doit être appliqué à chaque opération. Si une méthode dans une interface qui contient l’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) n’a pas l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), cette méthode n’est pas exposée. Le code utilisé pour effectuer ces tâches est indiqué dans l'exemple suivant la procédure.

La principale différence entre un contrat WCF et un contrat de type REST est l’ajout d’une propriété à l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) : [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Cette propriété vous permet de mapper une méthode dans votre interface à une méthode de l'autre côté de l'interface. Dans ce cas, nous utiliserons [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) pour associer une méthode à HTTP GET. Cela permet à Service Bus de récupérer et d’interpréter correctement les commandes envoyées à l'interface.

### <a name="to-create-a-contract-with-an-interface"></a>Création d’un contrat avec une interface

1. Ouvrez Visual Studio en tant qu’administrateur : cliquez avec le bouton droit sur le programme dans le menu **Démarrer**, puis cliquez sur **Exécuter en tant qu’administrateur**.
2. Créez un projet d’application de console. Cliquez sur le menu **Fichier**, sélectionnez **Nouveau**, puis **Projet**. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Visual C#**, sélectionnez le modèle **Application console** et nommez-le **ImageListener**. Utilisez **l’emplacement** par défaut. Cliquez sur **OK** pour créer le projet.
3. Pour un projet C#, Visual Studio crée un fichier `Program.cs`. Cette classe contient une méthode `Main()` vide, requise pour créer correctement un projet d’application console.
4. Ajoutez des références à Service Bus et **System.ServiceModel.dll** au projet en installant le package NuGet Service Bus. Ce package ajoute automatiquement des références aux bibliothèques Service Bus, ainsi qu’au WCF **System.ServiceModel**. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ImageListener**, puis cliquez sur **Gérer les packages NuGet**. Cliquez sur l’onglet **Parcourir**, puis recherchez `Microsoft Azure Service Bus`. Cliquez sur **Installer**et acceptez les conditions d’utilisation.
5. Vous devez explicitement ajouter une référence à **System.ServiceModel.Web.dll** au projet :
   
    a. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le dossier **Références**, puis cliquez sur **Ajouter une référence**.
   
    b. Dans la boîte de dialogue **Ajouter une référence**, cliquez sur l’onglet **Framework** sur le côté gauche puis, dans la zone **Rechercher**, tapez **System.ServiceModel.Web**. Cochez la case **System.ServiceModel.Web**, puis cliquez sur **OK**.
6. Ajoutez les instructions `using` suivantes en haut du fichier Program.cs.
   
    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```
   
    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) est l’espace de noms qui permet l’accès par programme aux fonctionnalités WCF de base. WCF Relay utilise la plupart des objets et attributs de WCF pour définir des contrats de service. Vous utiliserez cet espace de noms dans la plupart de vos applications de relais. De même, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) permet de définir le canal, qui est l’objet via lequel vous communiquez avec Azure Relay et le navigateur web client. Enfin, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contient les types qui vous permettent de créer des applications web.
7. Renommez l’espace de noms `ImageListener` en **Microsoft.ServiceBus.Samples**.
   
    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```
8. Directement après l’accolade d’ouverture de la déclaration d’espace de noms, définissez une nouvelle interface nommée **IImageContract** et appliquez l’attribut **ServiceContractAttribute** à l’interface avec une valeur de `http://samples.microsoft.com/ServiceModel/Relay/`. La valeur de l'espace de noms diffère de l'espace de noms que vous utilisez dans l’ensemble de votre code. La valeur de l'espace de noms est utilisée comme identificateur unique pour ce contrat et doit contenir des informations de version. Pour plus d’informations, consultez la rubrique [Contrôle de version du service](http://go.microsoft.com/fwlink/?LinkID=180498). Spécifier explicitement l'espace de noms empêche l'ajout au nom du contrat de la valeur d'espace de noms par défaut.
   
    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```
9. Dans l’interface `IImageContract`, déclarez une méthode pour une seule opération que le contrat `IImageContract` expose dans l’interface, puis appliquez l’attribut `OperationContractAttribute` à la méthode que vous souhaitez exposer dans le cadre du contrat Service Bus public.
   
    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```
10. En regard de l’attribut **OperationContract**, ajoutez la valeur **WebGet**.
    
    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```
    
    Cette opération permet au service de relais d’acheminer les demandes HTTP GET vers `GetImage`, et de convertir les valeurs `GetImage` renvoyées dans une réponse HTTP GETRESPONSE. Plus loin dans ce didacticiel, vous utiliserez un navigateur web pour accéder à cette méthode et pour afficher l'image dans le navigateur.
11. Directement après la définition de `IImageContract`, déclarez un canal qui hérite à la fois des interfaces `IImageContract` et `IClientChannel` :
    
    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```
    
    Un canal est l'objet WCF par le biais duquel le service et le client se transmettent des informations. Plus tard, vous créerez le canal dans votre application hôte. Azure Relay utilise ensuite ce canal pour transmettre les requêtes HTTP GET du navigateur vers votre implémentation **GetImage**. Le relais utilise également ce canal pour extraire la valeur **GetImage** renvoyée et la traduire en une HTTP GETRESPONSE pour le navigateur client.
12. Dans le menu **Générer**, cliquez sur **Générer la solution** pour confirmer que votre travail est correct.

### <a name="example"></a>Exemple
Le code suivant montre une interface de base qui définit un contrat WCF Relay.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Étape 3 : Implémentation d’un contrat de service REST WCF à utiliser avec Service Bus
La création d’un service WCF Relay de type REST nécessite la création au préalable du contrat défini à l’aide d’une interface. L'étape suivante consiste à implémenter l'interface. Cela implique la création d’une classe nommée **ImageService** qui implémente l’interface **IImageContract** définie par l’utilisateur. Une fois le contrat implémenté, vous configurez ensuite l'interface à l'aide d'un fichier App.config. Le fichier de configuration contient les informations nécessaires à l’application, notamment le nom du service, le nom du contrat et le type de protocole utilisé pour communiquer avec le service de relais. Le code utilisé pour effectuer ces tâches est fourni dans l'exemple suivant la procédure.

Comme pour les étapes précédentes, il y a très peu de différences entre l’implémentation d’un contrat de type REST et un contrat WCF Relay.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implémentation d’un contrat Service Bus de type REST 
1. Créez une classe nommée **ImageService** directement après la définition de l’interface **IImageContract**. La classe **ImageService** implémente l’interface **IImageContract**.
   
    ```csharp
    class ImageService : IImageContract
    {
    }
    ```
    Comme pour d'autres implémentations d'interface, vous pouvez implémenter la définition dans un autre fichier. Toutefois, pour ce didacticiel, l’implémentation apparaît dans le même fichier que la définition d’interface et la méthode `Main()`.
2. Appliquez l’attribut [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à la classe **IImageService** pour indiquer que la classe est une implémentation d’un contrat WCF.
   
    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```
   
    Comme mentionné précédemment, cet espace de noms n'est pas un espace de noms standard. Il fait en effet partie de l'architecture WCF qui identifie le contrat. Pour plus d’informations, consultez la rubrique [Noms des contrats de données](https://msdn.microsoft.com/library/ms731045.aspx) dans la documentation WCF.
3. Ajoutez une image .jpg à votre projet.  
   
    Il s'agit d'une image que le service affiche dans le navigateur de réception. Cliquez avec le bouton droit sur votre projet, puis cliquez sur **Ajouter**. Cliquez ensuite sur **Élément existant**. Utilisez la boîte de dialogue **Ajouter un élément existant** pour accéder à un fichier .jpg approprié, puis cliquez sur **Ajouter**.
   
    Lorsque vous ajoutez le fichier, assurez-vous que l’option **Tous les fichiers** est sélectionnée dans la liste déroulante en regard du champ **Nom de fichier :**. Le reste de ce didacticiel suppose que le nom de l'image est « image.jpg ». Si vous avez un fichier différent, vous devrez renommer l'image ou modifier votre code pour compenser.
4. Pour vous assurer que le service en cours d’exécution est capable de trouver le fichier image, cliquez avec le bouton droit sur le fichier image dans **l’Explorateur de solutions**, puis cliquez sur **Propriétés**. Dans le volet **Propriétés**, définissez la valeur **Copier dans le répertoire de sortie** sur **Copier si plus récent**.
5. Ajoutez une référence à l’assembly **System.Drawing.dll** au projet et ajoutez également les instructions `using` associées suivantes.  
   
    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```
6. Dans la classe **ImageService**, ajoutez le constructeur suivant qui charge le bitmap et prépare son envoi au navigateur client.
   
    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```
7. Directement après le code précédent, ajoutez la méthode **GetImage** suivante à la classe **ImageService** afin de retourner un message HTTP contenant l’image.
   
    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```
   
    Cette implémentation utilise **MemoryStream** pour récupérer l’image et la préparer pour la diffusion en continu vers le navigateur. La diffusion en continu commence à la position zéro, déclare le contenu du flux au format jpeg et diffuse l'information.
8. Dans le menu **Générer**, cliquez sur **Générer la solution**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Définition de la configuration pour l’exécution du service web sur Service Bus
1. Dans **l’Explorateur de solutions**, double-cliquez sur le fichier **App.config** pour l’ouvrir dans l’éditeur Visual Studio.
   
    Le fichier **App.config** contient le nom du service, le point de terminaison (c’est-à-dire l’emplacement qu’Azure Relay expose aux clients et aux ordinateurs hôtes afin qu’ils communiquent entre eux) et la liaison (type de protocole utilisé pour communiquer). La principale différence réside dans le fait que le point de terminaison de service configuré fait référence à une liaison [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding).
2. L’élément XML `<system.serviceModel>` est un élément WCF qui définit un ou plusieurs services. Ici, il sert à définir le nom du service et le point de terminaison. En bas de l’élément `<system.serviceModel>` (mais toujours au sein de `<system.serviceModel>`), ajoutez un élément `<bindings>` avec le contenu suivant. Cela définit les liaisons utilisées dans l'application. Vous pouvez définir plusieurs liaisons, mais pour ce didacticiel, vous n’en définissez qu'une seule.
   
    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```
   
    Le code précédent définit une liaison WCF Relay [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) avec **relayClientAuthenticationType** défini sur **Aucun**. Ce paramètre indique qu'un point de terminaison utilisant cette liaison ne nécessite aucune information d'identification du client.
3. Après l’élément `<bindings>`, ajoutez un élément `<services>`. Comme pour les liaisons, vous pouvez définir plusieurs services dans un même fichier de configuration. Toutefois, pour ce didacticiel, vous n’en définissez qu'un seul.
   
    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```
   
    Cette étape configure un service qui utilise la valeur par défaut **webHttpRelayBinding** définie précédemment. Il utilise également la valeur par défaut **sbTokenProvider**, définie dans l’étape suivante.
4. Après l’élément `<services>`, créez un élément `<behaviors>` avec le contenu suivant, en remplaçant « SAS_KEY » par la clé avec *signature d’accès partagé* (SAP) obtenue précédemment à partir du [portail Azure][Azure portal].
   
    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```
5. Toujours dans le fichier App.config, dans l’élément `<appSettings>`, remplacez la valeur de la chaîne de connexion entière par la chaîne de connexion obtenue précédemment à partir du portail. 
   
    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```
6. Dans le menu **Générer**, cliquez sur **Générer la solution** pour générer la solution complète.

### <a name="example"></a>Exemple
Le code suivant montre l’implémentation du contrat et du service pour un service REST qui s’exécute sur Service Bus à l’aide de la liaison **WebHttpRelayBinding**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

L'exemple suivant montre le fichier App.config associé au service.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey="YOUR_SAS_KEY"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-azure-relay"></a>Étape 4 : Hébergement du service WCF REST pour utiliser Azure Relay
Cette étape décrit comment exécuter un service web à l’aide d’une application console avec WCF Relay. Une liste complète du code écrit dans cette étape est fournie dans l'exemple suivant la procédure.

### <a name="to-create-a-base-address-for-the-service"></a>Création d’une adresse de base pour le service
1. Dans la déclaration de fonction `Main()`, créez une variable pour stocker l’espace de noms de votre projet. Assurez-vous de remplacer `yourNamespace` par le nom de l’espace de noms de service Relay créé précédemment.
   
    ```csharp
    string serviceNamespace = "yourNamespace";
    ```
    Service Bus utilise le nom de votre espace de noms pour créer une adresse URI unique.
2. Créez une instance `Uri` pour l’adresse de base du service basé sur l’espace de noms.
   
    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Création et configuration de l'hôte de service web
* Créez l'hôte de service web en utilisant l'adresse URI créée précédemment dans cette section.
  
    ```csharp
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    L'hôte de service est l'objet WCF qui instancie l'application hôte. Cet exemple transmet le type d’hôte que vous souhaitez créer (un élément **ImageService**) ainsi que l’adresse à laquelle vous souhaitez exposer l’application hôte.

### <a name="to-run-the-web-service-host"></a>Exécution de l'hôte de service web
1. Ouvrez le service.
   
    ```csharp
    host.Open();
    ```
    Le service est maintenant actif.
2. Affichez un message indiquant que le service est en cours d'exécution et expliquant comment arrêter ce service.
   
    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```
3. Lorsque vous avez terminé, fermez l'hôte de service.
   
    ```csharp
    host.Close();
    ```

## <a name="example"></a>Exemple
L'exemple suivant inclut le contrat de service et l'implémentation des étapes précédentes du didacticiel et héberge le service dans une application console. Compilez le code suivant dans un fichier exécutable nommé ImageListener.exe.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Compilation du code
Après avoir créé la solution, procédez comme suit pour exécuter l'application :

1. Appuyez sur **F5** ou accédez à l’emplacement du fichier exécutable (ImageListener\bin\Debug\ImageListener.exe), pour exécuter le service. Ne fermez pas l’application en cours d’exécution, car l’étape suivante est obligatoire.
2. Copiez et collez l'adresse à partir de l'invite de commande dans un navigateur pour afficher l'image.
3. Une fois que vous avez terminé, appuyez sur **Entrée** dans la fenêtre d’invite de commande pour fermer l’application.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé une application qui utilise le service de relais Service Bus, consultez les articles suivants pour en savoir plus sur Azure Relay :

* [Azure Service Bus](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Vue d’ensemble d’Azure Relay](relay-what-is-it.md)
* [Guide pratique pour utiliser le service WCF Relay avec .NET](relay-wcf-dotnet-get-started.md)

[Azure portal]: https://portal.azure.com

