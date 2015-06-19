<properties 
	pageTitle="Créer des modules R personnalisés dans Microsoft Azure Machine Learning | Azure" 
	description="Démarrage rapide pour la création de modules R personnalisés dans Microsoft Azure Machine Learning." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/04/2015" 
	ms.author="bradsev" />


# Créer des modules R personnalisés dans Microsoft Azure Machine Learning

Cette rubrique explique comment créer un module R personnalisé dans Microsoft Azure Machine Learning. Elle explique ce qu'est un module R personnalisé, en détaillant les fichiers utilisés pour le définir. Par ailleurs, elle illustre la construction de ces fichiers et l'inscription du module à des fins de déploiement dans un espace de travail ML. Les éléments et attributs utilisés dans la définition du module personnalisé sont ensuite décrits plus en détail. Par ailleurs, nous allons découvrir comment utiliser les fichiers et la fonctionnalité auxiliaires, ainsi que les sorties multiples. 

## Qu'est-ce qu'un module R personnalisé ?
Un module personnalisé est un module défini par l'utilisateur, qui peut être chargé dans l'espace de travail d'un utilisateur et exécuté dans le cadre d'une expérience Microsoft Azure ML. Un module R personnalisé est un module exécutant une fonction R définie par l'utilisateur dans ML. R est un langage de programmation utilisé pour le traitement informatique des statistiques et les graphiques. Les scientifiques des données et les statisticiens s'en servent pour implémenter des algorithmes. Par défaut, Machine Learning est défini de manière à pouvoir utiliser le langage R dans les modules personnalisés.

Les modules personnalisés présentent un état de première classe dans Microsoft Azure Machine Learning, en ce sens qu'ils peuvent être utilisés comme n'importe quel module classique. Ils peuvent être exécutés avec d'autres modules, inclus dans les expériences publiées, ou visualisés. Les utilisateurs contrôlent l'algorithme implémenté par le module, les ports d'entrée et de sortie à utiliser, les paramètres de modélisation et divers autres comportements d'exécution.


## Fichiers dans un module R personnalisé
Un module R personnalisé est défini par un fichier .zip contenant au moins deux fichiers :

* un fichier qui implémente la fonction R exposée par le module ;
* un fichier de définition XML qui décrit le module personnalisé.

Des fichiers auxiliaires supplémentaires peuvent également être inclus dans le fichier .zip, qui fournit des fonctionnalités accessibles à partir du module personnalisé. Cette option est décrite ci-dessous.

## Exemple de démarrage rapide
Cet exemple explique comment créer les fichiers requis par un module R personnalisé, les empaqueter dans un fichier .zip, puis enregistrer le module dans l'espace de travail ML.

Prenons l'exemple d'un module de My Add Rows personnalisé, qui modifie l'implémentation standard du module Add Rows utilisé pour concaténer des lignes (observations) à partir de deux jeux de données (trames de données). Le module Add Rows ajoute les lignes du deuxième jeu de données d'entrée à la fin du premier, au moyen de l'algorithme rbind. De la même manière, la fonction `myAddRows` personnalisée accepte deux jeux de données, mais également un paramètre d'échange booléen en entrée. Si le paramètre d'échange a la valeur **FALSE**, il renvoie le même jeu de données que l'implémentation standard. Par contre, s'il a la valeur **TRUE**, il ajoute des lignes du premier jeu de données d'entrée à la fin du deuxième jeu de données. Le fichier qui implémente la fonction R myAddRows exposée par le module My Add Rows contient le code R suivant.

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (dataset)
	}

Pour exposer la fonction `myAddRows` en tant que module Microsoft Azure Machine Learning, vous devez créer un fichier de définition XML, afin de définir l'apparence du module My Add Rows et son comportement. 

	<Module Version="v0.00.1" type="Public" insync="false" Owner="myName">
	  <GUID>{1CE529D1-B9D2-496F-AB42-8DBA60DE8279}</GUID>
	  <ID>myAddRows</ID>
	  <Name>My Add Rows</Name>	
	  <State>Custom</State>
	  <Description>This is my module description. </Description>
	  <Language Name="R" EntryPointFile="myAddRows.R" EntryPoint="myAddRows" />  
	    <Ports>
	      <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	      </output>
	      <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	      </input>
	      <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	      </input>
	    </Ports>
	    <Arguments>
	      <arg id="swap" display="swap" type="bool" >
	        <Description>Swaps inputs</Description>
	      </arg>
	    </Arguments>
	  <Category>My Category</Category>
	</Module>

 
Remarque : il est essentiel que le contenu de l'élément ID dans le fichier XML corresponde exactement au nom de la fonction. 

Enregistrez ces deux fichiers en tant qu'éléments *myAddRows.R* et *myAddRows.xml*, puis compressez-les ensemble au sein d'un fichier *myAddRows.zip*.

