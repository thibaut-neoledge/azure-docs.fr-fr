---
ms.assetid: 
title: "Suppression réversible d’Azure Key Vault | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 2b73fb55384cdcc8db2736557e0efef97b34fc45
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---
# <a name="azure-key-vault-soft-delete-feature-overview"></a>Présentation de la fonctionnalité de suppression réversible d’Azure Key Vault

>[!NOTE]
>Pour cette préversion d’Azure Key Vault, seule la fonctionnalité de **suppression réversible** est disponible en préversion. Azure Key Vault, dans son ensemble, est un service de production complet.

Pour cette version préliminaire d’Azure Key Vault, la suppression récupérable des coffres Key Vault et des objets Key Vault est appelée « suppression réversible ». Nous allons plus particulièrement aborder les scénarios suivants :

- Prise en charge de la suppression récupérable d’un coffre Key Vault
- Prise en charge de la suppression récupérable d’objets Key Vault (par exemple, clés, secrets et certificats)

## <a name="supporting-interfaces"></a>Prise en charge des interfaces

La fonctionnalité de suppression réversible est pour le moment disponible sur les interfaces .NET/C# et PowerShell. Pour plus d’informations, consultez les références correspondant à ces interfaces dans la [Documentation Key Vault](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Scénarios

Les coffres Azure Key Vault désignent des ressources suivies, gérées par Azure Resource Manager. Azure Resource Manager spécifie également un comportement bien défini pour la suppression, qui suppose qu’une opération DELETE correctement exécutée sur une ressource ne permet plus d’accéder à cette ressource. La fonctionnalité de suppression réversible permet la récupération de l’objet supprimé en cas de suppression accidentelle ou intentionnelle.

1. Dans un scénario type, un utilisateur peut avoir supprimé par mégarde un coffre Key Vault ou un objet Key Vault ; si la restauration du coffre ou de l’objet en question a été programmée pendant une période prédéterminée, l’utilisateur peut annuler la suppression et récupérer ses données.

2. Dans un autre scénario, un utilisateur non autorisé peut tenter de supprimer un coffre Key Vault ou un objet Key Vault, par exemple une clé se trouvant à l’intérieur d’un coffre, pour provoquer une interruption de service. Le fait de séparer la suppression du coffre Key Vault ou de l’objet Key Vault de la suppression effective des données sous-jacentes peut renforcer la sécurité, par exemple en limitant les autorisations de suppression de données à un autre rôle approuvé. Cela suppose d’appliquer un quorum à une opération donnée qui pourrait, dans d’autres circonstances, se traduire par une perte de données immédiate.

### <a name="soft-delete-behavior"></a>Comportement de la suppression réversible

Avec cette version préliminaire, l’opération DELETE sur un coffre Key Vault ou un objet Key Vault constitue une suppression réversible, qui conserve efficacement les ressources pendant une période de rétention donnée, tout en donnant l’impression que l’objet est supprimé. Le service fournit en outre un mécanisme de récupération de l’objet supprimé, qui a essentiellement pour effet d’annuler la suppression. 

La suppression réversible est un comportement facultatif de Key Vault et **n’est pas activée par défaut** dans cette version. Pour plus d’informations sur l’activation de la suppression réversible pour votre Key Vault, consultez les instructions spécifiques à l’interface de votre choix dans la [Documentation Key Vault](https://docs.microsoft.com/azure/key-vault/).

### <a name="key-vault-recovery"></a>Récupération d’un Key Vault

Lors de la suppression d’un Key Vault, le service crée une ressource de proxy sous l’abonnement, en y ajoutant suffisamment de métadonnées pour faciliter la récupération. La ressource de proxy est un objet stocké, disponible au même emplacement que le Key Vault supprimé. 

### <a name="key-vault-object-recovery"></a>Récupération d’objets Key Vault

Lors de la suppression d’un objet Key Vault (une clé, par exemple), le service place l’objet à l’état supprimé, ce qui le rend inaccessible à toutes les opérations de récupération. Dans cet état, l’objet Key Vault ne peut pas être répertorié, restauré ou supprimé de façon forcée ou permanente. 

Dans le même temps, Key Vault planifiera la suppression des données sous-jacentes correspondant au Key Vault ou à l’objet Key Vault supprimé afin qu’elle soit exécutée après un intervalle de rétention prédéterminé. L’enregistrement DNS correspondant au coffre est également conservé pendant la durée de l’intervalle de rétention.

### <a name="soft-delete-retention-period"></a>Période de rétention de la suppression réversible

Les ressources ayant fait l’objet d’une suppression réversible sont conservées pendant une durée fixée à 90 jours. 

>[!NOTE]
> Cette période de rétention de la suppression réversible n’est pas configurable dans notre version préliminaire du 10 mai 2017. 

Au cours de la période de rétention de la suppression réversible :

- Vous pouvez répertorier tous les coffres Key Vault et les objets Key Vault à l’état de suppression réversible pour votre abonnement, et accéder à des informations concernant leur suppression et leur récupération.
    - Seuls les utilisateurs disposant d’autorisations spéciales peuvent répertorier des coffres supprimés. Nous recommandons à nos utilisateurs de créer un rôle personnalisé avec ces autorisations spéciales pour le traitement des coffres supprimés.
- Il est impossible de créer un Key Vault du même nom au même emplacement. En conséquence, un objet Key Vault ne peut pas être créé dans un coffre donné si celui-ci contient un objet portant le même nom et se trouvant à l’état supprimé 
- Seul un utilisateur disposant de privilèges spécifiques peut restaurer un coffre Key Vault ou un objet Key Vault en exécutant une commande de récupération sur la ressource de proxy correspondante.
    - L’utilisateur membre du rôle personnalisé qui est autorisé à créer un coffre sous le groupe de ressources peut restaurer le coffre.
- Seul un utilisateur disposant de privilèges spécifiques peut forcer la suppression d’un coffre Key Vault ou d’un objet Key Vault en exécutant une commande de suppression sur la ressource de proxy correspondante.

Sauf en cas de restauration d’un coffre Key Vault ou d’un Objet Key Vault, à la fin de l’intervalle de rétention, le service videra le coffre ou l’objet Key Vault qui a fait l’objet d’une suppression réversible, ainsi que son contenu. La suppression de la ressource ne peut pas être replanifiée.

### <a name="permissioned-purge"></a>Purge autorisée

Il est possible de supprimer ou vider définitivement un coffre Key Vault en exécutant une commande POST sur la ressource de proxy. Cette opération nécessite des privilèges spéciaux. En général, seul le propriétaire de l’abonnement peut vider un coffre. L’opération POST déclenchera la suppression immédiate et irrécupérable de ce coffre, 

sauf si l’abonnement Azure a été marqué comme *impossible à supprimer*. Dans ce cas, seul le service peut effectuer la suppression, dans le cadre d’un processus planifié. 




