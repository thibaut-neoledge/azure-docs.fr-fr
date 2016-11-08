---
title: Exemple de webhook d’alerte dans Log Analytics
description: Une des actions que vous pouvez exécuter en réponse à une alerte Log Analytics est un *webhook*, qui vous permet d’appeler un processus externe par le biais d’une requête HTTP unique. Cet article décrit un exemple de création d’une action de webhook dans une alerte Log Analytics à l’aide de Slack.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren

---
# <a name="webhooks-in-log-analytics-alerts"></a>Webhooks dans les alertes Log Analytics
Une des actions que vous pouvez exécuter en réponse à une [alerte Log Analytics](log-analytics-alerts.md) est un *webhook*, qui vous permet d’appeler un processus externe par le biais d’une requête HTTP unique.  Vous pouvez consulter les détails des alertes et des webhooks dans [Alertes dans Log Analytics](log-analytics-alerts.md)

Dans cet article, nous allons découvrir un exemple de création d’une action de webhook dans une alerte Log Analytics à l’aide du service de messagerie Slack.

> [!NOTE]
> Vous devez disposer d’un compte Slack pour suivre cet exemple.  Vous pouvez ouvrir un compte gratuit à l’adresse [slack.com](http://slack.com).
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>Étape 1 : Activer les webhooks dans Slack
1. Connectez-vous à Slack à l’adresse [slack.com](http://slack.com).
2. Sélectionnez un canal dans la section **Channels** (Canaux) dans le volet gauche.  Il s’agit du canal auquel le message sera envoyé.  Vous pouvez sélectionner un des canaux par défaut tels que **general** (général) ou **random** (aléatoire).  Dans un scénario de production, vous créeriez probablement un canal spécial tel que **alertes_service_critiques**. <br>
   
   ![Canaux Slack](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. Cliquez sur **Add an app or custom integration** (Ajouter une application ou une intégration personnalisée) pour ouvrir l’annuaire d’applications.
4. Tapez *webhooks* dans la zone de recherche, puis sélectionnez **Incoming WebHooks**(Webhooks entrants). <br>
   
   ![Canaux Slack](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. Cliquez sur **Installer** en regard du nom de votre équipe.
6. Cliquez sur **Ajouter une configuration**.
7. Sélectionnez le canal que vous allez utiliser pour cet exemple, puis cliquez sur **Add Incoming WebHooks integration**(Ajouter l’intégration des Webhooks entrants).  
8. Copiez l’ **URL du webhook**.  Vous la collerez dans la configuration de l’alerte. <br>
   
    ![Canaux Slack](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Étape 2 : Créer une règle d’alerte dans Log Analytics
1. [Créez une règle d’alerte](log-analytics-alerts.md) avec les paramètres suivants.
   * Requête : ```    Type=Event EventLevelName=error ```
   * Vérifier cette alerte toutes les : 5 minutes
   * The number of results is (Le nombre de résultats est) : supérieur à 10
   * Over this time window (Pendant cette fenêtre de temps) : 60 minutes
   * Sélectionnez **Oui** pour **Webhook** et **Non** pour les autres actions.
2. Collez l’URL Slack dans le champ **URL du webhook** .
3. Sélectionnez l’option permettant d’ **inclure une charge utile JSON personnalisée**.
4. Slack attend une charge utile au format JSON avec un paramètre nommé *text*.  Il s’agit du texte qu’il affiche dans le message qu’il crée.  Vous pouvez utiliser un ou plusieurs des paramètres d’alerte à l’aide du symbole *#* , comme dans l’exemple suivant.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![exemple de charge utile JSON](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. Cliquez sur **Enregistrer** pour enregistrer la règle d’alerte.
6. Attendez qu’une alerte soit créée, puis, dans Slack, recherchez un message similaire au message ci-après.
   
   ![exemple de webhook dans Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Charge utile de webhook avancée pour Slack
Vous disposez d’une marge de manœuvre importante pour personnaliser les messages entrants avec Slack. Pour plus d’informations, consultez [Incoming Webhooks](https://api.slack.com/incoming-webhooks) (Webhooks entrants) sur le site web Slack. Voici une charge utile plus complexe pour créer un message enrichi avec mise en forme :

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


Le message généré dans Slack ressemble à celui ci-dessous.

![exemple de message dans Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Résumé
Une fois cette règle d’alerte en place, un message est envoyé à Slack chaque fois que les critères sont satisfaits.  

Cela n’est qu’un exemple d’une action que vous pouvez créer en réponse à une alerte.  Vous pouvez créer une action de webhook qui appelle un autre service externe, une action de runbook pour démarrer un runbook dans Azure Automation ou une action de messagerie pour envoyer un message à vous-même ou à d’autres destinataires.   

## <a name="next-steps"></a>Étapes suivantes
* Poursuivez la découverte des [alertes dans Log Analytics](log-analytics-alerts.md) , notamment d’autres actions.
* [Créez des runbooks dans Azure Automation](../automation/automation-webhooks.md) qui peuvent être appelés à partir d’un webhook.

<!--HONumber=Oct16_HO2-->


