---
title: "Utilisation de l’extensibilité Python avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit une vue d’ensemble et des exemples détaillés de l’utilisation du code Python pour étendre les fonctionnalités de préparation des données"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4e1935a7830b8174796ac12792fbbc0ed110d081
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="data-prep-python-extensions"></a>Extensions Python de préparation des données
Afin de combler les lacunes de fonctionnalité qui existent entre les fonctions intégrées, la préparation des données offre des possibilités d’extension à plusieurs niveaux. Ce document décrit l’extensibilité via un script Python. 

## <a name="custom-code-steps"></a>Étapes de code personnalisé 
La préparation des données comporte les étapes personnalisées suivantes, dans le cadre desquelles les utilisateurs peuvent écrire du code : 
1. File Reader*
2. Writer*
3. Add Column
4. Advanced Filter
5. Transform Dataflow
6. Transform Partition

*À l’heure actuelle, ces étapes ne sont pas prises en charge dans une exécution Spark. 

## <a name="code-block-types"></a>Types de blocs de code 
Pour chacune de ces étapes, nous prenons en charge deux types de blocs de code. Tout d’abord, nous prenons en charge une expression Python simple exécutée telle quelle. Deuxièmement, nous prenons en charge un module Python où nous appelons une fonction particulière avec une signature connue dans le code que vous fournissez.

Par exemple, vous pouvez ajouter une nouvelle colonne qui calcule le journal d’une autre colonne des deux manières suivantes : Expression : 

```python    
    math.log(row["Score"])
```

Module : 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


La transformation Add Column en mode module s’attend à trouver une fonction nommée `newvalue` qui accepte une variable de ligne et renvoie la valeur de la colonne. Ce module peut contenir n’importe quelle quantité de code Python avec d’autres fonctions, des importations, etc. 

Les détails de chaque point d’extension sont décrits dans les sections suivantes : 

## <a name="imports"></a>Importations 
Si vous utilisez le type de bloc Expression, vous pouvez toujours ajouter des instructions d’importation à votre code, mais celles-ci doivent toutes être regroupées sur les premières lignes de votre code. Utilisation correcte : 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Error:  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Si vous utilisez le type de bloc Module, vous pouvez suivre toutes les règles Python normales d’utilisation de l’instruction « import ». 

## <a name="default-imports"></a>Importations par défaut
Les importations suivantes sont toujours incluses et utilisables dans votre code. Il est inutile de les importer à nouveau. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="installing-new-packages"></a>Installation de nouveaux packages
Pour utiliser un package qui n’est pas installé par défaut, vous devez tout d’abord l’installer dans les environnements utilisés par la préparation des données. Cette installation doit être effectuée à la fois sur votre ordinateur local et sur les cibles de calcul que vous souhaitez éventuellement utiliser.

Pour installer les packages dans une cible de calcul, vous devez modifier le fichier conda_dependencies.yml situé dans le dossier aml_config, sous la racine de votre projet.

### <a name="windows"></a>Windows 
Pour rechercher l’emplacement dans Windows, la méthode consiste à trouver l’installation de Python propre à l’application et son répertoire de scripts, la valeur par défaut étant :  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts.` 

Exécutez ensuite l’une des commandes suivantes : 

`conda install <libraryname>` 

ou 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Pour rechercher l’emplacement sur un Mac, la méthode consiste à trouver l’installation de Python propre à l’application et son répertoire de scripts, la valeur par défaut étant : 

`/Users/<user>/Library/Caches/AmlWorkbench>/Python/bin` 

Exécutez ensuite l’une des commandes suivantes : 

`./conda install <libraryname>`

ou 

`./pip install <libraryname>`

## <a name="column-data"></a>Données de la colonne 
Les données de la colonne sont accessibles à partir d’une ligne à l’aide de la notation par point ou de la notation clé-valeur. Les noms de colonnes qui contiennent des espaces ou des caractères spéciaux ne sont pas accessibles à l’aide de la notation par points. La variable `row` doit toujours être définie dans les deux modes des extensions Python (Module et Expression). 

Exemples : 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>File reader 
### <a name="purpose"></a>Objectif 
Ce point d’extension vous permet de contrôler intégralement le processus de lecture d’un fichier dans un flux de données. Le système appelle votre code, transmet la liste des fichiers que vous devez traiter, et votre code doit alors créer et renvoyer une trame de données Pandas. 

>[!NOTE]
>Ce point d’extension ne fonctionne pas dans Spark. 


### <a name="how-to-use"></a>Utilisation 
Vous accédez à ce point d’extension à partir de l’Assistant Ouvrir la source de données. Choisissez Fichier sur la première page, puis choisissez l’emplacement de votre fichier. Dans la page « Choisir les paramètres de fichier », déroulez la liste Type de fichier et choisissez « Fichier personnalisé (Script) ». 

Votre code reçoit une trame de données Pandas nommée « df », qui contient des informations sur les fichiers que vous devez lire. Si vous choisissez d’ouvrir un répertoire qui contient plusieurs fichiers, la trame de données contiendra plusieurs lignes.  

Cette trame de données comporte les colonnes suivantes : 
- Path : le fichier à lire.
- PathHint : indique l’emplacement du fichier. Valeurs : « Local », « AzureBlobStorage », « AzureDataLakeStorage »
- AuthenticationType : type d’authentification utilisé pour accéder au fichier. Valeurs : « None », « SasToken », « OAuthToken »
- AuthenticationValue : contient la valeur None (aucun) ou le jeton à utiliser.

### <a name="syntax"></a>Syntaxe 
Expression : 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Module :  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Writer 
### <a name="purpose"></a>Objectif 
Le point d’extension writer vous permet de contrôler intégralement le processus d’écriture de données à partir d’un flux de données. Le système appelle votre code et transmet une trame de données que votre code peut utiliser pour écrire des données de la manière que vous souhaitez. 

>[!NOTE]
>Le point d’extension writer ne fonctionne pas dans Spark. 


### <a name="how-to-use"></a>Utilisation 
Vous pouvez ajouter ce point d’extension à l’aide du bloc « Write Dataflow (Script) ». Celui-ci est disponible dans le menu de transformations de niveau supérieur. 

### <a name="syntax"></a>Syntaxe 
Expression : 

```python
    df.to_csv('c:\\temp\\output.csv')
