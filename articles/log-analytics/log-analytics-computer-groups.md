<properties
    pageTitle="Groupes d’ordinateurs dans les recherches de journal Log Analytics | Microsoft Azure"
    description="Les groupes d’ordinateurs dans Log Analytics vous permettent d’étendre des recherches de journal à un ensemble spécifique d’ordinateurs.  Cet article décrit les différentes méthodes permettant de créer des groupes d’ordinateurs et la manière de les utiliser dans une recherche de journal."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="bwren"/>


# <a name="computer-groups-in-log-analytics-log-searches"></a>Groupes d’ordinateurs dans les recherches de journal Log Analytics
Les groupes d’ordinateurs dans Log Analytics vous permettent d’étendre des [recherches de journal](log-analytics-log-searches.md) à un ensemble spécifique d’ordinateurs.  Vous peuplez chaque groupe d’ordinateurs soit à l’aide d’une requête que vous définissez, soit en important des groupes à partir de différentes sources.  Quand un groupe est inclus dans une recherche de journal, les résultats sont limités aux enregistrements correspondant aux ordinateurs du groupe.

## <a name="creating-a-computer-group"></a>Création d’un groupe d’ordinateurs
Vous pouvez créer un groupe d’ordinateurs dans Log Analytics en utilisant l’une des méthodes répertoriées dans le tableau suivant.  Des détails sur chaque méthode sont fournis dans les sections ci-dessous. 

| Méthode | Description |
|:---|:---|
| Recherche de journal       | Créer une recherche de journal qui retourne une liste d’ordinateurs, puis enregistrer les résultats en tant que groupe d’ordinateurs. |
| API Recherche de journal   | Utiliser l’API Recherche de journal pour créer par programme un groupe d’ordinateurs basé sur les résultats d’une recherche de journal. |
| Active Directory | Analyser automatiquement l’appartenance au groupe de tous les ordinateurs agents membres d’un domaine Active Directory, et créer un groupe dans Log Analytics pour chaque groupe de sécurité.
| WSUS              | Analyser automatiquement des serveurs ou clients WSUS pour des groupes de ciblage, et créer un groupe pour chacun d’eux dans Log Analytics. |


### <a name="log-search"></a>Recherche de journaux

Les groupes d’ordinateurs créés à partir d’une recherche de journal contiennent tous les ordinateurs retournés par une requête de recherche que vous définissez.  Cette requête est exécutée chaque fois que le groupe d’ordinateurs est utilisé, de façon à refléter toutes les modifications apportées depuis la création du groupe.

Utilisez la procédure suivante pour créer un groupe d’ordinateurs à partir d’une recherche de journal.

1. [Créez une recherche de journal](log-analytics-log-searches.md) qui retourne une liste d’ordinateurs.  La recherche doit retourner un ensemble distinct d’ordinateurs en utilisant quelque chose comme **Distinct Computer** ou **measure count() by Computer** dans la requête.  
2. Cliquez sur le bouton **Enregistrer** en haut de l’écran.
3. Sélectionnez **Oui** à **Enregistrer cette requête comme groupe d’ordinateurs**.
4. Tapez un **Nom** et une **Catégorie** pour le groupe.  S’il existe déjà une recherche du même nom et de la même catégorie, vous êtes invité à la remplacer.  Vous pouvez avoir plusieurs recherches du même nom dans des catégories différentes. 

Voici des exemples de recherches que vous pouvez enregistrer en tant que groupe d’ordinateurs.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>API Recherche de journal

Les groupes d’ordinateurs créés avec l’API Recherche de journal sont les mêmes que les recherches créées avec une Recherche de journal.

