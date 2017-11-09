---
title: "Personnaliser la solution d’usine connectée - Azure | Microsoft Docs"
description: "Découvrez comment personnaliser le comportement de la solution préconfigurée d’usine connectée."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 4505023c1869c0bb23e99dc7db1ee6e65466c2cc
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Personnaliser le mode d’affichage des données de vos serveurs OPC UA par la solution d’usine connectée

## <a name="introduction"></a>Introduction

La solution d’usine connectée agrège et affiche les données des serveurs OPC UA qui y sont connectés. Vous pouvez parcourir les serveurs OPC UA et leur envoyer des commandes dans votre solution. Pour plus d’informations sur OPC UA, consultez les [questions fréquentes (FAQ) sur l’usine connectée](iot-suite-faq-cf.md).

Des exemples de données agrégées dans la solution incluent l’efficacité globale des équipements (OEE) et les indicateurs de performance clés (KPI), que vous pouvez afficher dans le tableau de bord au niveau d’une usine, d’une ligne de production et d’un poste. La capture d’écran suivante illustre les valeurs d’OEE et de KPI pour le poste d’assemblage **Assembly** de la ligne de production **Production line 1** dans l’usine de **Munich** :

![Exemple de valeurs d’OEE et de KPI dans la solution][img-oee-kpi]

La solution permet d’afficher des informations détaillées pour des éléments de données spécifiques des serveurs OPC UA appelés *postes*. La capture d’écran suivante illustre des graphiques du nombre d’articles fabriqués à partir d’un poste spécifique :

![Graphiques du nombre d’éléments fabriqués][img-manufactured-items]

Si vous cliquez sur l’un des graphiques, vous pouvez explorer les données plus en détail à l’aide de Time Series Insights (TSI) :

![Explorer les données à l’aide de Time Series Insights][img-tsi]

Cet article aborde les points suivants :

- Comment rendre disponibles les données dans les différentes vues de la solution.
- Comment personnaliser le mode d’affichage des données par la solution.

## <a name="data-sources"></a>Sources de données

La solution d’usine connectée affiche les données des serveurs OPC UA qui y sont connectés. L’installation par défaut inclut plusieurs serveurs OPC UA exécutant une simulation d’usine. Vous pouvez ajouter vos propres serveurs OPC UA qui [se connectent via une passerelle][lnk-connect-cf] à votre solution.

Vous pouvez parcourir les éléments de données qu’un serveur OPC UA peut envoyer à votre solution dans le tableau de bord :

1. Accédez à la vue **Select an OPC UA server** (Sélectionner un serveur OPC UA) :

    ![Accéder à la vue Select an OPC UA server (Sélectionner un serveur OPC UA)][img-select-server]

1. Sélectionnez un serveur et cliquez sur **Connect** (Connexion). Lorsque l’avertissement de sécurité s’affiche, cliquez sur **Proceed** (Continuer).

    > [!NOTE]
    > Cet avertissement s’affiche une seule fois pour chaque serveur et établit une relation d’approbation entre le tableau de bord de la solution et le serveur.

1. Vous pouvez maintenant parcourir les éléments de données que le serveur peut envoyer à la solution. Les éléments qui sont envoyés à la solution présentent une coche verte :

    ![Éléments publiés][img-published]

1. Si vous êtes un *administrateur* dans la solution, vous pouvez choisir de publier un élément de données pour le rendre disponible dans la solution d’usine connectée. En tant qu’administrateur, vous pouvez également modifier la valeur des éléments de données et appeler des méthodes sur le serveur OPC UA.

## <a name="map-the-data"></a>Mapper les données

La solution d’usine connectée mappe et agrège les éléments de données publiés à partir du serveur OPC UA dans les différentes vues de la solution. La solution d’usine connectée se déploie sur votre compte Azure lorsque vous la configurez. Un fichier JSON de la solution Visual Studio d’usine connectée stocke ces informations de mappage. Vous pouvez afficher et modifier ce fichier de configuration JSON dans la solution Visual Studio d’usine connectée. Vous pouvez redéployer la solution une fois que vous apportez une modification.

Vous pouvez utiliser le fichier de configuration pour :

- Modifier les usines, les lignes de production existantes et les postes simulés existants.
- Mapper les données des serveurs OPC UA réels que vous connectez à la solution.

Pour cloner une copie de la solution Visual Studio d’usine connectée, utilisez la commande git suivante :

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

Le fichier **ContosoTopologyDescription.json** définit le mappage entre les éléments de données des serveurs OPC UA et les vues du tableau de bord de la solution d’usine connectée. Ce fichier de configuration se trouve dans le dossier **Contoso\Topology** du projet **WebApp** dans la solution Visual Studio.

