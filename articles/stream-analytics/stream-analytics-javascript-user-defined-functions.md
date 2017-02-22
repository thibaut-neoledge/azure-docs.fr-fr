---
title: "Fonctions JavaScript définies par l’utilisateur Azure Stream Analytics | Microsoft Docs"
description: "Flux de données et balises de capteur IoT avec analyses de flux et traitement des données en temps réel"
keywords: solution iot, guide pratique iot, outils
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 822f0870396e1fa5fe2dfee3bae410aad3da1122
ms.openlocfilehash: 959fd48cb7d7f21385f6eea21f79ad26e8164bc0

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Fonctions JavaScript définies par l’utilisateur Azure Stream Analytics
Azure Stream Analytics prend en charge les fonctions définies par l’utilisateur écrites en JavaScript. Avec le large éventail de méthodes String, RegExp, Math, Array et Date que fournit JavaScript, les transformations complexes de données des travaux Stream Analytics deviennent plus faciles à créer.

## <a name="overview"></a>Vue d'ensemble
Les fonctions JavaScript définies par l’utilisateur prennent en charge les fonctions scalaires de calcul pur sans état qui ne nécessitent pas de connectivité externe. La valeur renvoyée par une fonction ne peut être qu’une valeur (unique) scalaire. Une fois ajoutée à un travail, la fonction peut être utilisée n’importe où dans la requête comme une fonction scalaire prédéfinie.

Voici quelques exemples de scénarios dans lesquels les fonctions JavaScript définies par l’utilisateur vous seront utiles :
* Analyse et manipulation de chaînes avec des fonctions d’expressions régulières, par exemple Regexp_Replace() et Regexp_Extract()
* Décodage de données encodées, par exemple la conversion de binaire en hexadécimal
* Calculs mathématiques avec les fonctions Math de JavaScript
* Opérations de tableaux comme le tri, la jointure, la recherche et le remplissage

Vous ne pouvez pas effectuer les actions suivantes avec une fonction JavaScript définie par l’utilisateur dans Stream Analytics :
* Appel de points de terminaison REST externes, par exemple la recherche inversée d’une IP ou l’extraction de données de référence à partir d’une source externe
* Sérialisation à un format d’événement personnalisé ou désérialisation sur les entrées/sorties
* Agrégats personnalisés

Bien qu’elles ne soient pas bloquées dans la définition des fonctions, évitez d’utiliser des fonctions telles que Date.GetDate() ou Math.random(). Ces fonctions ne retournent **pas** le même résultat à chaque fois que vous les appelez et le service Azure Stream Analytics ne conserve pas de journal des appels de fonction et des résultats renvoyés. Par conséquent, si une fonction retourne un résultat différent sur le(s) même(s) événement(s), la reproductibilité n’est pas garantie lorsqu’un travail est relancé par vos soins ou par le service Stream Analytics.

## <a name="adding-a-javascript-udf-from-azure-portal"></a>Ajouter une fonction JavaScript définie par l’utilisateur à partir du Portail Azure
Pour créer une fonction JavaScript simple définie par l’utilisateur dans un travail Stream Analytics existant, suivez ces étapes.

1.  Ouvrez le portail Azure.

2.  Localisez votre travail Stream Analytics, puis cliquez sur le lien des fonctions sous **TOPOLOGIE DU TRAVAIL**.
 
3.  La liste des fonctions existantes, vide, apparaît ; cliquez sur l’icône **Ajouter** pour ajouter une nouvelle fonction définie par l’utilisateur.

4.  Sur le panneau **Nouvelle fonction**, sélectionnez le Type de fonction JavaScript ; un modèle de fonction par défaut apparaît dans l’éditeur.
 
5.  Ajoutez l’alias _hex2Int_ à la fonction définie par l’utilisateur et modifiez l’implémentation de la fonction de la façon suivante :

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Cliquez sur le bouton **Enregistrer** ; votre fonction s’affiche dans la liste des fonctions. 

7.  Cliquez sur la nouvelle fonction **hex2Int** pour vérifier la définition de la fonction. Notez qu’un préfixe « UDF » est ajouté devant l’alias de chaque fonction. Vous devez appeler la fonction **avec le préfixe** dans votre requête Stream Analytics, dans ce cas **UDF.hex2Int**.
 
## <a name="calling-javascript-udf-in-a-query"></a>Appeler une fonction JavaScript définie par l’utilisateur dans une requête

1. Ouvrez l’éditeur de requêtes en cliquant sur **Requête** sous **TOPOLOGIE DU TRAVAIL**. 

2.  Modifiez votre requête et appelez la fonction définie par l’utilisateur que vous venez d’ajouter de la façon suivante :

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Cliquez avec le bouton droit sur l’entrée du travail pour charger l’exemple de fichier de données. 
 
4.  Cliquez sur **Test** pour tester votre requête.


## <a name="supported-javascript-objects"></a>Objets JavaScript pris en charge
La fonction JavaScript définie par l’utilisateur Azure Stream Analytics prend en charge les objets prédéfinis standard du langage JavaScript. Pour connaître la liste des objets, reportez-vous au document sur les [Objets globaux](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversion de type Stream Analytics et JavaScript

Il existe des différences entre les types pris en charge dans le langage de requête Stream Analytics et JavaScript. Le tableau suivant répertorie les mappages de conversion entre les deux.


---
Stream Analytics | JavaScript
--- | ---
bigint | Number (à noter que JavaScript ne peut représenter les entiers que jusqu'à 2^53 précisément)
DateTime | Date (à noter que JavaScript ne prend en charge que les millisecondes) 
double | Number
nvarchar(MAX) | String
Enregistrement | Object
Tableau | Tableau
NULL | Null


Les conversions de JavaScript en ASA sont également listées.

---
JavaScript | Stream Analytics
--- | ---
Number | Bigint si le nombre est rond et entre long.MinValue et long.MaxValue, double sinon
Date | DateTime
String | nvarchar(MAX)
Object | Enregistrement
Tableau | Tableau
Null, Undefined | NULL
tout autre type, par exemple Function, Error, etc. | Non pris en charge - erreur d’exécution

## <a name="troubleshooting"></a>Résolution de problèmes
Les erreurs d’exécution JavaScript seront considérées comme irrécupérables et remontées par le biais du journal d’activité. Pour récupérer le journal à partir du Portail Azure, accédez à votre travail et cliquez sur **Journal d’activité**.
 

## <a name="other-javascript-udf-usage-patterns"></a>Autres modèles d’utilisation des fonctions JavaScript définies par l’utilisateur

### <a name="writing-nested-json-to-output"></a>Écrire du code JSON imbriqué en sortie
Écrire une chaîne JSON en sortie est une tâche courante lorsque vous avez une étape de traitement de suivi qui prend la sortie du travail Stream Analytics en entrée et exige le format JSON. L’exemple suivant appelle la fonction JSON.stringify() pour compresser toutes les paires nom/valeur de l’entrée et les écrire sous la forme d’une valeur de chaîne unique en sortie. 

### <a name="javascript-udf-definition"></a>Définition de la fonction JavaScript définie par l’utilisateur

```
function main(x) {
return JSON.stringify(x);
}
```

**Exemple de requête :**
```
SELECT 
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--HONumber=Feb17_HO1-->


