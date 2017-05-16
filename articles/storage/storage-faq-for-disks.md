---
title: "Forum aux Questions (FAQ) sur les disques de machine virtuelle Azure IaaS | Microsoft Docs"
description: "Forum aux questions sur les disques de machines virtuelles et les disques Premium (gérés et non gérés) Azure IaaS"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 284b239860481cf76f647d78f6a7b5e2b7cf9a3b
ms.contentlocale: fr-fr
ms.lasthandoff: 04/26/2017


---
# <a name="frequently-asked-questions-about-azure-iaas-vm-disks-and-managed-and-unmanaged-premium-disks"></a>Forum aux questions sur les disques de machines virtuelles et les disques Premium gérés et non gérés Azure IaaS

Dans cet article, nous étudierons les questions fréquentes sur Managed Disks et le stockage Premium.

## <a name="managed-disks"></a>Managed Disks

**Qu’est-ce qu’Azure Managed Disks ?**

Managed Disks est une fonctionnalité qui simplifie la gestion des disques associés aux machines virtuelles Azure IaaS en prenant en charge pour vous la gestion des comptes de stockage. Pour plus d’informations, consultez la [vue d’ensemble d’Azure Managed Disks](storage-managed-disks-overview.md).

**Si je crée un disque standard géré à partir d’un disque dur virtuel existant présentant une taille de 80 Go, combien cela me coûtera-t-il ?**

Un disque géré standard créé à partir d’un disque dur virtuel de 80 Go sera traité comme la taille immédiatement supérieure disponible de disque standard, dont le coût s’élève à 10 dollars. Vous serez facturé 10 dollars par disque. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/storage).

**Des frais de transaction s’appliquent-ils aux disques gérés Standard ?**

Oui, vous êtes facturé pour chaque transaction. Consultez la [page de tarification] (https://azure.microsoft.com/fr-fr/pricing/details/storage/blobs/) pour en savoir plus.

**Pour un disque standard géré, serai-je facturé pour la taille réelle des données sur le disque ou pour la capacité configurée du disque ?**

Vous êtes facturé en fonction de la capacité configurée du disque. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/storage).

**En quoi la tarification appliquée aux disques gérés Premium est-elle différente de cette associée aux disques non gérés ?**

La tarification de ces 2 types de disques est identique.

**Puis-je modifier le type de compte de stockage (Standard/Premium) de mes disques gérés ?**

Oui. Vous pouvez modifier le type de compte de stockage de vos disques gérés dans le portail Azure, dans PowerShell ou dans l’interface Azure CLI.

**Existe-t-il un moyen de copier ou d’exporter un disque géré vers un compte de stockage privé ?**

Oui, vous pouvez exporter vos disques gérés à l’aide du portail Azure, de PowerShell ou de l’interface Azure CLI.

**Puis-je utiliser un fichier VHD dans un compte de stockage Azure pour créer un disque géré dans un abonnement différent ?**

Non.

**Puis-je utiliser un fichier VHD dans un compte de stockage Azure pour créer un disque géré dans une région différente ?**

Non.

**Y aura-t-elle des restrictions de mise à l’échelle pour les clients utilisant des disques gérés ?**

Managed Disks élimine les restrictions associées aux comptes de stockage. Toutefois, le nombre de disques gérés par abonnement est limité à 2 000 par défaut. Pour augmenter ce paramètre, appelez le support.

**Puis-je prendre une capture instantanée incrémentielle d’un disque géré ?**

Non. La fonctionnalité actuelle de capture instantanée exécute une copie complète d’un disque géré. Toutefois, nous envisageons de prendre en charge les captures instantanées incrémentielles à l’avenir.

**Les machines virtuelles d’un groupe à haute disponibilité peuvent-elles valoriser une combinaison de disques gérés et non gérés ?**

Non, les machines virtuelles d’un groupe à haute disponibilité doivent utiliser exclusivement des disques gérés ou non gérés. Lorsque vous créez un groupe à haute disponibilité, vous pouvez définir le type des disques à utiliser.

**Les disques gérés sont-ils l’option par défaut dans le portail Azure ?**

Pas actuellement, mais ils le seront à l’avenir.

