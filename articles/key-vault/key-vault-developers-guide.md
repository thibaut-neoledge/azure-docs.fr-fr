<properties
   pageTitle="Guide du développeur de coffre de clés | Microsoft Azure"
   description="Les développeurs peuvent utiliser le coffre de clés Azure pour gérer les clés de chiffrement dans l'environnement Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="1/19/2016"
   ms.author="bruceper" />

# Guide du développeur de coffre de clés Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

En tant que développeur, vous pouvez utiliser le coffre de clés Azure pour gérer les clés de chiffrement dans l'environnement Microsoft Azure. Le coffre de clés prend en charge plusieurs types de clés et algorithmes et peut être utilisé avec des modules de sécurité matériel (HSM) pour les clés de valeur. En outre, vous pouvez utiliser le coffre de clés pour stocker en toute sécurité les secrets qui sont des objets avec un nombre d'octets limité avec aucune sémantique spécifique. Le contrôle d'accès pour les types d'objets est géré indépendamment.

Vous pouvez, sous réserve d'autorisation, exécuter les opérations suivantes :

- Gérer les clés de chiffrement à l'aide d'opérations [Créer](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importer](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Mettre à jour](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Supprimer](https://msdn.microsoft.com/library/azure/dn903611.aspx) entre autres

- Gérer les secrets à l’aide de [Obtenir](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Mettre à jour](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Supprimer](https://msdn.microsoft.com/library/azure/dn903613.aspx) et d’autres opérations

- Utiliser des clés de chiffrement avec les opérations [Signe](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Vérifier](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) et [Chiffrer](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Déchiffrer](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Les opérations sur des coffres de clés sont authentifiées et autorisées par le biais d'Azure Active Directory.

## Programmation de coffre de clés

Le système de gestion de coffre de clés pour les programmeurs se compose de plusieurs interfaces, avec REST comme base. [Référence d'API REST Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentation du Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Documentation du Kit de développement logiciel (SDK) Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Package du Kit de développement logiciel (SDK) .NET](https://azure.microsoft.com/fr-FR/documentation/api/)|[Package du Kit de développement logiciel (SDK) Node.js](https://www.npmjs.com/package/azure-keyvault)|

## Gestion des coffres de clés

Les conteneurs Azure Key Vault (coffres) peuvent également être gérés à l'aide de REST, de PowerShell ou de l'interface de ligne de commande, comme décrit dans les articles suivants :

- [Créer et gérer les coffres de clés avec REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Créer et gérer les coffres de clés avec PowerShell](key-vault-get-started.md)
- [Créer et gérer les coffres de clés avec l'interface de ligne de commande](key-vault-manage-with-cli.md)


## Procédures

Les articles suivants fournissent des conseils spécifiques à des tâches :

- [Génération et transfert de clés HSM protégées pour coffre de clés](key-vault-hsm-protected-keys.md)

## Exemples

- Ce téléchargement contient l'exemple d'application HelloKeyVault et un exemple de service web Azure. [Exemples coffre de clés Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Utilisez ce didacticiel pour vous aider à comprendre comment utiliser Azure Key Vault à partir d'une application web. [Utilisation d'Azure Key Vault à partir d'une application web](key-vault-use-from-web-application.md)

## Bibliothèques connexes

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) fournit les interfaces IKey et IKeyResolver pour localiser les clés des identificateurs et effectuer des opérations avec des clés.

- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) fournit des fonctionnalités étendues pour Azure Key Vault.

<!---HONumber=AcomDC_0121_2016-->