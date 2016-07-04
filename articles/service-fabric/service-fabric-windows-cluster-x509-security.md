<properties
   pageTitle="Se connecter à un cluster privé sécurisé | Microsoft Azure"
   description="Cet article explique comment sécuriser les communications au sein du cluster autonome ou privé, ainsi qu’entre les clients et le cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/10/2016"
   ms.author="dkshir"/>

# Sécurisation de votre cluster Windows autonome via des certificats

Cet article explique comment sécuriser les communications entre les différents nœuds de votre cluster Windows autonome, et comment authentifier des clients se connectant à ce cluster à l’aide de certificats X.509. Cela garantit que seuls les utilisateurs autorisés puissent accéder au cluster et aux applications déployées et effectuer des tâches de gestion. La sécurité par certificat doit avoir été activée sur le cluster à sa création. Pour en savoir plus sur la sécurité nœud à nœud, la sécurité client à nœud et le contrôle d’accès en fonction du rôle, voir [Sécuriser un cluster Service Fabric](service-fabric-cluster-security.md).

## Quels sont les certificats requis ?

Pour commencer, [téléchargez le package de cluster autonome](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) sur l’un des nœuds de votre cluster. Dans le package téléchargé, vous trouverez un fichier **ClusterConfig.X509.json**. Ouvrez le fichier en mode *Édition* et consultez la section relative à la **sécurité** sous l’onglet **Propriétés** :

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
			"ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
        }
    },

Cette section décrit tous les certificats dont vous aurez besoin pour la sécurisation de votre cluster Windows autonome. Activez la sécurité basée sur les certificats en définissant les valeurs des paramètres **ClusterCredentialType** et **ServerCredentialType** sur *X509*.

>[AZURE.NOTE] Un [Thumbprint](https://en.wikipedia.org/wiki/Public_key_fingerprint) est l’identité principale d’un certificat. Lisez la section [Comment récupérer l’empreinte numérique d’un certificat](https://msdn.microsoft.com/library/ms734695.aspx) pour déterminer le Thumbprint des certificats que vous créez.

Le tableau suivant répertorie les certificats réels dont vous aurez besoin pour la configuration de votre cluster :

|**Paramètre CertificateInformation**|**Description**|
|-----------------------|--------------------------|
|ClusterCertificate|Ce certificat est requis pour sécuriser les communications entre les nœuds sur un cluster. Vous pouvez utiliser deux certificats différents : un certificat principal et un certificat secondaire pour le basculement. Définissez le Thumbprint du certificat principal dans la section **Thumbprint**, et celui du certificat secondaire, dans les variables **ThumbprintSecondary**.|
|ServerCertificate|Ce certificat est présenté au client lorsqu’il tente de se connecter à ce cluster. Pour plus de commodité, vous pouvez choisir d’utiliser le même certificat pour les éléments *ClusterCertificate* et *ServerCertificate*. Vous pouvez utiliser deux certificats de serveur différents : un certificat principal et un certificat secondaire pour le basculement. Définissez le Thumbprint du certificat principal dans la section **Thumbprint**, et celui du certificat secondaire, dans les variables **ThumbprintSecondary**. |
|ClientCertificateThumbprints|Il s’agit du jeu de certificats que vous souhaitez installer sur les clients authentifiés. Il peut y avoir un certain nombre de certificats clients installés sur les machines qui seront autorisées à accéder au cluster, ainsi qu’aux applications qui s’exécutent sur ce dernier. Définissez le Thumbprint de chaque certificat dans la variable **CertificateThumbprint**. Si vous définissez le paramètre **IsAdmin** sur *true*, le client sur lequel ce certificat est installé peut effectuer diverses activités de gestion du cluster. Si le paramètre **IsAdmin** a la valeur *false*, il peut uniquement accéder aux applications exécutées sur le cluster.|
|ClientCertificateCommonNames|Définissez le nom commun du premier certificat client pour **CertificateCommonName**. L’élément **CertificateIssuerThumbprint** est le Thumbprint relatif à l’émetteur de ce certificat. Lisez la section [Utilisation des certificats](https://msdn.microsoft.com/library/ms731899.aspx) pour en savoir plus sur les noms communs et l’émetteur.|


## Installer des certificats

Pour sécuriser les communications entre le cluster, vous devez d’abord obtenir des certificats X.509 pour vos nœuds de cluster. En outre, pour limiter les connexions à ce cluster aux ordinateurs/utilisateurs autorisés, vous devez obtenir et installer des certificats pour ces ordinateurs clients potentiels. Lisez la section [Comment obtenir un certificat (WCF)](https://msdn.microsoft.com/library/aa702761.aspx) pour savoir comment obtenir des certificats X.509. Vous devez créer un certificat au format **.pfx** afin de pouvoir stocker votre clé privée. Si vous disposez d’un abonnement Azure, vous pouvez également suivre les instructions de la section [Acquire the X.509 certificates (Acquérir des certificats X.509)](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts) pour créer vos certificats. Une fois que vous disposez de ce certificat, vous pouvez l’installer sur les nœuds de cluster à l’aide de la procédure suivante. Remarque : ces étapes partent du principe que la version la plus récente de Windows PowerShell 3.x est installée sur vos nœuds. Vous devrez répéter cette procédure sur chaque nœud, pour les certificats de cluster et de serveur et pour chaque certificat secondaire éventuel, sur chaque système.

- Copiez le fichier .pfx file sur le nœud.

- Ouvrez une fenêtre PowerShell en tant qu’administrateur, puis saisissez les commandes suivantes :
	
		Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My `
		-FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $password -AsPlainText -Force)

Remplacez l’élément *$password* par le mot de passe que vous avez utilisé pour créer ce certificat. Remplacez l’élément $PfxFilePath par le chemin d’accès complet du fichier .pfx copié sur ce nœud.

- Définissez le contrôle d’accès sur ce certificat afin que le processus Service Fabric puisse l’utiliser, en exécutant le script suivant :

		$cert = get-item Cert:\LocalMachine\My<CertificateThumbprint>
		$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

		#Note down the string output by this command, this is the container name for your private key. 

		$privateKeyFilePath = 'C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys<PrivateKeyContainerName>'
	
		#Observe the access rights currently assigned to this certificate.
		get-acl $privateKeyFilePath | fl

		#Set the ACL so that the Service Fabric process can access it.
		$acl = get-acl $privateKeyFilePath
		$acl.SetAccessRule((New-Object System.Security.Accesscontrol.FileSystemAccessRule("NT AUTHORITY\NetworkService","FullControl","Allow")))
		Set-Acl $privateKeyFilePath $acl -ErrorAction Stop 
	

Cette procédure vous permet également d’installer les certificats clients sur les machines qui seront autorisées à accéder au cluster.


## Étapes suivantes

Après avoir configuré la section relative à la **sécurité** du fichier ClusterConfig.X509.json, vous pouvez passer à la section [Création de votre cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) pour configurer les nœuds et créer le cluster autonome. N’oubliez pas d’utiliser le fichier **ClusterConfig.X509.json** lors de la création du cluster. Voici un exemple de commande :

	cd $ServiceFabricDeployAnywhereFolder
	.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true -Verbose


Lorsque le cluster Windows autonome sécurisé s’exécute correctement et que vous avez configuré les clients authentifiés pour qu’ils s’y connectent, suivez les instructions de la section [Se connecter à un cluster sécurisé avec PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) pour vous y connecter.

<!---HONumber=AcomDC_0622_2016-->