**Est-il possible de créer un disque géré vide ?**

Oui, vous pouvez tout à fait créer un disque vide. Un disque géré peut être créé indépendamment d’une machine virtuelle, c’est-à-dire sans l’attacher à aucune machine virtuelle.

**Par défaut, combien de domaines sont pris en charge pour les groupes à haute disponibilité utilisant Managed Disks ?**

Par défaut, 2 ou 3 domaines par défaut sont pris en charge pour les groupes à haute disponibilité utilisant Managed Disks, en fonction de la région qu’ils occupent.

**Comment est configuré le compte de stockage Standard pour les diagnostics ?**

Vous configurez un compte de stockage privé pour les diagnostics de machine virtuelle. À l’avenir, nous prévoyons de basculer également les diagnostics sur Managed Disks.

**Quel type de prise en charge RBAC avons-nous mis en place pour Managed Disks ?**

Managed Disks prend en charge trois rôles principaux par défaut :

1.  Propriétaire : peut tout gérer, notamment les accès.

2.  Collaborateur : peut tout gérer, sauf les accès.

3.  Lecteur : peut tout afficher sans pouvoir apporter de modifications.

**Existe-t-il un moyen de copier ou d’exporter un disque géré vers un compte de stockage privé ?**

Vous pouvez obtenir un URI de signature d’accès partagé en lecture seule pour le disque géré et l’utiliser pour copier le contenu vers un compte de stockage privé ou un espace de stockage local.

**Puis-je créer une copie de mon disque géré ?**

Les clients peuvent prendre une capture instantanée de leurs disques gérés, qu’ils utilisent pour créer un autre disque géré.

**Les disques non gérés sont-ils pris en charge ?**

Oui, nous prenons à la fois en charge les disques gérés et non gérés. Toutefois, nous vous recommandons de démarrer avec des disques gérés pour les nouvelles charges de travail et de migrer vos charges de travail actuelles vers Managed Disks.

**Si je crée un disque présentant une taille de 128 Go, et que j’augmente la taille à 130 Go, serai-je facturé en fonction de la taille supérieure de disque (512 Go) ?**

Oui.

**Puis-je créer des disques gérés LRS, GRS et ZRS ?**

Actuellement, Azure Managed Disks prend uniquement en charge le stockage localement redondant (LRS).

**Puis-je réduire (ou réduire la taille de) mes disques gérés ?**
Non. Cette fonctionnalité n’est pas prise en charge pour l’instant. 

**Puis-je modifier la propriété de nom d’ordinateur lors de l’utilisation d’un disque de système d’exploitation spécialisé (pas préparé avec Sysprep ni généralisé) pour configurer une machine virtuelle** Non. Vous ne pouvez pas mettre à jour la propriété de nom d’ordinateur. La nouvelle machine virtuelle hérite de celle de la machine virtuelle parente qui a été utilisée pour créer le disque de système d’exploitation. 

## <a name="managed-disks-and-port-8443"></a>Managed Disks et port 8443

**Pourquoi les clients doivent-ils débloquer le trafic sortant sur le port 8443 pour les machines virtuelles avec Azure Managed Disks ?**

L’Agent de machine virtuelle Azure utilise le port 8443 pour signaler l’état de chaque extension de machine virtuelle à la plateforme Azure. Sans le déblocage de ce port, l’Agent de machine virtuelle ne pourra pas signaler l’état des extensions de machines virtuelles. Pour plus d’informations sur l’Agent de machine virtuelle, consultez [Vue d’ensemble de l’Agent de machine virtuelle Azure](../virtual-machines/windows/agent-user-guide.md).

**Que se passe-t-il si une machine virtuelle est déployée avec extensions et que le port n’est pas débloqué ?**

Le déploiement entraîne une erreur. 

**Que se passe-t-il si une machine virtuelle est déployée sans extensions et que le port n’est pas débloqué ?**

Il n’y aura aucun impact sur le déploiement. 

**Que se passe-t-il si une extension est installée sur une machine virtuelle qui est déjà approvisionnée et en cours d’exécution et que le port 8443 n’est pas débloqué pour la machine virtuelle ?**

Le déploiement de l’extension ne réussira pas. L’état de l’extension sera inconnu. 

