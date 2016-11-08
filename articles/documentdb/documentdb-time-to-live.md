---
title: Faire expirer des données dans DocumentDB avec la durée de vie | Microsoft Docs
description: Avec la TTL, Microsoft Azure DocumentDB offre la possibilité de vider automatiquement les documents du système après une période déterminée.
services: documentdb
documentationcenter: ''
keywords: durée de vie
author: kiratp
manager: jhubbard
editor: ''

ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2016
ms.author: kipandya

---
# Faire expirer des données dans des collections DocumentDB automatiquement avec la durée de vie
Les applications peuvent générer et stocker de grandes quantités de données. Certaines de ces données, telles que les données d’événement générées par la machine, les journaux et les informations de session utilisateur, sont utiles uniquement pendant une certaine période. Une fois les données trop nombreuses par rapport aux besoins de l’application, vous pouvez les vider et réduire les besoins de stockage d’une application.

Avec la « durée de vie » (TTL, Time to Live), Microsoft Azure DocumentDB offre la possibilité de vider automatiquement les documents de la base de données après une période déterminée. La durée de vie par défaut peut être définie au niveau de la collection et être substituée par document. Une fois la TTL définie, en tant que valeur par défaut de la collection ou au niveau du document, DocumentDB supprime automatiquement les documents existant après cette période, en secondes depuis leur dernière modification.

Dans DocumentDB, la durée de vie utilise un décalage par rapport au moment où le document a été modifié pour la dernière fois. Pour ce faire, il utilise le champ \_ts qui existe sur tous les documents. Le champ \_ts est un horodateur d’époque de style Unix représentant la date et l’heure. Le champ \_ts est mis à jour à chaque modification d’un document.

## Comportement de la TTL
La fonction TTL est contrôlée par les propriétés TTL à deux niveaux : au niveau de la collection et au niveau du document. Les valeurs sont définies en secondes et sont traitées en tant qu’écart par rapport à l’horodatage de dernière modification du document (\_ts).

1. DefaultTTL pour la collection
   
   * Si ce paramètre est manquant (ou a la valeur null), les documents ne sont pas supprimés automatiquement.
   * Si ce paramètre est présent et a la valeur « -1 » (infinie), les documents n’expirent pas par défaut.
   * Si ce paramètre est présent et que sa valeur est un nombre (« n »), les documents expirent « n » secondes après la dernière modification.
2. TTL pour les documents :
   
   * La propriété s’applique uniquement si le paramètre DefaultTTL est présent pour la collection parente.
   * Elle remplace la valeur DefaultTTL de la collection parente.

Dès que le document a expiré (TTL + \_ts >= heure actuelle du serveur), le document est marqué comme « expiré ». Aucune opération n’est autorisée sur ces documents une fois ce délai écoulé, et les documents sont exclus des résultats de toutes les requêtes effectuées. Les documents sont physiquement supprimés du système et sont supprimés en arrière-plan de façon opportuniste ultérieurement. Ceci ne consomme aucune [unité de requête](documentdb-request-units.md) du budget de la collection.

La logique ci-dessus peut être représentée dans le tableau suivant :

|  | DefaultTTL manquante/non définie sur la collection | DefaultTTL = -1 sur la collection | DefaultTTL = « n » sur la collection |
| --- |:--- |:--- |:--- |
| TTL manquante sur le document |Rien à substituer au niveau du document, car aucun concept de TTL n’existe pour le document et la collection. |Aucun document de cette collection n’expire. |Les documents de cette collection expireront une fois l’intervalle n écoulé. |
| TTL = -1 sur le document |Rien à substituer au niveau du document, car la collection ne définit pas la propriété DefaultTTL qu’un document peut substituer. La TTL sur un document n’est pas interprétée par le système. |Aucun document de cette collection n’expire. |Le document avec TTL=-1 dans cette collection n’expire jamais. Tous les autres documents expirent après l’intervalle « n ». |
| TTL = n sur le document |Rien à substituer au niveau du document. La TTL sur un document n’est pas interprétée par le système. |Le document avec TTL = n expire après l’intervalle n, en secondes. D’autres documents héritent de l’intervalle -1 et n’expirent jamais. |Le document avec TTL = n expire après l’intervalle n, en secondes. D’autres documents héritent de l’intervalle « n » de la collection. |

## Configuration de la TTL
Par défaut, la durée de vie est désactivée dans toutes les collections DocumentDB et sur tous les documents.

## Activation de la TTL
Pour activer la TTL sur une collection ou sur les documents d’une collection, vous devez définir la propriété DefaultTTL d’une collection sur -1 ou un nombre positif non nul. Si vous définissez DefaultTTL sur -1, tous les documents de la collection auront une durée de vie infinie par défaut. Cependant, le service DocumentDB doit alors surveiller la collection pour identifier les documents pour lesquels cette valeur par défaut a été remplacée.

## Configuration de la TTL par défaut sur une collection
Vous pouvez configurer une durée de vie par défaut au niveau de la collection.

Pour définir la TTL sur une collection, vous devez fournir un nombre positif non nul qui indique, en secondes, le délai d’expiration de tous les documents de la collection après l’horodatage de dernière modification du document (\_ts).

Vous pouvez également définir la valeur par défaut sur -1, ce qui implique que tous les documents insérés dans la collection auront une durée de vie infinie par défaut.

## Définition de la TTL sur un document
En plus de définir une TTL par défaut sur une collection, vous pouvez définir une TTL spécifique au niveau d’un document. Cela remplace la valeur par défaut de la collection.

