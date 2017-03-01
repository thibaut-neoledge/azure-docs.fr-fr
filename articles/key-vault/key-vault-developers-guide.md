---
title: "Guide du développeur Key Vault | Microsoft Docs"
description: "Les développeurs peuvent utiliser le coffre de clés Azure pour gérer les clés de chiffrement dans l&quot;environnement Microsoft Azure. "
services: key-vault
documentationcenter: 
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: b2b1bd28-e149-4d69-b08b-97f6c50ebe30
ms.service: key-vault
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/17/2017
ms.author: bruceper
translationtype: Human Translation
ms.sourcegitcommit: 74de2165ea3a66cd0babc9f6d6d451522988bbe6
ms.openlocfilehash: b3fc845812e8a4a2729f026baa82b58319d5c5f3


---
# <a name="azure-key-vault-developers-guide"></a>Guide du développeur de coffre de clés Azure
À l’aide de Key Vault, vous pouvez accéder en toute sécurité à des informations sensibles de vos applications, par exemple :

* Les clés et secrets sont protégés sans que vous deviez écrire de code et vous pouvez facilement les utiliser dans vos applications.
* Vous pouvez faire en sorte que vos clients possèdent et gèrent leurs propres clés, afin que vous puissiez vous concentrer sur la fourniture des principales fonctionnalités du logiciel. Ainsi, vos applications ne vous exposent à aucune responsabilité en relation avec les clés et secrets de vos clients.
* Votre application peut utiliser des clés pour la signature et le chiffrement, tandis que la gestion des clés s’effectue à l’extérieur de l’application, de sorte que la solution convient pour une application distribuée géographiquement.
* Avec la version de septembre 2016 de Key Vault, vos applications peuvent désormais tirer parti des certificats de Key Vault. Pour plus d’informations, voir **clés, des secrets et des certificats** dans la [référence REST](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pour des informations plus générales sur le coffre de clés Azure Key Vault, voir [Qu’est-ce qu’Azure Key Vault ?](key-vault-whatis.md).

## <a name="videos"></a>Vidéos
Cette vidéo vous montre comment créer votre propre coffre de clés et comment l'utiliser à partir de l'exemple d'application « Hello Key Vault ».


>[!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player]


Liens vers les ressources mentionnées dans la vidéo :

* [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
* [Exemple de code de coffre de clés Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Pour en savoir plus, vous pouvez consulter le [blog Key Vault](http://aka.ms/kvblog) et participer au [forum Key Vault](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Création et gestion des coffres de clés
Avant d’utiliser Azure Key Vault dans votre code, vous pouvez créer et gérer des coffres via REST, des modèles Resource Manager, PowerShell ou CLI, comme décrit dans les articles suivants :

* [Créer et gérer les coffres de clés avec REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
* [Créer et gérer les coffres de clés avec PowerShell](key-vault-get-started.md)
* [Créer et gérer les coffres de clés avec l'interface de ligne de commande](key-vault-manage-with-cli.md)
* [Créer un coffre de clés et ajouter un secret via un modèle Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)

> [!NOTE]
> Les opérations sur les coffres de clés sont authentifiées via AAD et autorisées par une stratégie d’accès propre au coffre de clés.
>
>

## <a name="coding-with-key-vault"></a>Codage avec coffre de clés
Le système de gestion de coffre de clés pour les programmeurs se compose de plusieurs interfaces, avec REST comme base, comme expliqué dans [Référence de l’API REST de coffre de clés](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Vous pouvez, sous réserve d’autorisation, exécuter les opérations suivantes :

* Gérer les clés de chiffrement à l’aide d’opérations [Create](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Import](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Update](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903611.aspx) et autres
* Gérer les secrets à l’aide d’opérations [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) et autres
* Utiliser des clés de chiffrement avec les opérations [Sign](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) and [Encrypt](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[Decrypt](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Les kits de développement logiciel (SDL) suivants sont disponibles pour une utilisation avec le coffre de clés :

| [![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx) | [![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest) |
|:---:|:---:|
| [Documentation du Kit de développement logiciel (SDK) .NET](https://msdn.microsoft.com/library/mt765854.aspx) |[Documentation du Kit de développement logiciel (SDK) Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest) |
| [Package du Kit de Développement .NET sur NuGet](http://www.nuget.org/packages/Microsoft.Azure.KeyVault) |[Package du Kit de développement logiciel (SDK) Node.js](https://www.npmjs.com/package/azure-keyvault) |

Pour plus d’informations sur la version 2.x du Kit de développement logiciel (SDK) .NET, voir les [Notes de publication](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Exemple de code
Pour obtenir des exemples complets d’utilisation de Key Vault avec vos applications, voir :

* Exemple d’application .NET *HelloKeyVault* et un exemple de service web Azure. [Exemples coffre de clés Azure](http://www.microsoft.com/download/details.aspx?id=45343)
* Didacticiel pour vous aider à comprendre comment utiliser Azure Key Vault à partir d’une application web dans Azure. [Utilisation d'Azure Key Vault à partir d'une application web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Procédures
Les articles et scénarios suivants fournissent des conseils spécifiques sur l’utilisation d’Azure Key Vault :

* [Modifier l’ID de client de Key Vault après un déplacement d’abonnement](key-vault-subscription-move-fix.md) - Lorsque vous déplacez votre abonnement Azure du client A vers le client B, vos coffres de clés existants ne sont pas accessibles par les principaux (utilisateurs et applications) dans le client B. Ce guide vous aide à résoudre ce problème.
* [Accès à Key Vault derrière un pare-feu](key-vault-access-behind-firewall.md) - Pour accéder à un coffre de clés, votre application cliente Key Vault doit être en mesure d’accéder à plusieurs points de terminaison pour différentes fonctionnalités.
* [Génération et transfert de clés protégées par HSM pour Azure Key Vault](key-vault-hsm-protected-keys.md) : ces informations vous aideront à planifier, générer, puis transférer vos propres clés protégées par le module de sécurité matériel (HSM) utilisées avec Azure Key Vault.
* [Transmission de valeurs sécurisées (par exemple des mots de passe) lors du déploiement](../azure-resource-manager/resource-manager-keyvault-parameter.md) - Lorsque vous devez passer une valeur sécurisée (par exemple un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que clé secrète dans un coffre de clés Azure et faire référence à la valeur dans d'autres modèles Resource Manager.
* [Utilisation de Key Vault pour la gestion extensible de clés avec SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) : le connecteur SQL Server pour Azure Key Vault permet à SQL Server et à SQL-in-a-VM d'exploiter le service Azure Key Vault comme un fournisseur de gestion extensible de clés (EKM) afin de protéger ses clés de chiffrement pour le lien des applications ; chiffrement transparent des données, chiffrement de sauvegarde et chiffrement au niveau des colonnes.
* [Comment déployer des certificats sur des machines virtuelles à partir du coffre de clés](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Une application cloud s’exécutant sur une machine virtuelle sur Azure a besoin d’un certificat. Comment obtenir ce certificat sur cette machine virtuelle dès aujourd’hui ?
* [Configuration de Key Vault avec une rotation des clés et un audit de bout en bout](key-vault-key-rotation-log-monitoring.md) : cet article décrit la procédure de configuration de la rotation des clés et de l’audit avec Azure Key Vault.
* [Déploiement d’Azure App Service Certificate via Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) fournit des instructions détaillées pour déployer les certificats stockés dans Key Vault dans le cadre l’offre [App Service Certificate](https://azure.microsoft.com/en-us/blog/internals-of-app-service-certificate/).
* [Autoriser de nombreuses applications à accéder à un coffre de clés](key-vault-group-permissions-for-apps.md) La stratégie de contrôle de Key Vault ne prend en charge que 16 entrées. Mais vous pouvez créer un groupe de sécurité Azure Active Directory. Ajoutez tous les principaux du service associés à ce groupe de sécurité et accordez à ce groupe de sécurité l’accès à Key Vault.

Pour obtenir des conseils plus spécifiques sur certaines tâches, en relation avec l’intégration et l’utilisation de coffres de clés avec Azure, voir les [exemples de modèles Azure Resource Manager de Ryan Jones pour Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Intégration avec Key Vault
Ces articles concernent d’autres scénarios et services qui utilisent ou intègrent Key Vault.

* [Azure Disk Encryption](../security/azure-security-disk-encryption.md) s’appuie sur la fonctionnalité standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de Windows et la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement de volume du système d’exploitation et des disques de données. La solution est intégrée à Azure Key Vault pour vous aider à contrôler et à gérer les clés de chiffrement de disque et les secrets de votre abonnement au coffre de clés, tout en vous assurant que toutes les données des disques virtuels sont chiffrées au repos dans le stockage Azure.
* [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) propose une option de chiffrement des données stockées dans le compte. Concernant la gestion des clés, Data Lake Store met à votre disposition deux modes pour gérer les clés de chiffrement principales, qui sont nécessaires pour déchiffrer les données stockées dans le Data Lake Store. Vous pouvez soit laisser Data Lake Store gérer les clés de chiffrement principales, soit choisir de conserver la propriété des clés de chiffrement principales en utilisant votre compte Azure Key Vault. Vous spécifiez le mode de gestion de clés lors de la création du compte Data Lake Store. 
* [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) vous permet de gérer votre propre clé de locataire. Par exemple, plutôt que Microsoft gère votre clé de client (par défaut), vous pouvez gérer votre propre clé pour vous conformer aux réglementations spécifiques à votre organisation. La gestion de votre propre clé est également appelée BYOK, ou Bring your own key.


## <a name="supporting-libraries"></a>Bibliothèques connexes
* [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fournit les interfaces `IKey` et `IKeyResolver` pour localiser des clés à partir d’identificateurs et effectuer des opérations avec des clés.
* [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fournit des fonctionnalités étendues pour Azure Key Vault.

## <a name="other-key-vault-resources"></a>Autres ressources Key Vault
* [Blog de Key Vault](http://aka.ms/kvblog)
* [Forum de Key Vault](http://aka.ms/kvforum)



<!--HONumber=Jan17_HO3-->


