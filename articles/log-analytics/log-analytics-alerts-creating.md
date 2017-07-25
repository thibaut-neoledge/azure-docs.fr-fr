---
title: "Création d’alertes dans OMS Log Analytics | Microsoft Docs"
description: "Les alertes dans Log Analytics identifient des informations importantes dans votre référentiel OMS et peuvent de façon proactive vous informer sur des problèmes ou appeler des actions pour tenter de les corriger.  Cet article décrit comment créer une règle d’alerte et détaille les différentes actions qu’elle peut engager."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: c34fb7295e8f386f0e7cf2c1db6b26a3e49eae98
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="working-with-alert-rules-in-log-analytics"></a>Utilisation des règles d’alerte dans Log Analytics
Les alertes sont créées par des règles dédiées, qui exécutent automatiquement des recherches de journaux à intervalles réguliers.  Elles créent un enregistrement d’alerte si les résultats correspondent aux critères spécifiques.  La règle peut ensuite exécuter automatiquement une ou plusieurs actions pour vous avertir de l’alerte ou appeler un autre processus de façon proactive.   

Cet article décrit les processus permettant de créer et de modifier des règles d’alerte à l’aide du portail OMS.  Pour plus d’informations sur les différents paramètres et sur l’implémentation de la logique requise, consultez l’article [Présentation des alertes dans Log Analytics](log-analytics-alerts.md).

>[!NOTE]
> Pour le moment, vous ne pouvez pas créer ni modifier une règle d’alerte dans le portail Azure. 

## <a name="create-an-alert-rule"></a>Création d'une règle d'alerte

Pour créer une règle d’alerte à l’aide du portail OMS, commencez par créer une recherche dans les journaux pour trouver les enregistrements qui doivent appeler l’alerte.  Vous pouvez ensuite utiliser le bouton **Alerte** pour créer et configurer la règle d’alerte.

>[!NOTE]
> Un maximum de 250 règles d’alerte peut être créé dans un espace de travail OMS. 

