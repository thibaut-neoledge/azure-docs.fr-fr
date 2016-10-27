<properties
    pageTitle="Surveiller les Surface Hubs avec Log Analytics | Microsoft Azure"
    description="La solution Surface Hub permet de suivre l’intégrité de vos Surface Hubs et de comprendre comment ils sont utilisés."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="monitor-surface-hubs-with-log-analytics"></a>Surveiller les Surface Hubs avec Log Analytics

Cet article décrit comment utiliser la solution Surface Hub dans Log Analytics pour surveiller des appareils Microsoft Surface Hub avec Microsoft Operations Management Suite (OMS). Log Analytics vous aide à suivre l’intégrité de vos Surface Hubs ainsi qu’à comprendre comment ils sont utilisés.

Chaque Surface Hub a un Microsoft Monitoring Agent installé. C’est via cet agent que vous pouvez envoyer des données de votre Surface Hub à OMS. Les fichiers journaux sont lus à partir de vos Surface Hubs, puis envoyés au Service OMS. Des problèmes tels que des serveurs hors connexion, un calendrier qui ne se synchronise pas, ou un compte d’appareil incapable de se connecter à Skype s’affichent sur le tableau de bord Surface Hub d’OMS. Les données du tableau de bord vous permettent d’identifier les appareils qui ne sont pas en cours d’exécution ou qui rencontrent d’autres problèmes, voire d’appliquer des correctifs pour les problèmes détectés.


## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution

Utilisez les informations suivantes pour installer et configurer la solution. Pour gérer vos Surface Hubs à partir de Microsoft Operations Management Suite (OMS), vous devez disposer des éléments suivants :

- Un abonnement valide à [OMS](http://www.microsoft.com/oms).
- Un niveau d’[abonnement OMS](https://azure.microsoft.com/pricing/details/log-analytics/) prenant en charge le nombre d’appareils à analyser. La tarification d’OMS varie selon le nombre d’appareils inscrits et la quantité de données traitées. Vous devez prendre cela en compte lors de la planification de votre déploiement de Surface Hub.

Ensuite, vous allez soit ajouter un abonnement OMS à votre abonnement Microsoft Azure, soit créer un espace de travail directement via le portail OMS. Pour des instructions détaillées sur ces deux méthodes, voir [Prise en main de Log Analytics](log-analytics-get-started.md). Une fois l’abonnement OMS créé, vous pouvez inscrire vos appareils Surface Hub de deux façons :

- automatiquement via InTune ;
- manuellement, via les **Paramètres** de votre appareil Surface Hub.

## <a name="set-up-monitoring"></a>Configurez l’analyse

Vous pouvez analyser l’intégrité et l’activité de votre Surface Hub à l’aide de Log Analytics dans OMS. Vous pouvez inscrire le Surface Hub dans OMS via InTune, ou localement via les **Paramètres** du Surface Hub.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Connecter des Surface Hubs à OMS via InTune

Vous devez disposer de l’ID et de la clé de l’espace de travail OMS devant gérer vos Surface Hubs. Vous pouvez les obtenir à partir du portail OMS.

InTune est un produit Microsoft permettant de gérer de manière centralisée les paramètres de configuration OMS appliqués à un ou plusieurs de vos appareils. Pour configurer vos appareils via InTune, procédez comme suit :

1. Connectez-vous à InTune.
2. Accédez à **Paramètres** > **Sources connectées**.
3. Créez ou modifiez une stratégie basée sur le modèle Surface Hub.
4. Accédez à la section OMS (Azure Operational Insights) de la stratégie, puis ajoutez l’*ID de l’espace de travail* et la *Clé de l’espace de travail* à la stratégie.
5. Enregistrez la stratégie.
6. Associez la stratégie au groupe approprié d’appareils.

  ![Stratégie InTune](./media/log-analytics-surface-hubs/intune.png)

InTune synchronise ensuite les paramètres OMS avec les appareils du groupe cible, en inscrivant ceux-ci dans votre espace de travail OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Connecter des Surface Hubs à OMS en utilisant l’application Paramètres

Vous devez disposer de l’ID et de la clé de l’espace de travail OMS devant gérer vos Surface Hubs. Vous pouvez les obtenir à partir du portail OMS.

Si vous n’utilisez pas InTune pour gérer votre environnement, vous pouvez inscrire des appareils manuellement via les **Paramètres** de chaque Surface Hub :

1. Sur votre Surface Hub, ouvrez **Paramètres**.
2. Entrez les informations d’identification d’administrateur de l’appareil lorsque vous y êtes invité.
3. Cliquez sur **Cet appareil**, puis, sous **Analyse**, cliquez sur **Configurer les paramètres OMS**.
4. Sélectionnez **Activer l’analyse**.
6. Dans la boîte de dialogue Paramètres OMS, tapez l’**ID de l’espace de travail** et la **Clé de l’espace de travail**.  
  ![Paramètres](./media/log-analytics-surface-hubs/settings.png)
7. Cliquez sur **OK** pour achever la configuration.

Une confirmation s’affiche, vous indiquant si la configuration OMS a été correctement appliquée à l’appareil. Si c’est le cas, un message s’affiche, indiquant que l’agent est bien connecté au service OMS. L’appareil commence alors à envoyer des données à OMS, où vous pouvez les consulter et les utiliser.

## <a name="monitor-surface-hubs"></a>Analyser les Surface Hubs

L’analyse de vos Surface Hubs à l’aide d’OMS est très similaire à l’analyse d’autres appareils inscrits.

1. Connectez-vous au portail OMS.
2. Accédez au tableau de bord du pack de solutions Surface.
3. L’intégrité de votre appareil s’affiche.

  ![Tableau de bord de Surface Hub](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Vous pouvez créer des [alertes](log-analytics-alerts.md) basées sur des recherches de journal existantes ou personnalisées. En utilisant les données qu’OMS collecte à partir de vos Surface Hubs, vous pouvez rechercher des problèmes et générer des alertes sur les conditions que vous définissez pour vos appareils.


## <a name="next-steps"></a>Étapes suivantes

- Utiliser des [recherches de journal dans Log Analytics](log-analytics-log-searches.md) pour afficher des données détaillées de Surface Hub.
- Créer des [alertes](log-analytics-alerts.md) pour être averti en cas de problèmes avec vos Surface Hubs.



<!--HONumber=Oct16_HO2-->


