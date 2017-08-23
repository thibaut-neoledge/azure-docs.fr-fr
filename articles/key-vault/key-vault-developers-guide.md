---
title: "Guide du développeur de coffre de clés Azure"
description: "Les développeurs peuvent utiliser le coffre de clés Azure pour gérer les clés de chiffrement dans l'environnement Microsoft Azure."
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: bruceper
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 115862aca98926e354e4703f86cb4a7e1d1f72a2
ms.contentlocale: fr-fr
ms.lasthandoff: 08/05/2017

---
# <a name="azure-key-vault-developers-guide"></a>Guide du développeur de coffre de clés Azure

Key Vault vous permet d’accéder en toute sécurité aux informations sensibles à l’intérieur de vos applications :

- Les clés et secrets sont protégés sans que vous deviez écrire de code et vous pouvez facilement les utiliser dans vos applications.
- Vous pouvez faire en sorte que vos clients possèdent et gèrent leurs propres clés, afin que vous puissiez vous concentrer sur la fourniture des principales fonctionnalités du logiciel. Ainsi, vos applications ne vous exposent à aucune responsabilité en relation avec les clés et secrets de vos clients.
- Votre application peut utiliser des clés pour la signature et le chiffrement, tandis que la gestion des clés s’effectue à l’extérieur de l’application, de sorte que votre solution convient parfaitement pour une application distribuée géographiquement.
- Depuis la version de septembre 2016 de Key Vault, vos applications peuvent désormais utiliser des [certificats](https://docs.microsoft.com/rest/api/keyvault/certificate-operations) Key Vault. Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Pour des informations plus générales sur le coffre de clés Azure Key Vault, voir [Qu’est-ce qu’Azure Key Vault ?](key-vault-whatis.md).

## <a name="public-previews"></a>Préversions publiques

Nous publions régulièrement la préversion publique d’une nouvelle fonctionnalité de Key Vault. Essayez-les et envoyez votre avis à azurekeyvault@microsoft.com, notre adresse e-mail dédiée aux commentaires.

### <a name="storage-account-keys---july-10-2017"></a>Clés de compte de stockage - 10 juillet 2017

>[!NOTE]
>Pour cette mise à jour d’Azure Key Vault, seule la fonctionnalité **Clés de compte de stockage** est en préversion.

Cette préversion inclut notre nouvelle fonctionnalité Clés de compte de stockage, disponible par le biais de ces interfaces : [.NET/C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) et [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Pour plus d’informations sur la nouvelle fonctionnalité Clés de compte de stockage, consultez la page [Vue d’ensemble des clés de compte de stockage Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>vidéos

Cette vidéo vous montre comment créer votre propre coffre de clés et comment l'utiliser à partir de l'exemple d'application « Hello Key Vault ».

- [Développeur Key Vault - guide de démarrage rapide](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Ressources mentionnées dans la vidéo ci-dessus :

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Exemple de code de coffre de clés Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Création et gestion des coffres de clés

Avant d’utiliser Azure Key Vault dans votre code, vous pouvez créer et gérer des coffres via REST, des modèles Resource Manager, PowerShell ou CLI, comme décrit dans les articles suivants :

- [Créer et gérer les coffres de clés avec REST](https://docs.microsoft.com/rest/api/keyvault/)
- [Créer et gérer les coffres de clés avec PowerShell](key-vault-get-started.md)
- [Créer et gérer les coffres de clés avec l'interface de ligne de commande](key-vault-manage-with-cli2.md)
- [Créer un coffre de clés et ajouter un secret via un modèle Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)

> [!NOTE]
> Les opérations sur les coffres de clés sont authentifiées via AAD et autorisées par une stratégie d’accès propre au coffre de clés.

## <a name="coding-with-key-vault"></a>Codage avec coffre de clés

Le système de gestion Key Vault à destination des programmeurs se compose de plusieurs interfaces, dont REST qui constitue sa fondation. Par le biais de l’interface REST, toutes vos ressources Key Vault sont accessibles : clés, secrets et certificats. [Informations de référence sur l’API REST Key Vault](https://docs.microsoft.com/rest/api/keyvault/) 

### <a name="supported-programming-languages"></a>Langages de programmation pris en charge

#### <a name="net"></a>.NET

- [Informations de référence sur l’API .NET pour Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Pour plus d’informations sur la version 2.x du kit SDK .NET, consultez les [Notes de publication](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

- [Kit de développement logiciel (SDK) Java pour Key Vault](https://docs.microsoft.com/java/api/com.microsoft.azure.keyvault)

#### <a name="nodejs"></a>Node.js

Dans Node.js, l’API de gestion des coffres et l’API des objets de coffres sont distinctes. Key Vault Management permet de créer et de mettre à jour les coffres de clés. L’API Key Vault Operations sert à travailler avec des objets de coffres, comme les clés, les secrets et les certificats. 

- [Informations de référence sur l’API Node.js pour Key Vault Management](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/)
- [Informations de référence sur l’API Node.js pour les opérations Key Vault](http://azure.github.io/azure-sdk-for-node/azure-keyvault/latest/) 

### <a name="quick-start"></a>Démarrage rapide

- [Création d'un coffre de clés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Bien démarrer avec Key Vault dans Node.js](https://azure.microsoft.com/en-us/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Exemples de code

Pour obtenir des exemples complets d’utilisation de Key Vault avec vos applications, voir :

- [Exemples de code Azure Key Vault](http://www.microsoft.com/download/details.aspx?id=45343) - exemple d’application .NET *HelloKeyVault* et exemple de service web Azure. 
- [Utiliser Azure Key Vault à partir d’une application web](key-vault-use-from-web-application.md) : tutoriel destiné à vous apprendre à utiliser Azure Key Vault à partir d’une application web dans Azure. 

## <a name="how-tos"></a>Procédures

Les articles et scénarios suivants fournissent des conseils spécifiques sur l’utilisation d’Azure Key Vault :

- [Modifier l’ID de client de Key Vault après un déplacement d’abonnement](key-vault-subscription-move-fix.md) - Lorsque vous déplacez votre abonnement Azure du client A vers le client B, vos coffres de clés existants ne sont pas accessibles par les principaux (utilisateurs et applications) dans le client B. Ce guide vous aide à résoudre ce problème.
- [Accès à Key Vault derrière un pare-feu](key-vault-access-behind-firewall.md) - Pour accéder à un coffre de clés, votre application cliente Key Vault doit être en mesure d’accéder à plusieurs points de terminaison pour différentes fonctionnalités.
- [Génération et transfert de clés protégées par HSM pour Azure Key Vault](key-vault-hsm-protected-keys.md) : ces informations vous aideront à planifier, générer, puis transférer vos propres clés protégées par le module de sécurité matériel (HSM) utilisées avec Azure Key Vault.
- [Transmission de valeurs sécurisées (par exemple des mots de passe) lors du déploiement](../azure-resource-manager/resource-manager-keyvault-parameter.md) - Lorsque vous devez passer une valeur sécurisée (par exemple un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur en tant que clé secrète dans un coffre de clés Azure et faire référence à la valeur dans d'autres modèles Resource Manager.
- [Utilisation de Key Vault pour la gestion extensible de clés avec SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) : le connecteur SQL Server pour Azure Key Vault permet à SQL Server et à SQL-in-a-VM d'exploiter le service Azure Key Vault comme un fournisseur de gestion extensible de clés (EKM) afin de protéger ses clés de chiffrement pour le lien des applications ; chiffrement transparent des données, chiffrement de sauvegarde et chiffrement au niveau des colonnes.
- [Comment déployer des certificats sur des machines virtuelles à partir du coffre de clés](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Une application cloud s’exécutant sur une machine virtuelle sur Azure a besoin d’un certificat. Comment obtenir ce certificat sur cette machine virtuelle dès aujourd’hui ?
- [Configuration de Key Vault avec une rotation des clés et un audit de bout en bout](key-vault-key-rotation-log-monitoring.md) : cet article décrit la procédure de configuration de la rotation des clés et de l’audit avec Azure Key Vault.
- [Déploiement d’Azure App Service Certificate via Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) fournit des instructions détaillées pour déployer les certificats stockés dans Key Vault dans le cadre l’offre [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/).
- [Autoriser de nombreuses applications à accéder à un coffre de clés](key-vault-group-permissions-for-apps.md) La stratégie de contrôle de Key Vault ne prend en charge que 16 entrées. Mais vous pouvez créer un groupe de sécurité Azure Active Directory. Ajoutez tous les principaux du service associés à ce groupe de sécurité et accordez à ce groupe de sécurité l’accès à Key Vault.
- Pour obtenir des conseils plus spécifiques sur certaines tâches, en relation avec l’intégration et l’utilisation de coffres de clés avec Azure, voir les [exemples de modèles Azure Resource Manager de Ryan Jones pour Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Guide pratique pour utiliser la suppression réversible Key Vault avec l’interface CLI](key-vault-soft-delete-cli.md) décrit l’utilisation et le cycle de vie d’un coffre de clés et des différents objets du coffre de clés quand la suppression réversible est activée.
- [Guide pratique pour utiliser la suppression réversible Key Vault avec PowerShell](key-vault-soft-delete-powershell.md) décrit l’utilisation et le cycle de vie d’un coffre de clés et des différents objets du coffre de clés quand la suppression réversible est activée.

## <a name="integrated-with-key-vault"></a>Intégration avec Key Vault

Ces articles concernent d’autres scénarios et services qui utilisent ou intègrent Key Vault.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) s’appuie sur la fonctionnalité standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de Windows et la fonctionnalité [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement de volume du système d’exploitation et des disques de données. La solution est intégrée à Azure Key Vault pour vous aider à contrôler et à gérer les clés de chiffrement de disque et les secrets de votre abonnement au coffre de clés, tout en vous assurant que toutes les données des disques virtuels sont chiffrées au repos dans le stockage Azure.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) propose une option de chiffrement des données stockées dans le compte. Concernant la gestion des clés, Data Lake Store met à votre disposition deux modes pour gérer les clés de chiffrement principales, qui sont nécessaires pour déchiffrer les données stockées dans le Data Lake Store. Vous pouvez soit laisser Data Lake Store gérer les clés de chiffrement principales, soit choisir de conserver la propriété des clés de chiffrement principales en utilisant votre compte Azure Key Vault. Vous spécifiez le mode de gestion de clés lors de la création du compte Data Lake Store. 
- [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) vous permet de gérer votre propre clé de locataire. Par exemple, plutôt que Microsoft gère votre clé de client (par défaut), vous pouvez gérer votre propre clé pour vous conformer aux réglementations spécifiques à votre organisation. La gestion de votre propre clé est également appelée BYOK, ou Bring your own key.

## <a name="key-vault-overviews-and-concepts"></a>Concepts et présentations des coffres de clés

- [Comportement de suppression réversible de Key Vault](key-vault-ovw-soft-delete.md) : décrit une fonctionnalité qui permet la récupération des objets supprimés, que la suppression soit accidentelle ou intentionnelle.
- [Limitation du client Key Vault](key-vault-ovw-throttling.md) : vous oriente vers les concepts de base de la limitation et propose une approche pour votre application.
- [Vue d’ensemble des clés de compte de stockage Key Vault](key-vault-ovw-storage-keys.md) : décrit les clés de comptes de stockage Azure d’intégration Key Vault.
- [Mondes de sécurité Key Vault](key-vault-ovw-security-worlds.md) : décrit les relations entre les régions et les zones de sécurité.

## <a name="social"></a>Réseaux sociaux

- [Blog de Key Vault](http://aka.ms/kvblog)
- [Forum de Key Vault](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliothèques connexes

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fournit les interfaces **IKey** et **IKeyResolver** pour localiser des clés à partir d’identificateurs et effectuer des opérations avec des clés.
- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fournit des fonctionnalités étendues pour Azure Key Vault.



