---
title: Chiffrement dans Azure Data Lake Store | Microsoft Docs
description: "Comprendre le fonctionnement du chiffrement et de la rotation des clés dans Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: fr-fr
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Chiffrement des données dans Azure Data Lake Store

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Présentation du chiffrement dans Azure Data Lake Store

Le chiffrement dans Azure Data Lake Store (ADLS) vous offre la possibilité de protéger vos données, de mettre en œuvre des stratégies de sécurité d’entreprise et de répondre aux exigences de conformité réglementaire. Cet article fournit une vue d’ensemble de la conception et traite des aspects techniques de la mise en œuvre du chiffrement des données par Data Lake Store.

ADLS s’appuie sur un chiffrement activé par défaut, transparent, des données au repos. Voici, plus en détails, ce que ces termes signifient :

* Activé par défaut : lorsque vous créez un nouveau compte Azure Data Lake Store, le paramètre par défaut active le chiffrement. Ainsi, les données stockées dans Data Lake Store sont toujours chiffrées avant d’être stockées sur un support permanent. Ce comportement s’applique à toutes les données et ne peut pas être modifié après la création d’un compte.
* Transparent : ADLS chiffre automatiquement les données avant le stockage permanent et déchiffre les données avant la récupération. Le chiffrement est configuré et géré au niveau Data Lake Store par un administrateur. Aucune modification n’est apportée aux API d’accès aux données. Aucune modification n’est donc requise dans les applications et services qui interagissent avec Data Lake Store en raison du chiffrement.

Les données en transit (ou données en mouvement) sont également toujours chiffrées dans Data Lake Store. Outre le chiffrement des données avant le stockage sur un support permanent, les données sont également toujours sécurisées en transit ou en mouvement à l’aide du protocole HTTPS (HTTP sur SSL (Secure Sockets Layer)). HTTPS est le seul protocole pris en charge pour les interfaces REST Data Lake Store.