Pour plus d’informations sur la création d’un groupe d’ordinateurs à l’aide de l’API Recherche de journal, voir [Groupes d’ordinateur dans l’API REST de recherche de journal Log Analytics](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory

Lorsque vous configurez Log Analytics pour importer les appartenances aux groupes Active Directory, Log Analytics analyse l’appartenance au groupe des ordinateurs joints à un domaine avec l’Agent OMS.  Un groupe d’ordinateurs est créé dans Log Analytics pour chaque groupe de sécurité dans Active Directory, et chaque ordinateur est ajouté aux groupes d’ordinateurs correspondant aux groupes de sécurité auxquels il appartient.  Cet appartenance est mise à jour toutes les 4 heures.  

Vous configurez Log Analytics pour importer les groupes de sécurité Active Directory à partir du menu **Groupes d’ordinateurs** dans les **Paramètres** de Log Analytics.  Sélectionnez **Automation** puis **Importer les appartenances à des groupes Active Directory depuis les ordinateurs**.  Aucune configuration supplémentaire n’est requise.

![Groupes d’ordinateurs d’Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Une fois des groupes importés, le menu répertorie le nombre d’ordinateurs détectés avec une appartenance à un groupe et le nombre de groupes importés.  Vous pouvez cliquer sur l’un de ces liens pour retourner les enregistrements **ComputerGroup**avec ces informations.

### <a name="windows-server-update-service"></a>Windows Server Update Service

Lorsque vous configurez Log Analytics pour importer les appartenances aux groupes WSUS, Log Analytics analyse les appartenances aux groupes de ciblage de tous les ordinateurs avec l’Agent OMS.  Si vous utilisez un ciblage côté client, l’appartenance à un groupe de tout ordinateur connecté à OMS et faisant partie d’un groupe de ciblage WSUS est importée dans Log Analytics. Si vous utilisez un ciblage côté client, l’Agent OMS doit être installé sur le serveur WSUS afin que les informations d’appartenance au groupe soient importées dans OMS.  Cet appartenance est mise à jour toutes les 4 heures. 

Vous configurez Log Analytics pour importer les groupes de sécurité Active Directory à partir du menu **Groupes d’ordinateurs** dans les **Paramètres** de Log Analytics.  Sélectionnez **Active Directory** puis **Importer les appartenances à des groupes Active Directory depuis les ordinateurs**.  Aucune configuration supplémentaire n’est requise.

![Groupes d’ordinateurs d’Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Une fois des groupes importés, le menu répertorie le nombre d’ordinateurs détectés avec une appartenance à un groupe et le nombre de groupes importés.  Vous pouvez cliquer sur l’un de ces liens pour retourner les enregistrements **ComputerGroup**avec ces informations.

## <a name="managing-computer-groups"></a>Gestion de groupes d’ordinateurs

Vous pouvez afficher les groupes d’ordinateurs créés à partir d’une recherche de journal ou de l’API Recherche de journal via le menu **Groupes d’ordinateurs** dans les **Paramètres** de Log Analytics.  Cliquez sur le signe **x** dans la colonne **Supprimer** pour supprimer le groupe d’ordinateurs.  Cliquez sur l’icône **Afficher les membres** correspondant à un groupe pour exécuter la recherche de journal du groupe qui retourne les membres de celui-ci. 

![Groupes d’ordinateurs enregistrés](media/log-analytics-computer-groups/configure-saved.png)

Pour modifier le groupe, créez un groupe de la même **Catégorie** et du même **Nom** pour remplacer le groupe d’origine.

## <a name="using-a-computer-group-in-a-log-search"></a>Utilisation d’un groupe d’ordinateurs dans une recherche de journal
Vous utilisez la syntaxe suivante pour faire référence à un groupe d’ordinateurs dans une recherche de journal.  La spécification de la **Catégorie** est facultative. Elle n’est requise que si vous avez des groupes d’ordinateurs du même nom dans des catégories différentes. 

    $ComputerGroups[Category: Name]

Lors de l’exécution d’une recherche, les membres de tous les groupes d’ordinateurs inclus dans la recherche sont résolus en premier.  Si le groupe est basé sur une recherche de journal, celle-ci est exécutée pour retourner les membres du groupe avant d’effectuer la recherche de journal de niveau supérieur.

Les groupes d’ordinateurs sont généralement utilisés avec la clause **IN**dans la recherche de journal, comme dans l’exemple suivant.

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>Enregistrements de groupe d’ordinateurs

Un enregistrement est créé dans le référentiel OMS pour chaque appartenance à un groupe d’ordinateur créée à partir d’Active Directory ou de WSUS.  Ces enregistrements sont de type **ComputerGroup** et ont les propriétés décrites dans le tableau suivant.  Des enregistrements ne sont pas créés pour des groupes d’ordinateurs basés sur des recherches de journal.

| Propriété | Description |
|:--|:--|
| Type                | *ComputerGroup* |
| SourceSystem        | *SourceSystem*  |
| Ordinateur            | Nom de l’ordinateur membre. |
| Groupe               | Nom du groupe. |
| GroupFullName       | Chemin d’accès complet au groupe, incluant la source et le nom de la source.
| GroupSource         | Source à partir de laquelle ce groupe a été collecté. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName     | Nom de la source à partir de laquelle les groupes ont été collectées.  Pour Active Directory, il s’agit du nom de domaine. |
| ManagementGroupName | Nom du groupe d'administration pour les agents SCOM.  Pour les autres agents, il s’agit d’AOI-\<workspace ID\> |
| TimeGenerated       | Date et heure de création ou de mise à jour du groupe d’ordinateurs. |



## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [recherches de journal](log-analytics-log-searches.md) pour analyser les données collectées dans des sources de données et des solutions.  


<!--HONumber=Oct16_HO2-->


