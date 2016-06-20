<properties
   pageTitle="Mettre à jour les certificats pour un cluster Azure | Microsoft Azure"
   description="Décrit comment charger un nouveau certificat dans Azure Key Vault et mettre à jour ou supprimer un certificat pour un cluster Service Fabric existant."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# Ajouter ou supprimer des certificats pour un cluster Service Fabric dans Azure

Service Fabric vous permet de spécifier deux certificats, un principal et un secondaire, lorsque vous configurez la sécurité par certificat lors de la création du cluster. Par défaut, le certificat principal est celui que vous spécifiez lors de la création. Après la création du cluster, vous pouvez ajouter un certificat en tant que certificat secondaire ou supprimer un certificat existant. Pour plus d’informations sur la façon dont Service Fabric utilise les certificats X.509, consultez [Scénarios de sécurité des clusters](service-fabric-cluster-security.md).

>[AZURE.NOTE] Pour un cluster sécurisé, vous avez toujours besoin d’au moins un certificat (principal ou secondaire) valide (non révoqué ni arrivé à expiration) déployé, sinon vous ne pouvez pas accéder au cluster.

## Ajouter un certificat secondaire
Pour ajouter un autre certificat comme certificat secondaire, vous devez charger le certificat dans un coffre de clés Azure, puis le déployer sur les machines virtuelles du cluster. Pour plus d’informations, consultez [Déployer des certificats sur les machines virtuelles à partir de coffres de clés gérés par les clients](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx).

1. [Charger un certificat X.509 dans le coffre de clés](service-fabric-cluster-azure-secure-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)
2. Connectez-vous au [portail Azure](https://portal.azure.com/), puis accédez à la ressource de cluster à laquelle vous voulez ajouter ce certificat.
3. Sous **Paramètres**, cliquez sur le paramètre de certificat et entrez l’empreinte de certificat secondaire.
4. Cliquez sur **Enregistrer**. Un déploiement est lancé et une fois qu’il est terminé, vous pouvez utiliser le certificat principal ou le certificat secondaire pour effectuer des opérations de gestion sur le cluster.

![Capture d’écran des empreintes de certificat dans le portail][SecurityConfigurations_02]

## Supprimer un certificat
Voici le processus de suppression d’un ancien certificat pour que le cluster ne l’utilise plus :

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis accédez aux paramètres de sécurité de votre cluster.
2. Supprimez l’un des certificats.
3. Cliquez sur **Enregistrer**, ce qui démarre un nouveau déploiement. Une fois ce déploiement terminé, le certificat que vous avez supprimé ne peut plus être utilisé pour la connexion au cluster.

## Étapes suivantes
Lisez les articles suivants pour plus d’informations sur la gestion des clusters :

- [Processus de mise à niveau du cluster Service Fabric et attentes à votre égard](service-fabric-cluster-upgrade.md)
- [Configurer l’accès en fonction du rôle pour les clients](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0608_2016-->