Pour définir la TTL sur un document, vous devez fournir un nombre positif non nul qui indique, en secondes, le délai d’expiration du document après l’horodatage de dernière modification du document (\_ts).

Pour définir ce décalage d’expiration, définissez le champ TTL sur le document.

Si un document n’a pas de champ TTL, la valeur par défaut de la collection s’applique.

Si la TTL est désactivée au niveau de la collection, le champ TTL du document sera ignoré jusqu’à ce que la TTL soit de nouveau activée sur la collection.

## Extension de la TTL sur un document existant
Vous pouvez réinitialiser la TTL d’un document en effectuant une opération d’écriture quelconque sur le document. Cela définit le champ \_ts selon l’heure actuelle. Le compte à rebours jusqu’à l’expiration du document, tel que défini par la TTL, recommence.

Si vous souhaitez modifier la TTL d’un document, vous pouvez mettre à jour le champ comme n’importe quel autre champ définissable.

## Suppression de la TTL d’un document
Si une TTL a été définie sur un document et que vous ne souhaitez plus que ce document expire, vous pouvez extraire le document, supprimer le champ TTL et replacer le document sur le serveur.

Lorsque le champ TTL est supprimé du document, la valeur par défaut de la collection est appliquée.

Pour éviter qu’un document n’expire et n’hérite de la collection, vous devez définir la valeur de TTL sur -1.

## Désactivation de la TTL
Pour désactiver la TTL entièrement sur une collection et arrêter la recherche de documents expirés par le processus en arrière-plan, vous devez supprimer la propriété DefaultTTL de la collection.

Supprimer cette propriété ne revient pas à la définir sur -1. Si vous la définissez sur -1, les nouveaux documents ajoutés à la collection auront une durée de vie infinie. Cependant, vous pouvez la remplacer sur des documents spécifiques de la collection.

Si vous supprimez cette propriété de la collection entièrement, aucun document n’expirera, même si une valeur par défaut précédente a été explicitement remplacée sur certains documents.

## Forum Aux Questions
**Quel est le coût de la TTL ?**

La définition d’une TTL sur un document n’entraîne aucun coût supplémentaire.

**Combien de temps faut-il pour supprimer un document une fois la TTL activée ?**

Les documents sont marqués comme non disponibles dès leur expiration (TTL + \_ts >= heure actuelle du serveur). Aucune opération n’est autorisée sur ces documents une fois ce délai écoulé, et les documents sont exclus des résultats de toutes les requêtes effectuées. Les documents sont physiquement supprimés par le système en arrière-plan. Ceci ne consomme aucune unité de requête du budget de la collection.

**Si la suppression des documents implique un certain temps, sont-ils pris en compte dans mon quota (et ma facturation) jusqu’à leur suppression ?**

Non. Après l’expiration de documents, leur stockage ne vous est plus facturé, et leur taille n’est plus comptabilisée dans le quota de stockage d’une collection.

**La TTL d’un document a-t-elle un impact sur les frais d’unités de requête ?**

Non. Les opérations effectuées sur un document dans DocumentDB n’auront aucun impact sur les frais d’unités de requête.

**La suppression de documents a-t-elle un impact sur le débit que j’ai approvisionné sur ma collection ?**

Non. Le traitement des requêtes relatives à votre collection est prioritaire sur l’exécution du processus de suppression de vos documents en arrière-plan. L’ajout d’une TTL à un document n’a aucun impact à cet égard.

**Lorsqu’un document expire, combien de temps reste-t-il dans ma collection jusqu’à sa suppression ?**

Dès l’expiration du document, il n’est plus accessible. Le temps exact pendant lequel un document reste dans votre collection avant d’être réellement supprimé n’est pas déterministe. Il est basé sur le moment auquel le processus en arrière-plan est en mesure de supprimer le document.

**Les documents expirés sont-ils supprimés sur tous les nœuds, ou le processus est-il « cohérent » ?**

Le document deviendra indisponible sur tous les nœuds et dans toutes les régions en même temps.

**Les tâches de surveillance de TTL en arrière-plan impliquent-elles un coût d’unités de requête ?**

Non. Cela n’implique aucun coût d’unités de requête.

**À quelle fréquence les expirations de TTL sont-elles vérifiées ?**

La vérification des expirations de TTL ne se produit pas en arrière-plan. Lorsqu’il répond à une requête, le service principal effectue les vérifications inline et exclut tous les documents qui ont expiré. La suppression du document physique est le seul processus exécuté de manière asynchrone en arrière-plan. La fréquence de ce processus est déterminée par les unités de requête disponibles sur la collection.

**La fonctionnalité TTL s’applique-t-elle uniquement à des documents entiers, ou puis-je faire expirer des valeurs de propriété de document individuelles ?**

La TTL s’applique à l’ensemble du document. Si vous souhaitez faire expirer uniquement une partie d’un document, il est recommandé d’extraire la partie du document principal dans un document distinct « lié », puis d’utiliser la TTL sur ce document extrait.

**La fonction TTL impose-t-elle des exigences d’indexation spécifiques ?**

Oui. La collection doit avoir un [jeu de stratégie d’indexation](documentdb-indexing-policies.md) différée ou cohérente. Une erreur se produira si vous tentez de définir le paramètre DefaultTTL sur une collection dont l’indexation est définie sur Aucune et si vous essayez de désactiver l’indexation sur une collection dont le paramètre DefaultTTL est déjà défini.

## Étapes suivantes
Pour en savoir plus sur Azure DocumentDB, consultez la page de [*documentation*](https://azure.microsoft.com/documentation/services/documentdb/) du service.

<!---HONumber=AcomDC_0720_2016-->