Le contenu du fichier JSON est organisé sous forme de hiérarchie de nœuds d’usines, de lignes de production et de postes. Cette hiérarchie définit la hiérarchie de navigation du tableau de bord de l’usine connectée. Les valeurs de chaque nœud de la hiérarchie déterminent les informations affichées dans le tableau de bord. Par exemple, le fichier JSON contient les valeurs suivantes pour l’usine de Munich :

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

Le nom, la description et l’emplacement apparaissent dans cette vue du tableau de bord :

![Données de l’usine de Munich dans le tableau de bord][img-munich]

Chaque usine, ligne de production et poste présente une propriété image. Ces fichiers JPEG se trouvent dans le dossier **Content\img** du projet **WebApp**. Ces fichiers image s’affichent dans le tableau de bord de l’usine connectée.

Chaque poste inclut plusieurs propriétés détaillées qui définissent le mappage à partir des éléments de données OPC UA. Ces propriétés sont décrites dans les sections suivantes :

### <a name="opcuri"></a>OpcUri

La valeur **OpcUri** correspond à l’URI d’application OPC UA qui identifie de façon unique le serveur OPC UA. Par exemple, la valeur **OpcUri** pour le poste d’assemblage de la ligne de production 1 de Munich se présente comme suit : **urn:scada2194:ua:munich:productionline0:assemblystation**.

Vous pouvez afficher l’URI des serveurs OPC UA connectés dans le tableau de bord de la solution :

![Afficher l’URI des serveurs OPC UA][img-server-uris]

### <a name="simulation"></a>Simulation

Les informations que contient le nœud **Simulation** sont propres à la simulation OPC UA exécutée sur les serveurs OPC UA configurés par défaut. Elles ne sont pas utilisées pour un serveur OPC UA réel.

### <a name="kpi1-and-kpi2"></a>Kpi1 et Kpi2

Ces nœuds décrivent la manière dont les données du poste contribuent aux deux valeurs de KPI dans le tableau de bord. Dans un déploiement par défaut, ces valeurs de KPI sont des unités par heure et des kWh. La solution calcule les valeurs de KPI au niveau d’un poste et les agrège au niveau de la ligne de production et de l’usine.

Chaque KPI présente une valeur minimale, maximale et cible. Chaque valeur de KPI peut également définir des actions d’alerte à exécuter par la solution d’usine connectée. L’extrait de code suivant illustre les définitions de KPI pour le poste d’assemblage de la ligne de production 1 à Munich :

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

La capture d’écran suivante illustre les données des KPI dans le tableau de bord.

![Informations des KPI dans le tableau de bord][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

Les nœuds **OpcNodes** identifient les éléments de données publiés à partir du serveur OPC UA et spécifient le mode de traitement de ces données.

La valeur **NodeId** correspond à l’identifiant de nœud OPC UA spécifique du serveur OPC UA. Le premier nœud du poste d’assemblage pour la ligne de production 1 à Munich présente la valeur **ns=2;i=385**. Une valeur **NodeId** spécifie l’élément de données à lire à partir du serveur OPC UA et la valeur **SymbolicName** fournit un nom convivial à utiliser dans le tableau de bord pour ces données.

Les autres valeurs associées à chaque nœud sont résumées dans le tableau suivant :

| Valeur | Description |
| ----- | ----------- |
| Pertinence  | Valeurs de KPI et d’OEE auxquelles ces données contribuent. |
| OpCode     | Mode d’agrégation des données. |
| Units      | Unités à utiliser dans le tableau de bord.  |
| Visible    | Indique si cette valeur doit être affichée dans le tableau de bord. Certaines valeurs sont utilisées dans les calculs mais ne sont pas affichées.  |
| Maximale    | Valeur maximale qui déclenche une alerte dans le tableau de bord. |
| MaximumAlertActions | Action à effectuer en réponse à une alerte (par exemple, envoyer une commande à un poste). |
| ConstValue | Valeur constante utilisée dans un calcul. |

## <a name="deploy-the-changes"></a>Déployer les modifications

Une fois que vous avez apporté toutes les modifications requises au fichier **ContosoTopologyDescription.json**, vous devez redéployer la solution d’usine connectée dans votre compte Azure.

Le référentiel **azure-iot-connected-factory** inclut un script PowerShell **build.ps1** que vous pouvez utiliser pour régénérer et déployer la solution.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la solution préconfigurée d’usine connectée, consultez les articles suivants :

* [Procédure pas à pas de la solution préconfigurée d’usine connectée][lnk-rm-walkthrough]
* [Déployer une passerelle pour une usine connectée][lnk-connect-cf]
* [Autorisations sur le site azureiotsuite.com][lnk-permissions]
* [FAQ sur la fabrique connectée](iot-suite-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md