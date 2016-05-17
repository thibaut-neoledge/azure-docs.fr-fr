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
   ms.date="04/29/2016"
   ms.author="bruceper" />

# Guide du développeur de coffre de clés Azure
Le coffre de clés permet d’accéder en toute sécurité à des informations sensibles de vos applications, par exemple :

- Les clés et secrets sont protégés sans que vous deviez écrire du code, et vous pouvez facilement les utiliser dans vos applications.
- Vous pouvez faire en sorte que vos clients possèdent et gèrent leurs propres clés, afin que vous puissiez vous concentrer sur la fourniture des principales fonctionnalités du logiciel. Ainsi, vos applications ne vous exposent à aucune responsabilité en relation avec les clés et secrets de vos clients.
- Votre application peut utiliser des clés pour la signature et le chiffrement, tandis que la gestion des clés s’effectue à l’extérieur de l’application, de sorte que la solution convient pour une application distribuée géographiquement.

Pour des informations plus générales sur le coffre de clés Azure Key Vault, voir [Qu’est-ce qu’Azure Key Vault ?](key-vault-whatis.md).

## Vidéos
Cette vidéo vous montre comment créer votre propre coffre de clés et comment l'utiliser à partir de l'exemple d'application « Hello Key Vault ».

Liens vers les ressources mentionnées dans la vidéo :
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Exemple de code de coffre de clés Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Pour en savoir plus, vous pouvez consulter le [blog](http://aka.ms/kvblog) et le [forum](http://aka.ms/kvforum) consacrés aux coffres de clés.



> [AZURE.VIDEO azure-key-vault-developer-quick-start]



## Création et gestion des coffres de clés

Avant d’utiliser Azure Key Vault dans votre code, vous pouvez créer et gérer des coffres via REST, des modèles Resource Manager, PowerShell ou CLI, comme décrit dans les articles suivants :

- [Créer et gérer les coffres de clés avec REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Créer et gérer les coffres de clés avec PowerShell](key-vault-get-started.md)
- [Créer et gérer les coffres de clés avec l'interface de ligne de commande](key-vault-manage-with-cli.md)
- [Créer un coffre de clés et ajouter un secret via un modèle ARM](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Les opérations sur les coffres de clés sont authentifiées via AAD et autorisées par une stratégie d’accès propre au coffre de clés.

## Codage avec coffre de clés

Le système de gestion de coffre de clés pour les programmeurs se compose de plusieurs interfaces, avec REST comme base, comme expliqué dans [Référence de l’API REST de coffre de clés](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Vous pouvez, sous réserve d’autorisation, exécuter les opérations suivantes :

- Gérer les clés de chiffrement à l'aide d'opérations [Créer](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importer](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Mettre à jour](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Supprimer](https://msdn.microsoft.com/library/azure/dn903611.aspx) entre autres

- Gérer les secrets à l’aide d’opérations [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) et autres

- Utiliser des clés de chiffrement avec les opérations [Signe](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Vérifier](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) et [Chiffrer](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Déchiffrer](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Les kits de développement logiciel (SDL) suivants sont disponibles pour une utilisation avec le coffre de clés :

|[![.NET](./media/key-vault-developers-guide/net.png)](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentation du Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/azure/dn903301.aspx)|[Documentation du Kit de développement logiciel (SDK) Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Package du Kit de développement logiciel (SDK) .NET](https://azure.microsoft.com/documentation/api/)|[Package du Kit de développement logiciel (SDK) Node.js](https://www.npmjs.com/package/azure-keyvault)|


Pour obtenir des exemples complets d’utilisation du coffre de clés avec vos applications, voir :

- Exemple d’application .NET *HelloKeyVault* et un exemple de service web Azure. [Exemples coffre de clés Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Didacticiel pour vous aider à comprendre comment utiliser Azure Key Vault à partir d’une application web dans Azure. [Utilisation d'Azure Key Vault à partir d'une application web](key-vault-use-from-web-application.md)

## Procédures

Les articles et scénarios suivants fournissent des conseils spécifiques sur l’utilisation d’Azure Key Vault :

- [Génération et transfert de clés protégées par HSM pour Azure Key Vault](key-vault-hsm-protected-keys.md) : ces informations vous aideront à planifier, générer, puis transférer vos propres clés protégées par le module de sécurité matériel (HSM) utilisées avec Azure Key Vault.
- [Transmission de valeurs sécurisées (par exemple des mots de passe) lors du déploiement](../resource-manager-keyvault-parameter.md) - Lorsque vous devez passer une valeur sécurisée (par exemple un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que clé secrète dans un coffre de clés Azure et faire référence à la valeur dans d'autres modèles Resource Manager.
- [Utilisation de Key Vault pour la gestion extensible de clés avec SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) : le connecteur SQL Server pour Azure Key Vault permet à SQL Server et à SQL-in-a-VM d'exploiter le service Azure Key Vault comme un fournisseur de gestion extensible de clés (EKM) afin de protéger ses clés de chiffrement pour le lien des applications ; chiffrement transparent des données, chiffrement de sauvegarde et chiffrement au niveau des colonnes.

Pour obtenir des conseils plus spécifiques sur certaines tâches, en relation avec l’intégration et l’utilisation de coffres de clés avec Azure, voir les [exemples de modèles ARM de Ryan Jones pour le coffre de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)

## Bibliothèques connexes

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/1.0.0) fournit les interfaces `IKey` et `IKeyResolver` pour localiser des clés à partir d’identificateurs et effectuer des opérations avec des clés.

- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/1.0.0) fournit des fonctionnalités étendues pour Azure Key Vault.

## Autres ressources Key Vault
- [Blog de Key Vault](http://aka.ms/kvblog)
- [Forum de Key Vault](http://aka.ms/kvforum)

<!---HONumber=AcomDC_0504_2016-->