1. Dans la page de présentation d’OMS, cliquez sur **Recherche de journal**.
2. Créez une requête de recherche de journal ou sélectionnez une recherche de journal enregistrée. 
3. Cliquez sur **Alerte** en haut de la page pour ouvrir l’écran **Ajouter une règle d’alerte**.
4. Configurer la règle d’alerte à l’aide des informations indiquées dans la section [Détails des règles d’alerte](#details-of-alert-rules) ci-dessous.
6. Cliquez sur **Enregistrer** pour terminer la règle d’alerte.  Son exécution démarre immédiatement.


## <a name="edit-an-alert-rule"></a>Modifier une règle d’alerte
Vous pouvez obtenir la liste de toutes les règles d’alerte dans le menu **Alertes** des **Paramètres** Log Analytics.  

![Gérer les alertes](./media/log-analytics-alerts/configure.png)

1. Dans la console OMS, sélectionnez la vignette **Paramètres** .
2. Sélectionnez **Alertes**.

Vous pouvez effectuer plusieurs actions à partir de cette vue.

* Désactivez une règle en sélectionnant **Désactivé** en regard de celle-ci.
* Modifiez une règle d’alerte en cliquant sur l’icône en forme de crayon en regard de celle-ci.
* Supprimez une règle d’alerte en cliquant sur l’icône **X** en regard de celle-ci. 

## <a name="details-of-alert-rules"></a>Détails des règles d’alerte
Lorsque vous créez ou modifiez une règle d’alerte dans le portail OMS, vous devez utiliser la page **Ajouter une règle d’alerte** ou **Modifier une règle d’alerte**.  Les tableaux suivants décrivent les champs de cet écran.

![Règle d’alerte](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Informations sur l’alerte
Il s’agit des paramètres de base de la règle d’alerte et de l’alerte qu’elle crée.

| Propriété | Description |
|:--- |:---|
| Nom | Nom unique identifiant la règle d’alerte. Ce nom est inclus dans les alertes créées par la règle.  |
| Description | Description facultative de la règle d’alerte. |
| Severity |Gravité des alertes créées par cette règle. |

### <a name="search-query-and-time-window"></a>Fenêtre de requête et de durée de recherche
Retourne les enregistrements qui sont évalués pour déterminer si des alertes doivent être créées.

| Propriété | Description |
|:--- |:---|
| Requête de recherche | Il s’agit de la requête à exécuter.  Les enregistrements renvoyés par cette requête permettent de déterminer si une alerte a été créée.<br><br>Sélectionnez **Utiliser la requête de recherche actuelle** pour utiliser la requête actuelle ou sélectionnez une recherche enregistrée dans la liste.  La syntaxe de la requête est fournie dans la zone de texte, où vous pouvez la modifier si nécessaire. |
| Fenêtre de temps |Spécifie l’intervalle de temps pour la requête.  La requête retourne uniquement les enregistrements qui ont été créés dans cette plage de l’heure actuelle.  Cela peut être toute valeur comprise entre 5 minutes et 24 heures.  La valeur doit être supérieure ou égale à la fréquence de l’alerte.  <br><br> Par exemple, si la fenêtre de temps est définie sur 60 minutes et que la requête est exécutée à 1h15, seuls les enregistrements créés entre 12h15 et 1h15 apparaissent. |

Quand vous fournissez la fenêtre de temps de règle d’alerte, le nombre d’enregistrements qui satisfont aux critères de recherche pour cette fenêtre s’affiche.  Cela peut vous aider à déterminer la fréquence correspondant au nombre de résultats attendus.

### <a name="schedule"></a>Planification
Définit la fréquence d’exécution de la requête de recherche.

| Propriété | Description |
|:--- |:---|
| Fréquence des alertes | Spécifie la fréquence à laquelle la requête doit être exécutée. Peut être toute valeur comprise entre 5 minutes et 24 heures. La valeur doit être égale ou inférieure à la fenêtre de temps.  Si la valeur est supérieure à celle de la fenêtre de temps, vous risquez de manquer des enregistrements.<br><br>Par exemple, imaginons une fenêtre de temps de 30 minutes associée à une fréquence de 60 minutes.  Si la requête est exécutée à 13 h, les enregistrements entre 12 h 30 et 13 h sont renvoyés.  La requête s’exécute ensuite à 14 h, moment auquel elle renvoie les enregistrements entre 13 h 30 et 14 h.  Les enregistrements créés entre 13 h et 13 h 30 ne seront jamais analysés. |


### <a name="generate-alert-based-on"></a>Générer l’alerte selon
Définit les critères à évaluer dans les résultats de la requête de recherche pour déterminer si une alerte doit être créée.  Ces détails sont différents selon le type de règle d’alerte que vous sélectionnez.  Vous pouvez obtenir des détails correspondant aux différents types de règle d’alerte dans [Présentation des alertes dans Log Analytics](log-analytics-alerts.md).

| Propriété | Description |
|:--- |:---|
| Supprimer les alertes | Quand vous activez la suppression de la règle d’alerte, les actions correspondant à la règle sont désactivées pour une durée définie quand une alerte est créée. La règle est toujours en cours d’exécution et crée des enregistrements d’alerte si les critères sont satisfaits. Cette option vous donne le temps de résoudre le problème sans exécuter des actions en double. |

#### <a name="number-of-results-alert-rules"></a>Règles d’alerte Nombre de résultats

| Propriété | Description |
|:--- |:---|
| Nombre de résultats |Une alerte est créée si le nombre d’enregistrements retournés par la requête est **supérieur** ou **inférieur** à la valeur que vous indiquez.  |

#### <a name="metric-measurement-alert-rules"></a>Règles d’alerte Mesure métrique

| Propriété | Description |
|:--- |:---|
| Valeur de l’agrégat | Valeur de seuil que chaque valeur d’agrégat indiquée dans les résultats doit excéder pour être considérée comme une violation. |
| Déclencher l’alerte selon | Nombre de violations pour qu’une alerte soit créée.  Vous pouvez spécifier **Nombre total de violations** pour obtenir toutes les combinaisons de violations dans les résultats ou **Violations consécutives** pour exiger que les violations aient lieu dans des échantillons consécutifs. |

### <a name="actions"></a>Actions
Les règles d’alerte créent toujours un [enregistrement d’alerte](#alert-records) lorsque le seuil est atteint.  Vous pouvez également définir une ou plusieurs réponses à exécuter, par exemple l’envoi d’un e-mail ou le démarrage d’un runbook.



#### <a name="email-actions"></a>Actions de messagerie
Les actions de messagerie envoient un e-mail avec les détails de l’alerte à un ou plusieurs destinataires.

| Propriété | Description |
|:--- |:---|
| E-mail de notification |Spécifiez **Oui** si vous souhaitez qu’un e-mail soit envoyé quand l’alerte est déclenchée. |
| Objet |Objet de l’e-mail.  Vous ne pouvez pas modifier le corps du message. |
| Destinataires |Adresses de tous les destinataires de l’e-mail.  Si vous spécifiez plusieurs adresses, séparez-les par un point-virgule (;). |

#### <a name="webhook-actions"></a>Actions de webhook
Les actions de webhook permettent d’appeler un processus externe par le biais d’une simple requête HTTP POST.

| Propriété | Description |
|:--- |:---|
| webhook |Spécifiez **Oui** si vous souhaitez appeler un webhook quand l’alerte est déclenchée. |
| URL du webhook |URL du webhook. |
| Inclure la charge utile JSON personnalisée |Sélectionnez cette option si vous souhaitez remplacer la charge utile par défaut par une charge utile personnalisée. |
| Entrez votre charge utile JSON personnalisée |Charge utile personnalisée pour le webhook.  Pour plus d’informations, consultez la section précédente. |

#### <a name="runbook-actions"></a>Actions de runbook
Les actions de runbook démarrent un runbook dans Azure Automation. 

>[!NOTE]
> Pour activer cette action, vous devez avoir installé la solution Automation dans votre espace de travail. 


| Propriété | Description |
|:--- |:---|
| Runbook | Spécifiez **Oui** si vous souhaitez démarrer un runbook Azure Automation quand l’alerte est déclenchée.  |
| Compte Automation | Spécifie le compte Automation dans lequel les runbooks sont sélectionnés.  Il s’agit du compte d’action qui est lié à l’espace de travail. |
| Sélectionner un runbook | Sélectionnez le runbook que vous souhaitez démarrer lorsqu’une alerte est créée. |
| Exécuter sur | Sélectionnez **Azure** pour exécuter le runbook dans le cloud.  Sélectionnez **Worker hybride** pour exécuter le runbook sur un agent sur lequel [Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md ) est installé.  |




## <a name="next-steps"></a>Étapes suivantes
* Installez la [solution de gestion des alertes](log-analytics-solution-alert-management.md) pour analyser les alertes créées dans Log Analytics, ainsi que les alertes collectées à partir de System Center Operations Manager (SCOM).
* Approfondissez vos connaissances sur les [recherches de journal](log-analytics-log-searches.md) pouvant générer des alertes.
* Effectuez une procédure pas à pas pour [configurer un webhook](log-analytics-alerts-webhooks.md) avec une règle d’alerte.  
* Apprenez à écrire des [runbooks dans Azure Automation](https://azure.microsoft.com/documentation/services/automation) pour corriger les problèmes identifiés par des alertes.