![La figure 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Configuration du chiffrement avec Azure Data Lake Store

Le chiffrement pour Azure Data Lake Store est configuré lors de la création de compte ; il est toujours activé par défaut. Les clients ont la possibilité de gérer les clés ou d’autoriser Azure Data Lake Store (par défaut) à gérer les clés pour eux.

Pour découvrir comment configurer le chiffrement avec Azure Data Lake Store, consultez [Prise en main](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>Sous le capot - Comprendre le fonctionnement du chiffrement dans Azure Data Lake Store

### <a name="master-encryption-keys"></a>Clés de chiffrement principales

Azure Data Lake Store propose deux modes de gestion des clés de chiffrement principales (MEK). L’utilisation de clés de chiffrement principales est décrite plus en détails ci-dessous. À ce stade, supposons que la clé de chiffrement principale est la clé de niveau supérieur. Un accès à la clé de chiffrement principale est requis pour déchiffrer les données qui sont stockées dans Data Lake Store.

Les deux modes de gestion de la clé de chiffrement principale sont les suivants :

1.    Clés gérées par le service
2.    Clés gérées par le client

Dans ces deux modes, la clé de chiffrement principale est sécurisée en la stockant Azure Key Vault. Azure Key Vault est un service hautement sécurité entièrement géré sur Azure qui peut être utilisé pour protéger les clés cryptographiques. Pour en savoir plus sur Azure Key Vault, cliquez [ici](https://azure.microsoft.com/services/key-vault)

Voici une brève comparaison des fonctionnalités fournies par les deux modes de gestion des MEK.

|  | Clés gérées par le service | Clés gérées par le client |
| --- | --- | --- |
|Comment les données sont stockées ?|Toujours chiffrées avant d’être stockées|Toujours chiffrées avant d’être stockées|
|Où est stockée la clé de chiffrement principale ?|coffre de clés Azure|coffre de clés Azure|
|Des clés de chiffrement sont-elles stockées en dehors d’Azure Key Vault ?|Non|Non|
|La MEK peut-elle être récupérée dans Azure Key Vault ?|Non. Une fois stockée dans Key Vault, elle peut uniquement être utilisée pour le chiffrement et le déchiffrement.|Non. Une fois stockée dans Key Vault, elle peut uniquement être utilisée pour le chiffrement et le déchiffrement.|
|Qui possède Azure Key Vault et la MEK ?|Service Azure Data Lake Store.|Le client possède Azure Key Vault, qui fait partie de son propre abonnement Azure. La MEK dans Key Vault peut être gérée par logiciel ou matériel (HSM).|
|Le client peut-il révoquer l’accès à la MEK pour le service Azure Data Lake Store ?|Non|Oui. Il peut gérer des listes de contrôle d’accès sur Azure Key Vault et supprimer des entrées de contrôle d’accès à l’identité de service pour le service Azure Data Lake Store.|
|Le client peut-il supprimer définitivement la MEK ?|Non|Oui. Si le client supprime la MEK d’Azure Key Vault, les données dans le compte ADLS ne peuvent pas être déchiffrées par quiconque, y compris le service Azure Data Lake Store. <br><br> Si la MEK est explicitement sauvegardée par le client avant sa suppression d’Azure Key Vault, elle peut être restaurée et les données peuvent ensuite être récupérées. Toutefois, si la MEK n’est pas sauvegardée par le client avant sa suppression d’Azure Key Vault, les données dans le compte ADLS ne peuvent jamais être déchiffrées ultérieurement.|


Hormis la différence de niveau supérieur, l’utilisateur qui gère la MEK et le Key Vault dans lequel elle se trouve, les autres éléments de conception sont les mêmes pour les deux modes.

Vous devez retenir quelques points importants sur le choix du mode pour les clés de chiffrement principales.

1.    Vous pouvez choisir d’utiliser des clés gérées par le client ou des clés gérées par ADLS au moment de la configuration d’un compte ADLS
2.    Lorsqu’un compte ADLS est configuré, le mode ne peut pas être modifié

### <a name="encryption-and-decryption-of-data"></a>Chiffrement et déchiffrement des données

Trois types de clés sont utilisées dans la conception du chiffrement des données pour Azure Data Lake. Le tableau suivant résume leurs rôles :

| Clé                   | Abréviation | Associée à | Emplacement de stockage                             | Type       | Remarques                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Clé de chiffrement principale | MEK          | Un compte ADLS | coffre de clés Azure                              | Asymétrique | Peut être géré par ADLS ou par le client                                                              |
| Clé de chiffrement des données   | DEK          | Un compte ADLS | Stockage permanent, géré par le service ADLS | Symétrique  | La DEK est chiffrée par la MEK et la DEK chiffrée correspond à ce qui est stocké sur un support permanent par du service |
| Clé de chiffrement de bloc  | BEK          | Un bloc de données | Aucun                                         | Symétrique  | La BEK est dérivée de la DEK et du bloc de données                                                      |

Le schéma suivant illustre ces concepts :

![Figure 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algorithme lorsqu’un fichier doit être déchiffré :
1.    Vérifiez si la DEK du compte ADLS est mise en cache et prête à être utilisée.
    * Si ce n’est pas le cas, lisez alors la DEK chiffrée à partir d’un stockage permanent et envoyez-la vers Azure Key Vault pour être déchiffrée. Mettez la DEK déchiffrée en mémoire cache, elle est maintenant prête à être utilisée.
2.    Pour chaque bloc de données du fichier
    * Lire le bloc de données chiffré sur un stockage permanent
    * Générer la BEK à partir de la DEK et du bloc de données chiffré
    * Utiliser la BEK pour déchiffrer des données
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algorithme lorsqu’un bloc de données doit être chiffré :
1.    Vérifiez si la DEK du compte ADLS est mise en cache et prête à être utilisée.
    * Si ce n’est pas le cas, lisez alors la DEK chiffrée à partir d’un stockage permanent et envoyez-la vers Azure Key Vault pour être déchiffrée. Mettez la DEK déchiffrée en mémoire cache, elle est maintenant prête à être utilisée.
2.    Générez une BEK unique pour le bloc de données à partir de la DEK.
3.    Chiffrez le bloc de données avec la BEK à l’aide du chiffrement AES-256.
4.    Stocker le bloc de données chiffré sur un stockage permanent

> [!NOTE] 
> Pour des raisons de performances, la clé de chiffrement de données (DEK) est temporairement mise en mémoire cache pour une courte durée et effacée immédiatement une fois la durée écoulée. Sur un support permanent, elle est toujours stockée chiffrée par la clé de chiffrement principale (MEK).

## <a name="key-rotation"></a>Rotation des clés

Azure Data Lake Store permet la rotation de la clé de chiffrement principale (MEK) lors de l’utilisation de clés gérées par le client. Pour savoir comment configurer un compte ADLS avec des clés gérées par le client, consultez le page [Prise en main](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="pre-requisites"></a>Conditions préalables

Lors de la configuration du compte Azure Data Lake, les clients ont choisi d’utiliser leurs propres clés. Cette option ne peut pas être modifiée une fois le compte créé. Si vous utilisez les options par défaut pour le chiffrement, vos données seront toujours chiffrées à l’aide de clés gérées par Azure Data Lake. Ainsi, le client ne peut pas effectuer une rotation des clés car elles sont gérées par Azure Data Lake. Les étapes ci-dessous supposent que vous utilisez des clés gérées par le client (vous avez choisi vos propres clés dans votre Key Vault).

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Comment effectuer une rotation de la clé (MEK) dans Azure Data Lake Store

1. Connectez-vous au nouveau [portail Azure](https://portal.azure.com/)
2. Accédez au Key Vault dans lequel sont stockées vos clés associées à votre compte Azure Data Lake Store, puis sélectionnez des clés.

    ![de clés symétriques](./media/data-lake-store-encryption/keyvault.png)

3.    Sélectionnez la clé associée à votre compte Azure Data Lake Store et créez une nouvelle version de cette clé.
  
   À ce stade, Azure Data Lake prend uniquement en charge la rotation des clés vers une nouvelle version d’une clé ; nous ne prenons pas en charge la rotation vers une autre clé

   ![newversion](./media/data-lake-store-encryption/keynewversion.png)

4.    Accédez au compte de stockage Azure Data Lake et sélectionnez Chiffrement

    ![newversion](./media/data-lake-store-encryption/select-encryption.png)

5.    Une note vous informant qu’une nouvelle version de la clé est disponible et un bouton permettant d’effectuer une rotation de la clé vers cette nouvelle version s’affichent. Cliquez sur le bouton de rotation de la clé pour mettre à jour la clé vers la nouvelle version.

    ![done](./media/data-lake-store-encryption/rotatekey.png)

6. Cette opération doit prendre moins de 2 minutes et aucune interruption de service due à la rotation des clés n’est attendue. Une fois l’opération terminée, la nouvelle version de la clé est utilisée.

