---
title: FAQ Azure Disk Encryption | Microsoft Docs
description: "Cet article offre des réponses aux questions courantes sur Microsoft Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 2a81a88750ae40cd75e5d394f97c8310416546e4
ms.contentlocale: fr-fr
ms.lasthandoff: 07/28/2017

---
# <a name="azure-disk-encryption-frequently-asked-questions-faq"></a>Forum aux questions sur Azure Disk Encryption

Ce FAQ répond aux questions sur Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux. Pour plus d’informations sur ce service, consultez la page [Chiffrement de disque Azure pour des machines virtuelles Windows et Linux IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Questions générales
Q. Dans quelles régions Azure Disk Encryption est-il en disponibilité générale ?
R : Le chiffrement Azure Disk Encryption pour les machines virtuelles IaaS Windows et Linux est en disponibilité générale dans toutes les régions publiques Azure.

Q : Avec quelles autres expériences utilisateur Azure Disk Encryption est-il disponible ?
R : Azure Disk Encryption (disponibilité générale) prend en charge les modèles Azure Resource Manager, Azure PowerShell et Azure CLI. Cela vous donne une grande flexibilité, car vous disposez de trois options différentes pour activer le chiffrement des disques sur vos machines virtuelles IaaS. Vous trouverez plus d’informations et d’instructions étape par étape dans les scénarios et expériences Azure Disk Encryption.

Q : Combien coûte Azure Disk Encryption ?
R: Le chiffrement des disques de machines virtuelles avec Azure Disk Encryption est gratuit.

Q : Avec quels niveaux de machines virtuelles puis-je utiliser Azure Disk Encryption ?
A : Azure Disk Encryption est disponible uniquement sur les machines virtuelles de niveau standard, notamment avec les machines virtuelles IaaS séries [A, D, DS, G, GS, F](https://azure.microsoft.com/pricing/details/virtual-machines/), etc., incluant également les machines avec stockage Premium. Il n’est pas disponible sur les machines virtuelles de niveau de base.

Q : Quelles sont les distributions Linux prises en charge par Azure Disk Encryption ?
R : Azure Disk Encryption est pris en charge sur les versions et distributions de serveur Linux suivantes :
| Distribution Linux | Version | Type de volume pris en charge pour le chiffrement|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Disque de système d’exploitation et de données |
| Ubuntu | 14.04.5-DAILY-LTS | Disque de système d’exploitation et de données |
| RHEL | 7.3 | Disque de système d’exploitation et de données |
| RHEL | 7,2 | Disque de système d’exploitation et de données |
| RHEL | 6,8 | Disque de système d’exploitation et de données |
| RHEL | 6.7 | Disque de données |
| CentOS | 7.3 | Disque de système d’exploitation et de données |
| CentOS | 7.2n | Disque de système d’exploitation et de données |
| CentOS | 6.8 | Disque de système d’exploitation et de données |
| CentOS | 7.1 | Disque de données |
| CentOS | 7.0 | Disque de données |
| CentOS | 6.7 | Disque de données |
| CentOS | 6.6 | Disque de données |
| CentOS | 6.5 | Disque de données |
| openSUSE | 13.2 | Disque de données |
| SLES | 12 SP1 | Disque de données |
| SLES | Priority:12-SP1 | Disque de données |
| SLES | HPC 12 | Disque de données |
| SLES | Priority:11-SP4 | Disque de données |
| SLES | 11 SP4 | Disque de données |

Q : Comment puis-je commencer à utiliser Azure Disk Encryption ?
R : Les clients peuvent commencer par consulter le livre blanc sur Azure Disk Encryption situé [ici](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

Q : Puis-je chiffrer des volumes de démarrage et de données avec Azure Disk Encryption ?
R : Oui, vous pouvez chiffrer des volumes de démarrage et de données pour les machines virtuelles IaaS Windows et Linux. Pour les machines virtuelles Windows, vous ne pouvez pas chiffrer les données sans d’abord chiffrer le volume du système d’exploitation. Pour les machines virtuelles Linux, vous pouvez chiffrer le volume de données sans chiffrer le volume du système d’exploitation. Une fois que vous avez chiffré le volume du système d’exploitation sous Linux, la désactivation du chiffrement sur un volume du système d’exploitation pour les machines virtuelles IaaS Linux n’est pas prise en charge.

Q : Azure Disk Encryption propose-t-il une fonctionnalité « BYOK » (apportez votre propre clé) ?
R : Oui, vous pouvez fournir vos propres clés de chiffrement à clé. Ces clés sont sauvegardées dans Azure Key Vault, le magasin de clés d’Azure Disk Encryption. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement à clé, consultez les scénarios et expériences de déploiement d’Azure Disk Encryption.

Q : Puis-je utiliser une clé de chiffrement à clé créé par Azure ?
R : Oui, vous pouvez utiliser Azure Key Vault pour générer la clé de chiffrement à clé pour une utilisation avec Azure Disk Encryption. Ces clés sont sauvegardées dans Azure Key Vault, le magasin de clés d’Azure Disk Encryption. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement à clé, consultez les scénarios et expériences de déploiement d’Azure Disk Encryption.

Q : Puis-je utiliser le service de gestion de clés local/HSM pour sauvegarder les clés de chiffrement ?
R : Vous ne pouvez pas utiliser le service de gestion de clés local/HSM pour sauvegarder les clés de chiffrement avec Azure Disk Encryption. Vous pouvez uniquement utiliser le service Azure Key Vault pour sauvegarder les clés de chiffrement. Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement à clé, consultez les scénarios et expériences de déploiement d’Azure Disk Encryption.

Q : Quelles sont les conditions requises pour configurer Azure Disk Encryption ?
R : Le script PowerShell prérequis avec Azure Disk Encryption pour créer l’application AAD, créer un coffre de clés ou en configurer un existant pour l’accès au chiffrement de disque et pour activer le chiffrement et sauvegarder les secrets et la clé.  Pour plus d’informations sur les scénarios de prise en charge des clés de chiffrement à clé, consultez les conditions requises, ainsi que les scénarios et expériences de déploiement d’Azure Disk Encryption.

Q : Où puis-je obtenir plus d’informations sur l’utilisation de PowerShell pour la configuration d’Azure Disk Encryption ?
R : Vous pouvez consulter des articles très intéressants sur l’exécution de tâches Azure Disk Encryption de base, ainsi que des exemples de scénarios plus avancés. Pour en savoir plus sur les tâches de base, consultez le billet de blog en anglais [Azure Disk Encryption with Azure PowerShell - Part 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/) (Explorer Azure Disk Encryption avec Azure PowerShell - Partie 1). Pour connaître les scénarios plus avancés, consultez le billet de blog en anglais [Azure Disk Encryption with Azure PowerShell – Part 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/) (Explorer Azure Disk Encryption avec Azure PowerShell - Partie 2).

Q : Quelle version d’Azure PowerShell est prise en charge par Azure Disk Encryption ?
R : Utilisez la dernière version du Kit de développement logiciel (SDK) Azure PowerShell pour configurer Azure Disk Encryption. Téléchargez la dernière version d’[Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption n’est PAS pris en charge par le Kit de développement logiciel (SDK) Azure PowerShell version 1.1.0.

> [!NOTE]
> Il est déconseillé d’utiliser l’extension de la version préliminaire d’Azure Disk Encryption pour Linux. Pour plus d’informations, reportez-vous à [cette documentation](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

Q : Puis-je appliquer le chiffrement de disque Azure sur mon image Linux personnalisée ?
R : Vous ne pouvez pas appliquer le chiffrement de disque Azure sur votre image Linux personnalisée. Nous prenons uniquement en charge les images Linux de la galerie pour les distributions prises en charge indiquées ci-dessus. Actuellement, nous ne prenons pas en charge les images Linux personnalisées.

Q : Puis-je appliquer des mises à jour sur une machine virtuelle Red Hat Linux à partir d’une mise à jour Yum ?
R : Oui, vous pouvez appliquer une mise à jour ou un correctif sur une machine virtuelle Red Hat Linux en suivant les instructions décrites [ici](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

Q : Où puis-je poser des questions ou envoyer mes commentaires ? R : Vous pouvez poser des questions ou envoyer vos commentaires sur le forum d’Azure Disk Encryption [ici](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption).

## <a name="see-also"></a>Voir aussi
Ce document vous a fourni les réponses aux questions les plus courantes concernant Azure Disk Encryption. Pour en savoir plus sur ce service et ses fonctionnalités, consultez les pages suivantes :

- [Appliquer le chiffrement de disque dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Chiffrement d’une machine virtuelle Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure Data Encryption-at-Rest](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest) (Chiffrement des données Azure au repos)