```

Module :

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Ce bloc d’écriture personnalisé peut exister au milieu d’une liste d’étapes. Ainsi, si vous utilisez un module, votre fonction d’écriture doit renvoyer la trame de données qui constitue l’entrée de l’étape qui suit. 

## <a name="add-column"></a>Add column 
### <a name="purpose"></a>Objectif
Ce point d’extension vous permet d’écrire le code Python permettant de calculer une nouvelle colonne. Le code que vous écrivez a accès à la ligne entière. Il doit renvoyer une nouvelle valeur de colonne à chaque ligne. 

### <a name="how-to-use"></a>Utilisation
Vous pouvez ajouter ce point d’extension à l’aide du bloc « Add Column (Script) ». Celui-ci est disponible dans le menu de transformations de niveau supérieur, ainsi que dans le menu contextuel de la colonne. 

### <a name="syntax"></a>Syntaxe
Expression : 

```python
    math.log(row["Score"])
```

Module : 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Advanced filter
### <a name="purpose"></a>Objectif 
Ce point d’extension vous permet d’écrire un filtre personnalisé. Vous avez accès à la ligne entière et votre code doit renvoyer la valeur True (inclure la ligne) ou False (exclure la ligne). 

### <a name="how-to-use"></a>Utilisation
Vous pouvez ajouter ce point d’extension à l’aide du bloc « Advanced Filter (Script) ». Celui-ci est disponible dans le menu de transformations de niveau supérieur. 

### <a name="syntax"></a>Syntaxe

Expression : 

```python
    row["Score"] > 95
```

Module :  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Transform dataflow
### <a name="purpose"></a>Objectif 
Ce point d’extension vous permet de transformer intégralement le flux de données. Vous avez accès à une trame de données Pandas qui contient toutes les colonnes et toutes les lignes qui font l’objet de votre traitement, et votre code doit renvoyer une trame de données Pandas avec les nouvelles données. 

>[!NOTE]
>Dans Python, toutes les données doivent être chargées en mémoire dans une trame de données Pandas si cette extension est utilisée. 

Dans Spark, toutes les données sont collectées sur un nœud Worker unique. Cela peut entraîner l’épuisement de la mémoire pour un Worker si les données sont très volumineuses. Utilisez-le avec précaution.

### <a name="how-to-use"></a>Utilisation 
Vous pouvez ajouter ce point d’extension à l’aide du bloc « Transform DataFlow (Script) ». Celui-ci est disponible dans le menu de transformations de niveau supérieur. 
### <a name="syntax"></a>Syntaxe 

Expression : 

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Module : 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Transform partition  
### <a name="purpose"></a>Objectif 
Ce point d’extension vous permet de transformer une partition du flux de données. Vous avez accès à une trame de données Pandas qui contient toutes les colonnes et lignes de cette partition, et votre code doit renvoyer une trame de données Pandas avec les nouvelles données. 

>[!NOTE]
>Dans Python, vous pouvez obtenir une partition unique ou plusieurs partitions, en fonction de la taille de vos données. Dans Spark, vous travaillez sur une trame de données qui contient les données d’une partition sur un nœud Worker donné. Dans les deux cas, vous ne pouvez pas partir du principe que vous avez accès au jeu de données dans son intégralité. 


### <a name="how-to-use"></a>Utilisation
Vous pouvez ajouter ce point d’extension à l’aide du bloc « Transform Partition (Script) ». Celui-ci est disponible dans le menu de transformations de niveau supérieur. 

### <a name="syntax"></a>Syntaxe 

Expression : 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Module : 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Valeurs d’erreur  
La préparation des données intègre le concept de valeurs d’erreur. Leur création et la raison de leur existence sont traitées ici <link to error values doc>. 

Il est possible de rencontrer des valeurs d’erreur dans du code Python personnalisé. Ce sont des instances d’une classe Python nommée `DataPrepError`. Cette classe encapsule une exception Python et a deux propriétés qui contiennent des informations sur l’erreur qui s’est produite lors du traitement de la valeur d’origine, ainsi que la valeur d’origine. 


### <a name="datapreperror-class-definition"></a>Définition de la classe DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
En règle générale, la création d’une classe DataPrepError dans la structure Python de préparation des données se présente comme suit : 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Utilisation 
Lorsque Python est exécuté à un point d’extension, il est possible de générer des erreurs DataPrepErrors en tant que valeurs renvoyées, à l’aide de la méthode de création indiquée plus haut. Il est beaucoup plus probable que des erreurs DataPrepErrors surviennent lors du traitement de données à un point d’extension. À ce stade, le code Python personnalisé doit gérer l’erreur DataPrepError en tant que type de données valide. 

#### <a name="syntax"></a>Syntaxe 
Expression :  
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Module :  
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  

