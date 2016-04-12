<properties
   pageTitle="Sécuriser les communications pour les services dans Service Fabric | Microsoft Azure"
   description="Vue d’ensemble de la sécurisation des communications pour les services fiables qui s’exécutent dans un cluster Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/22/2016"
   ms.author="punewa"/>

# Sécuriser les communications pour les services dans Service Fabric
La sécurité est un des aspects les plus importants de la communication. L’infrastructure d’application Reliable Services fournit quelques piles et outils de communication prédéfinis. Cet article vous présente comment configurer la sécurité lors de l’utilisation de l’accès à distance au service et de la pile de communication WCF.

## Sécurisation du service lors de l’utilisation de l’accès distant au service
La configuration de l’accès à distance sécurisé pour un service s’effectue de la manière suivante :

1. Nous allons utiliser un précédent [exemple](service-fabric-reliable-services-communication-remoting.md) qui traite de la configuration d’un accès à distance fiable au service. Nous commençons par une interface `IHelloWorldStateful` qui définit les méthodes disponibles pour l’appel de procédure distante sur votre service. Votre service va utiliser un `FabricTransportServiceRemotingListener` qui est déclaré dans l’espace de noms `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime`. Il s'agit d'une implémentation `ICommunicationListener` qui fournit des fonctionnalités de communication à distance.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Ajout des paramètres de l’écouteur et des informations d’identification de sécurité

    Le certificat que vous souhaitez utiliser pour sécuriser les communications de votre service doit être installé sur tous les nœuds du cluster. Vous pouvez fournir les paramètres de l’écouteur et les informations d’identification de sécurité de deux manières.

    1. Directement dans le code de service.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listnerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listnerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. À l’aide du [package de configuration](service-fabric-application-model.md)

        Ajoutez une section `TransportSettings` dans le fichier settings.xml

        ```xml
        <!--Section name should always end with "TransportSettings"-->
        <!--Here we are using a prefix "HelloWorldStateful"-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        Dans ce cas, la méthode `CreateServiceReplicaListeners` se présente comme suit.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Si vous ajoutez une section `TransportSettings` dans le fichier settings.xml sans aucun préfixe, `FabricTransportListenerSettings` charge par défaut tous les paramètres de cette section.

         ```xml
         <!--"TransportSettings" section without any prefix-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         Dans ce cas, la méthode `CreateServiceReplicaListeners` se présente comme suit.

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Lors de l’appel des méthodes sur un service sécurisé à l’aide de la pile de communication à distance, au lieu d’utiliser la classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` pour créer un proxy de service, nous utilisons `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` et transmettons le `FabricTransportSettings` qui contient les `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si le code client s’exécute dans le cadre d’un service, vous pouvez charger le `FabricTransportSettings` à partir du fichier settings.xml. Créez une section TransportSettings similaire au code de service comme illustré ci-dessus. Apportez les modifications suivantes au code du client.

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Si le client n’est pas exécuté dans le cadre d’un service, vous pouvez créer un fichier client\_name.settings.xml dans le même emplacement que le fichier client\_name.exe et créer une section TransportSettings dans ce fichier.

    Comme pour le service, si vous ajoutez une section `TransportSettings` sans *préfixe* dans le fichier client settings.xml/client\_name.settings.xml, `FabricTransportSettings` charge par défaut tous les paramètres à partir de cette section.

    Dans ce cas, le code ci-dessus est encore davantage simplifié.

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## Sécurisation du service lors de l’utilisation de la pile de communication WCF

Nous allons utiliser un précédent [exemple](service-fabric-reliable-services-communication-wcf.md) qui explique comment configurer une communication WCF pour les services fiables et l’étendre pour assurer sa sécurité.

1. Pour le service, nous devons créer un `WcfCommunicationListener` qui est sécurisé. Pour cela, nous devons modifier la méthode `CreateServiceReplicaListeners`.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListner = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example we will be using NetTcpBinding
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the servicehost credentials.
        wcfCommunicationListner.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListner;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
    
2. Dans le client, la classe `WcfCommunicationClient` que nous avons créée dans l’[exemple](service-fabric-reliable-services-communication-wcf.md) précédent reste inchangée. Mais nous devons remplacer la méthode `CreateClientAsync` de `WcfCommunicationClientFactory`.

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by the
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Utilisez le `SecureWcfCommunicationClientFactory` pour créer un `WcfCommunicationClient`. Utilisez le client pour appeler les méthodes de service.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## Étapes suivantes

* [API Web avec OWIN dans Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0330_2016-->