Pour les enregistrer dans l'espace de travail ML, accédez à votre espace de travail dans ML Studio, cliquez sur le bouton **+ NOUVEAU** situé sur la partie inférieure de la fenêtre et choisissez **MODULE -> À PARTIR DU PACKAGE ZIP** pour charger le nouveau module My Add Rows personnalisé.

![](http://i.imgur.com/RFJhCls.png)

Le module My Add Rows peut désormais être ouvert par les expériences ML.

## Éléments du fichier de définition XML

### Entrées et sorties
Les entrées et sorties d'un module personnalisé sont spécifiées dans les éléments enfants de la section Ports du fichier de définition XML. L'ordre de ces éléments d'entrée et de sortie détermine la disposition rencontrée par les utilisateurs (interface utilisateur). Le premier enfant répertorié dans l'élément Ports  du fichier XML correspond au port d'entrée situé le plus à gauche dans l'interface utilisateur Machine Learning. Les types de données pris en charge pour les ports d'entrée et de sortie sont les suivants : 

**DataTable** : Ce type est passé à votre fonction R sous la forme suivante : data.frame. En fait, tous les types pris en charge par ML  et compatibles avec le type DataTable (par exemple, les fichiers CSV ou ARFF) sont automatiquement convertis en éléments DataTable. 

       <input id="dataset1" display="Input 1" type="DataTable" IsOptional="false">
           <Description>Input Dataset 1</Description>
       </input>

**Zip** : modules personnalisés pouvant accepter un fichier zip en entrée. Cette entrée est décompactée et placée dans le répertoire d'exécution de votre fonction.

       <input id="zippedData" display="Zip Input" type="Zip" IsOptional="false">
           <Description>Zip Input for port</Description>
       </input>

Il est nécessaire que les attributs ID associés à chacun des ports d'entrée et de sortie présentent des valeurs uniques, qui correspondent aux paramètres nommés dans votre fonction R. En outre, vous devez spécifier une valeur par défaut pour les attributs dont les entrées sont facultatives. L'attribut IsOptional est facultatif pour les types DataTable et Zip ; il a la valeur False par défaut. Cette valeur par défaut indique que le type d'entrée n'est pas facultatif.


### Paramètres
Les paramètres d'un module personnalisé sont spécifiés dans les éléments enfants de la section Arguments du fichier de définition XML. Comme dans le cas des éléments enfants de la section Ports, l'ordre des paramètres de la section Arguments définit la disposition rencontrée dans l'interface utilisateur. Le premier paramètre répertorié sera le premier paramètre de fonction. Les types pris en charge par ML pour les paramètres sont répertoriés ci-dessous. Les valeurs par défaut des attributs facultatifs doivent être spécifiées dans la définition XML. Les valeurs suivantes sont utilisées si la valeur du paramètre n'est pas spécifiée lors de l'utilisation de la fonction. Les attributs facultatifs sont indiqués pour chaque type.


**int** : paramètre de type entier (32 bits).

       <arg id="intValue1" display="My int Param" type="int" IsOptional="false" MinValue="0" MaxValue="100">
           <Description>Integer Parameter 1</Description>
       </arg>

Les éléments IsOptional, MinValue et MaxValue sont facultatifs pour int.

**double** : paramètre de type double.

       <arg id="doubleValue1" display="My double Param" type="double" IsOptional="false" min="0.000" max="0.999" default="0.3">
           <Description>Double Parameter 1</Description>
       </arg>
Les éléments IsOptional, min, max et default sont facultatifs pour double.

**bool** : paramètre booléen représenté par une case à cocher dans l'interface utilisateur.

       <arg id="boolValue1" display="My boolean Param" type="bool" default="true">
           <Description>Boolean Parameter 1 </Description>
       </arg>

L'élément default est facultatif pour bool.

**string** : chaîne standard.

        <arg id="stringValue1" display="My string Param" type="string" default="default value" IsOptional="true">
           <Description>String Parameter 1</Description>
        </arg>

Les éléments default et IsOptional sont facultatifs pour string.

**ColumnPickerFor** : paramètre de sélection de colonne. Ce type est représenté sous la forme d'un sélecteur de colonne dans l'interface utilisateur. L'ID de l'élément DataTable à partir duquel vous sélectionnez des colonnes doit remplacer la partie de la table dans la valeur associée à l'attribut de type. La variable sera passée à votre fonction sous la forme d'une liste de chaînes. 

        <arg id="columnSelection1" display="My Column Param" type="ColumnPickerFor:table">
           <Description>My column selector Param 1</Description>		
        </arg>

Si, par exemple, nous disposions d'un élément DataTable présentant l'ID dataset1, le type aurait la valeur suivante : 

		type="ColumnPickerFor:dataset1" 
                            
**enum:<ID_type_liste_déroulante>** : liste énumérée (déroulante). La valeur choisie est passée à votre fonction R sous la forme d'une chaîne. Ce type nécessite la définition préalable des valeurs énumérées valides dans la section Arguments.

       <DropDownType id="myDropDown1">
           <o id="red" display="Red"/>
           <o id="yellow" display="Yellow"/>
           <o id="blue" display="Blue"/>
       </DropDownType>
       <arg id="enum1" display="My Enum Param" default="red" type="enum:myDropDown1">
           <Description>My Enum Param 1</Description>
       </arg>

Comme dans le cas des entrées et sorties, il est essentiel que chaque paramètre soit associé à une valeur d'ID unique. En outre, les valeurs d'ID doivent correspondre aux paramètres nommés dans votre fonction R. Dans notre exemple de démarrage rapide, le paramètre/l'ID associé était le suivant : *swap*.

### Définition de l'élément Language
L'élément Language de votre fichier de définition XML permet de définir la fonctionnalité spécifique d'un langage. En général, dans le cas des modules R, nous obtenons : 

	<Language Name="R" EntryPointFile="myFunc.R" EntryPoint="myFunc" AddDisplayOutputPort="false"/>

Cette chaîne indique le langage spécifique à utiliser, le fichier dans lequel la fonction est définie et le point d'entrée dans cette définition. L'attribut AddDisplayOutputPort est facultatif pour l'élément Language. Comme dans le cas du module Execute R Script (lien à définir), si vous souhaitez ajouter un port de sortie pouvant être utilisé pour afficher des tracés/graphiques, sélectionnez la valeur *true* pour la balise AddDisplayOutputPort ; un port de sortie supplémentaire sera affiché. 

### Fonctionnalités auxiliaires

Il existe un certain nombre d'attributs supplémentaires, que les créateurs de modules personnalisés peuvent utiliser et qui ne sont pas représentés dans cet exemple. Par exemple, un module peut être associé à un comportement déterministe ou non-déterministe. Un module déterministe ne s'exécutera pas une seconde fois s'il reçoit les mêmes données d'entrée et la même configuration de paramètres. À la place, les résultats mis en cache seront utilisés et propagés à tous les modules en aval. Exemple de module déterministe dans Microsoft Azure ML : le module Add Rows. Exemple de module non-déterministe : Reader. Pour faire en sorte que votre module personnalisé soit non-déterministe, modifiez le paramètre par défaut en ajoutant l'attribut suivant à votre définition :

	<IsDeterministic>false</IsDeterministic>

### Fichiers auxiliaires

Tout fichier placé dans le fichier ZIP de votre module personnalisé sera disponible pour une utilisation au moment de l'exécution. S'il existe une structure de répertoire, elle est conservée. Cela signifie que l'approvisionnement du fichier fonctionnera de la même façon en local et lors de l'exécution de Microsoft Azure ML. 

Supposons que vous souhaitiez supprimer toutes les lignes présentant la mention " NA " et toutes les lignes en double dans le jeu de données avant d'en créer une sortie dans myAddRows. Or, vous avez déjà créé une fonction R qui effectue cette opération dans un fichier, removeDupNARows.R :

	removeDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
Vous pouvez sourcer la fonction removeDupNARows.R du fichier auxiliaire dans la fonction myAddRows :

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
		source("removeDupNARows.R")
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	dataset <- removeDupNARows(dataset)
	return (dataset)
	}

Ensuite, chargez un fichier zip contenant les éléments " myAddRows.R ", " myAddRows.xml " et " removeDupNARows.R " en tant que module R personnalisé.

### Sorties multiples

Pour créer une sortie portant sur plusieurs objets qui présentent un type de données pris en charge, vous devez spécifier les ports de sortie appropriés dans le fichier de définition XML et les objets doivent être renvoyés dans une liste. Les objets de la sortie seront attribués à des ports de sortie, de gauche à droite, selon l'ordre dans lequel les objets sont placés dans la liste renvoyée.
 
Par exemple, si vous souhaitez générer les éléments dataset, Dataset1 et Dataset2 aux ports de sortie dataset, dataset1 et dataset2 de gauche à droite, respectivement, définissez les ports de sortie dans le fichier " myAddRows.xml ", comme suit :

	<Ports>
	    <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset1" display="dataset1" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset2" display="dataset2" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
	    <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	    </input>
	    <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	    </input>
	</Ports>

Ensuite, renvoyez la liste des objets dans une liste respectant l'ordre adéquat dans " myAddRows.R " :

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (list(dataset, Dataset1, Dataset2))
	}

## Environnement d'exécution

TBD

## Étapes suivantes

TBD

<!--HONumber=49--> 