**Que se passe-t-il si un modèle ARM est utilisé pour approvisionner plusieurs machines virtuelles avec le port 8443 bloqué : une machine virtuelle avec extensions et une deuxième machine virtuelle qui dépend de la première machine virtuelle ?**

Le déploiement de la première machine virtuelle apparaîtra comme un échec, car le déploiement des extensions n’a pas réussi. La deuxième machine virtuelle ne sera pas déployée. 

**Cette exigence de déblocage du port s’appliquera-t-elle à toutes les extensions de machines virtuelles ?**

Oui.

**Les connexions entrantes et sortantes sur le port 8443 doivent-elles être débloquées ?**

Non. Seules les connexions entrantes et sortantes sur le port 8443 doivent être débloquées. 

**Est-il nécessaire de débloquer les connexions sortantes sur le port 8443 sur toute la durée de vie de la machine virtuelle ?**

Oui.

**Le déblocage de ce port affecte-t-il les performances de la machine virtuelle ?**

Non.

**Y a-t-il une date estimée de résolution de ce problème, qui permette de ne plus avoir à débloquer le port 8443 ?**

Oui, à la fin du mois de mai 2017.

## <a name="premium-disks--both-managed-and-unmanaged"></a>Disques Premium - Gérés et non gérés

**Si une machine virtuelle utilise une taille qui prend en charge le stockage Premium, comme DSv2, puis-je attacher des disques de données Standard et Premium ?** 

Oui.

**Puis-je attacher des disques de données Standard et Premium à une gamme de taille qui ne prend pas en charge le stockage Premium, comme D, Dv2, G ou F ?**

Non. Vous pouvez attacher aux machines virtuelles uniquement des disques de données Standard qui n’utilisent pas une gamme de taille prise en charge par le stockage Premium.

**Si je crée un disque de données Premium à partir d’un disque dur virtuel existant dont la taille était de 80 Go de taille, comment cela me coûte-t-il ?**

Un disque de données Premium créé à partir d’un disque dur virtuel de 80 Go est considéré comme ayant la taille de disque Premium disponible suivante, soit un disque P10. Vous êtes facturé au tarif d’un disque P10.

**L’utilisation du stockage Premium occasionne-t-elle des coûts de transaction ?**

Il existe un coût fixe pour chaque taille de disque configurée avec un nombre d’E/S par seconde et un débit donnés. Les seuls autres coûts sont liés à la bande passante sortante et à la capacité de captures instantanées, le cas échéant. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/storage).

**Quelles sont les limites d’E/S par seconde et de débit associées à la mise en cache sur disque ?**

Les limites combinées pour le cache et le disque SSD local d’une machine de la série DS sont de 4 000 IOPS par cœur et 33 Mo par seconde par cœur. La série GS offre 5 000 E/S par seconde par cœur et 50 Mo par seconde par cœur.

**Le disque local SSD est-il pris en charge pour les machines virtuelles Managed Disks ?**

Le disque local SSD est un stockage temporaire inclus avec une machine virtuelle de disques gérés. Ce stockage temporaire n’occasionne aucun frais supplémentaire. Il est recommandé de ne pas utiliser ce disque SSD local pour le stockage des données de votre application, car il n’est pas persistant dans le Stockage Blob Azure.

**Y-a-t-il des répercussions sur l’utilisation de la fonction TRIM sur les disques Premium ?**

Il n’existe aucun inconvénient à utiliser TRIM sur des disques Azure Premium ou Standard.

## <a name="what-if-my-question-isnt-answered-here"></a>Que dois-je faire si je n’ai pas trouvé de réponse à ma question ici ?

Si votre question n’est pas répertoriée ici, faites-le-nous savoir et nous vous aiderons à trouver une réponse. Vous pouvez poser une question à la fin de cet article, dans les commentaires, ou dans le [forum du stockage Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata) du site MSDN afin de solliciter l’équipe du stockage Azure et d’autres membres de la communauté sur des problématiques autour de cet article.

Pour soumettre une demande de fonctionnalité, transmettez vos questions et vos idées sur le [forum des commentaires du stockage Azure](https://feedback.azure.com/forums/217298-storage).

