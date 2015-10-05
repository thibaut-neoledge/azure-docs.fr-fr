<properties
   pageTitle="Guide du développeur de coffre de clés | Microsoft Azure"
   description="Les développeurs peuvent utiliser le coffre de clés Azure pour gérer les clés de chiffrement dans l'environnement Microsoft Azure."
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/22/2015"
   ms.author="mbaldwin" />

# Guide du développeur de coffre de clés Azure

> [AZURE.VIDEO azure-key-vault-developer-quick-start]

Les développeurs peuvent utiliser le coffre de clés Azure pour gérer les clés de chiffrement dans l'environnement Microsoft Azure. Le coffre de clés prend en charge plusieurs types de clés et algorithmes et peut être utilisé avec des modules de sécurité matériel (HSM) pour les clés de valeur des clients. En outre, vous pouvez utiliser le coffre de clés pour stocker en toute sécurité les secrets qui sont des objets avec un nombre d'octets limité avec aucune sémantique spécifique. Un coffre de clés peut contenir une combinaison de clés et de secrets. Le contrôle d'accès pour les types d'objets est géré indépendamment.

Les utilisateurs soumis à autorisation, les utilisateurs peuvent exécuter les opérations suivantes :

- Gérer les clés de chiffrement à l'aide d'opérations [Créer](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importer](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Mettre à jour](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Supprimer](https://msdn.microsoft.com/library/azure/dn903611.aspx) entre autres

- Gérer les secrets à l'aide de [Obtenir](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update] (https://msdn.microsoft.com/library/azure/dn986818.aspx, [Supprimer](https://msdn.microsoft.com/library/azure/dn903613.aspx) et d'autres opérations

- Utiliser des clés de chiffrement avec les opérations [Signe](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Vérifier](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) et [Chiffrer](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Déchiffrer](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Les opérations sur des coffres de clés sont authentifiées et autorisées à l'aide d'Azure Active Directory.

## Programmation de coffre de clés

Le système de gestion de coffre de clés pour les programmeurs se compose de plusieurs interfaces, avec REST comme base.

### REST

L'API REST est le fondement de toute interaction par programme avec le coffre de clés.

Le coffre de clés possède son propre point de terminaison REST qui est décrit dans la [référence API REST de coffre de clés](https://msdn.microsoft.com/library/azure/dn903609.aspx)

### .NET

L'API .NET est un ensemble de wrappers qui permet la mise en œuvre via le modèle de programmation C# sans avoir besoin d'interagir directement avec le point de terminaison REST. Ici, vous pouvez trouver la [référence d'API client .NET de coffre de clés](https://msdn.microsoft.com/library/azure/dn903301.aspx).

### Node.js

L'API Node.js est un ensemble de wrappers qui permet la mise en œuvre via le modèle de programmation JavaScript sans avoir besoin d'interagir directement avec le point de terminaison REST. Vous trouverez ici le [Microsoft Azure SDK pour Node.js - Gestionnaire de clés de coffre](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/).

## Gestion des clés coffre avec PowerShell et CLI

Clés du coffre de clé et les secrets Azure peuvent également être gérés à l'aide de PowerShell et CLI, comme décrit dans les articles suivants :

- [Créer et gérer les coffres de clés avec PowerShell](key-vault-get-started.md)
- [Créer et gérer les coffres de clés avec l'interface de ligne de commande](key-vault-manage-with-cli.md)
- [Génération et transfert de clés HSM protégées pour coffre de clés](key-vault-hsm-protected-keys.md)
- [À propos des clés et des secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx)

## Voir aussi

- [Exemples coffre de clés Azure](http://www.microsoft.com/download/details.aspx?id=45343)

<!---HONumber=Sept15_HO4-->