<properties
   pageTitle="Créer un cluster autonome avec des machines virtuelles Azure Windows | Microsoft Azure"
   description="Découvrez comment créer et gérer un cluster Azure Service Fabric sur des machines virtuelles sous Windows Server."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# Créer un cluster Service Fabric autonome à trois nœuds avec des machines virtuelles Azure sous Windows Server

Cet article décrit comment créer un cluster sur des machines virtuelles Azure Windows, à l’aide du programme d’installation Service Fabric autonome pour Windows Server. Il s’agit d’un cas spécial de [Créer et gérer un cluster sous Windows Server](service-fabric-cluster-creation-for-windows-server.md) où les machines virtuelles sont des [machines virtuelles Azure sous Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), mais vous ne créez pas de [cluster Service Fabric cloud Azure](service-fabric-cluster-creation-via-portal.md). La différence est que le cluster Service Fabric autonome créé par la procédure suivante est entièrement géré par vos soins, alors que les clusters Service Fabric cloud Azure sont gérés et mis à niveau par le fournisseur de ressources Service Fabric.


## Étapes pour créer le cluster autonome

1. Connectez-vous au portail Azure et créez une machine virtuelle Windows Server 2012 R2 Datacenter dans un groupe de ressources. Lisez l’article [Créer une machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour plus d’informations.
2. Ajoutez deux autres machines virtuelles Windows Server 2012 R2 Datacenter au même groupe de ressources. Vérifiez que chaque machine virtuelle a le même nom d’utilisateur d’administration et le même mot de passe lors de la création. Une fois créées, vous devriez voir les trois machines virtuelles sur le même réseau virtuel.
3. Connectez-vous à chacune des machines virtuelles et désactivez le Pare-feu Windows à l’aide du [tableau de bord Gestionnaire de serveur, Serveur Local](https://technet.microsoft.com/library/jj134147.aspx). Cela permet de s’assurer que le trafic réseau peut communiquer entre les machines. Une fois connecté à chaque machine, obtenez l’adresse IP en ouvrant une invite de commandes et en tapant `ipconfig`. Vous pouvez également voir l’adresse IP de chaque machine en sélectionnant la ressource de réseau virtuel du groupe de ressources dans le portail Azure.
4. Connectez-vous à l’une des machines virtuelles et vérifiez que vous pouvez communiquer avec les deux autres à l’aide d’une commande ping.
5. Connectez-vous à l’une des machines virtuelles et [téléchargez le package Service Fabric pour Windows Server autonome](http://go.microsoft.com/fwlink/?LinkId=730690) dans un nouveau dossier sur la machine, puis extrayez le package.
6. Ouvrez le fichier *ClusterConfig.Unsecure.MultiMachine.json* dans le Bloc-notes et modifiez chaque nœud avec les trois adresses IP des machines virtuelles. Modifiez le nom du cluster en haut et enregistrez le fichier. Vous trouverez ci-dessous un exemple partiel du manifeste de cluster.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
        	"metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Ouvrez une [fenêtre PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Accédez au dossier dans lequel vous avez extrait le package du programme d’installation autonome téléchargé et enregistré le fichier de manifeste de cluster. Exécutez la commande PowerShell suivante.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Le script PowerShell doit s’exécuter, se connecter à chaque machine et créer un cluster. Après environ une minute, vous pouvez vérifier que le cluster est opérationnel en vous connectant à Service Fabric Explorer sur l’une des adresses IP de la machine, par exemple avec `http://10.7.0.5:19080/Explorer/index.html`. S’agissant d’un cluster autonome utilisant des machines virtuelles Azure, vous devrez pour le sécuriser [déployer des certificats sur les machines virtuelles Azure](service-fabric-windows-cluster-x509-security.md) ou configurer l’une d’elles comme [contrôleur Windows Server Active Directory (AD) pour l’authentification Windows](service-fabric-windows-cluster-windows-security.md), comme vous le feriez localement.


## Étapes suivantes
- [Créer des clusters Service Fabric autonomes sur un serveur Windows Server ou Linux](service-fabric-deploy-anywhere.md)
- [Ajouter ou supprimer des nœuds d’un cluster Service Fabric autonome](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Paramètres de configuration pour un cluster Windows autonome](service-fabric-cluster-manifest.md)
- [Sécuriser un cluster autonome sur Windows à l’aide de la sécurité Windows](service-fabric-windows-cluster-windows-security.md)
- [Sécuriser un cluster autonome sur Windows à l’aide de certificats X509](service-fabric-windows-cluster-x509-security.md)

<!---HONumber=AcomDC_0810_2016-->