---
title: "Guide de programmabilité U-SQL pour Azure Data Lake Analytics | Documents Microsoft"
description: "Guide de programmabilité U-SQL"
services: data-lake-analytics
documentationcenter: 
author: MikeRys
manager: arindamc
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: mrys
translationtype: Human Translation
ms.sourcegitcommit: cd2aafd80db337cadaa2217a6638d93186975b68
ms.openlocfilehash: 563a6821b4a3736ef1233aa67d86b9ba06565788
ms.lasthandoff: 02/09/2017


---
# <a name="u-sql-programmability-guide"></a>Guide de programmabilité U-SQL
## <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake (ADL) inclut toutes les fonctionnalités permettant aux développeurs, chercheurs et analystes de données de stocker des données, quels que soient leur taille, leur forme ou leur débit, et d’effectuer tous types de traitements et d’analyses sur différentes plateformes et dans différents langages. Il élimine la complexité liée à l’ingestion et au stockage de l’ensemble de vos données, tout en accélérant la mise en route du traitement par lots, du streaming et des analyses interactives.

Azure Data Lake est un ensemble de services qui fonctionnent ensemble pour fournir une plateforme Big Data dans le cloud :

- HDInsight
- Azure Data Lake Store
- Service Analytique Azure Data Lake

U-SQL est un langage de requête spécifiquement conçu pour les données des charges de travail de type Big Data. L’une des fonctionnalités uniques de U-SQL est la combinaison d’un langage déclaratif de type SQL avec l’extensibilité et la programmabilité offertes par C#. Il permet également d’accéder aux métadonnées de schéma et de manipuler celles-ci, ainsi que de créer des composants personnalisés tels que des processeurs et réducteurs de données.

Ce guide se concentre sur l’extensibilité et la programmabilité du langage U-SQL que permet C#.

## <a name="requirements"></a>Configuration requise
Pour commencer un développement ADL, vous devez télécharger et installer [Azure Data Lake Tools pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="starting-with-u-sql"></a>Débuter avec U-SQL  
La description du langage U-SQL n’entre pas dans le cadre de ce document. Toutefois, nous décrivons une construction U-SQL de base pour introduire progressivement des fonctionnalités de programmation U-SQL. Pour plus d’informations, voir le guide [U-SQL Language Reference](http://aka.ms/usql_reference) (Référence sur le langage U-SQL).

Examinons l’exemple suivant :

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Dans l’exemple ci-dessus, nous avons un **fichier d’entrée** (input_file.tsv), défini par la **Variable locale** @input_file.

L’exécution du script U-SQL ci-dessus entraîne les actions suivantes :

* L’instruction **EXTRACT** charge les données dans la mémoire en convertissant le fichier d’entrée en **ensemble de lignes de mémoire**.
* L’instruction **SELECT** opère sur l’ensemble de lignes de données pour agréger les données et préparer leur exportation.
* La commande **OUTPUT** exporte l’ensemble de lignes de données vers le fichier externe **output_file**.

Tout d’abord, examinons quelques options pour utiliser une expression C# directement dans un script U-SQL.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Types et expressions C# dans les scripts U-SQL
Une expression C# U-SQL, comme en langage C# en général, est une séquence d’un ou plusieurs opérateurs qui peut être évaluée à une valeur, un objet, une méthode ou un espace de noms unique. Les expressions peuvent consister en une valeur littérale, un appel de méthode, un opérateur ou un nom simple. Un nom simple peut être le nom d’une variable, d’un membre de type, d’un paramètre de méthode, d’un espace de noms ou d’un type.

Lorsque nous parlons d’expressions C# U-SQL, nous faisons spécifiquement référence aux expressions C# de script U-SQL de base. La section code-behind C# sous-jacente, présentée plus loin dans ce document, peut également contenir des expressions C# en tant qu’élément basé sur du code C# ordinaire.

Les expressions peuvent recourir à des opérateurs utilisant d’autres expressions en tant que paramètres, ou des appels de méthode dont les paramètres sont eux-mêmes des appels de méthode. Exemple d’expression :  

```c#
    Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd"))
```

Le langage U-SQL permet d’utiliser des expressions C# standard à partir d’espaces de noms intégrés.  
 
```c#
    Microsoft.Analytics.Interfaces;  
    Microsoft.Analytics.Types.Sql;  
    System;  
    System.Collections.Generic;  
    System.Linq;  
    System.Text;  
```

Des expressions C# générales peuvent être utilisées dans les instructions U-SQL SELECT, EXTRACT.

Des expressions C# peuvent également être utilisées dans les instructions DECLARE ou IF. Voici un exemple d’une telle expression :   

```c#
    DateTime.Today.Day   
    Convert.ToDateTime
```

Exemple de script U-SQL de base :  

```sql
    DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");
```

Des expressions C# peuvent fournir des fonctionnalités étendues lors de la manipulation de colonnes dans le cadre de l’ensemble de lignes. Par exemple, si nous voulons convertir une colonne datetime en date sans heure, nous pouvons utiliser la partie SELECT suivante du script U-SQL de base :

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
     MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt).ToString("yyyy-MM-dd")))
AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Comme nous pouvons le constater, nous utilisons la méthode `System.Convert.ToDateTime` pour exécuter la conversion.

Le scénario d’utilisation un peu plus compliqué ci-dessous illustre l’usage de certains opérateurs C# de base. Examinez l’expression ci-dessous.

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
          MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

Voici un exemple d’expression d’opérateur conditionnel C#.

Les exemples ci-dessus illustrent l’utilisation d’expressions C# dans le script U-SQL de base. Toutefois, U-SQL permet d’autres fonctionnalités de programmabilité extensible, qui sont traitées plus loin dans ce document.  

Script complet :

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-data-type-conversions"></a>Utilisation d’expressions C# pour les conversions de types de données
À partir de l’exemple ci-dessus, l’exemple suivant illustre la technique de conversion de données datetime à l’aide d’expressions C#. Dans ce scénario, les données datetime de chaîne sont converties en données datetime standard avec la notation d’heure 00:00:00 (minuit).

```sql
DECLARE @dt String = "2016-07-06 10:23:15";
@rs1 =
    SELECT Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
           dt AS olddt
    FROM @rs0;
OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-c-expressions-for-todays-date"></a>Utilisation d’expressions C# pour la date du jour
Pour extraire la date du jour, nous pouvons utiliser l’expression C# suivante

```c#
DateTime.Now.ToString("M/d/yyyy")
```

Dans un script

```sql
@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;
```

## <a name="in-line-c-function-expressions"></a>Expressions de fonction C# inline
U-SQL autorise l’utilisation d’une définition d’expression de fonction inline dans des expressions C#. Cela offre des possibilités supplémentaires d’utilisation de fonctions C# avec des paramètres de référence de sortie.

Définition d’expression de fonction inline générale

```c#
    (Func<type of param1, type of param2>)
    (param1 =>
         { … return param2}
    ) (Rowset Column)
```

Exemple :

```c#
    (Func<string, DateTime?>)
    (input_p =>
         {
            DateTime dt_result;
            return DateTime.TryParse(input_p, out dt_result)
                   ? (DateTime?) dt_result : (DateTime?) null;
         }
    )
    ) (dt)
```

Dans cet exemple, nous définissons une fonction inline avec le paramètre d’entrée input_p . À l’intérieur de cette fonction, nous vérifions si la chaîne d’entrée est une valeur datetime valide. Si c’est le cas, retourner la valeur, sinon retourner la valeur null.

La fonction inline est nécessaire dans ce scénario, car la fonction DateTime.TryParse contient le paramètre de sortie - `out dt_result`. Nous le définissons comme `DateTime dt_result`.


## <a name="verifying-data-type-values"></a>Vérification des valeurs de type de données
Certaines fonctions C# ne peuvent pas être utilisées directement dans un script U-SQL de base comme une expression C#. C’est le cas en particulier des fonctions qui requièrent un paramètre de référence de sortie. Toutefois, ces fonctions peuvent être définies et utilisées dans une expression de fonction inline, comme nous l’avons vu plus haut.

### <a name="using-inline-function-expressions"></a>Utilisation d’expressions de fonction inline
Pour vérifier si la valeur DateTime est valide, nous pouvons utiliser `DateTime.TryParse`.

Voici l’exemple complet d’utilisation d’une expression de fonction inline pour vérifier une valeur de type de données avec `DateTime.TryParse`.

Dans ce scénario, nous vérifions la valeur de type de données DateTime :

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt string,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN((
             (Func<string, DateTime?>)
             (input_parameter =>
                { DateTime dt_result;
                  return
                     DateTime.TryParse(input_parameter, out dt_result)
                       ?(DateTime?) dt_result : (DateTime?) null;
                }
             )
             ) (dt)) AS start_time,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

### <a name="using-code-behind"></a>Utilisation de code-behind
Pour utiliser la même fonctionnalité dans la section code-behind du programme U-SQL, nous définissons la fonction C# ToDateTime.

Voici la section de script U-SQL de base, dans lequel nous avons apporté les modifications nécessaires :

```sql
     @rs1 =
        SELECT
          MAX(guid) AS start_id,
          MIN(USQL_Programmability.CustomFunctions.ToDateTime(dt)) AS start_time,
          DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
          user,
          des
          FROM @rs0
        GROUP BY user, des;
```

et la fonction code-behind

```c#
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }
```

## <a name="using-code-behind"></a>Utilisation de code-behind
code-behind est une section de programmabilité C# de projet U-SQL. Sur le plan conceptuel, le code-behind est un assembly compilé (DLL) référencé dans le script U-SQL. Visual Studio Tools vous permet de gérer et déboguer une section de programmabilité C# en tant que partie d’un projet U-SQL.

Un projet U-SQL standard créé dans Visual Studio comporte deux parties. Script de base : fichier avec l’extension **.usql**.

![a](./media/data-lake-analytics-u-sql-programmability-guide/base-script-file.png)


Projet de solution standard   
![typical-solution-project](./media/data-lake-analytics-u-sql-programmability-guide/typical-solution-project.png)


La deuxième partie du projet que nous appelons code-behind : fichier Script.usql.cs  
![code-behind](./media/data-lake-analytics-u-sql-programmability-guide/code-behind.png)

Ce fichier contient une définition d’espace de noms par défaut pour les objets de programmabilité.

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{

}
```

Le modèle de code-behind ci-dessus est ce qui est généré automatiquement. Ce fichier contient une définition d’espace de noms par défaut pour les objets de programmabilité. Pendant l’exécution du projet, il est compilé et référencé dans le script U-SQL de base.

Pour commencer à développer des objets de programmabilité, nous devons créer une classe **public**.

```c#
namespace USQL_Programmability
{
    public class MyClass
    {
        public static string MyFunction(string param1)
        {
            return "my result";
        }

    }

}
```

Les objets de programmabilité peuvent être des fonctions définies par l’utilisateur (ou **UDF**), des types définis par l’utilisateur (ou **UDT, PROCESS, REDUCER**), etc...

## <a name="registering-u-sql-assemblies"></a>Inscription des assemblys U-SQL
Le modèle d’extensibilité de U-SQL dépend largement de la possibilité d’ajouter votre propre code personnalisé. Actuellement, U-SQL vous offre des moyens faciles d’ajouter votre propre code basé sur .Net, en particulier C#, mais vous pouvez également ajouter du code personnalisé écrit dans d’autres langages .Net, tels que VB.Net ou F#. Vous pouvez même déployer votre propre runtime pour d’autres langages, bien que vous deviez toujours assurer l’interopérabilité à travers une couche .Net. Si vous souhaitez que nous prenions en charge un langage spécifique, veuillez introduire une demande de fonctionnalité et/ou laisser un commentaire ici : http://aka.ms/adlfeedback.

### <a name="difference-between-code-behind-and-assembly-registration-through-adl-tools-in-visual-studio"></a>Différence entre un code-behind et une inscription d’assembly via les outils ADL dans Visual Studio
Le moyen le plus simple d’utiliser un code personnalisé consiste à se servir des outils ADL pour les capacités de code-behind de Visual Studio.

Comme mentionné précédemment, vous entrez le code personnalisé pour le script (par exemple, Script.usql) dans son fichier de code-behind (par exemple, Script.usql.cs).

![code-behind-example](./media/data-lake-analytics-u-sql-programmability-guide/code-behind-example.jpg)
**Figure 1** : exemple de code-behind dans les outils ADL de Visual STUDIO (cliquez sur l’image pour l’agrandir, l’exemple de code est disponible [ici](https://github.com/Azure/usql/tree/master/Examples/TweetAnalysis))
<br />

L’avantage du code-behind est que les outils se chargent pour vous des opérations suivantes lorsque vous envoyez votre script :  

1. Ils génèrent l’assembly pour le fichier code-behind.  

1. Ils ajoutent au script un prologue qui utilise l’instruction [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) pour inscrire le fichier d’assembly et [REFERENCE ASSEMBLY]  (https://msdn.microsoft.com/library/azure/mt763294.aspx) pour charger l’assembly dans le contexte du script.

1. Ils ajoutent au script un épilogue qui utilise [DROP ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763295.aspx) pour supprimer à nouveau l’assembly temporairement inscrit.

Lorsque vous ouvrez le script, vous pouvez voir le prologue et l’épilogue générés :

![generated-prologue](./media/data-lake-analytics-u-sql-programmability-guide/generated-prologue.png)

**Figure 2** : prologue et épilogue générés automatiquement pour le code-behind
<br />

Le code-behind présente notamment les inconvénients suivants :

* Le code est chargé à chaque envoi de script.
* La fonctionnalité ne peut pas être partagée.

Par conséquent, vous pouvez ajouter une bibliothèque de classes C# séparée (pour U-SQL) à votre solution (voir Figure 3), développer le code ou recopier un code-behind existant (aucune modification du code C# requise, voir Figure 4), puis utiliser l’option de menu Inscrire un assembly sur le projet pour inscrire l’assembly (voir Étape 1 de la Figure 5).

![creating-project](./media/data-lake-analytics-u-sql-programmability-guide/creating-project.png)
**Figure 3** : création d’un projet de code C# U-SQL.  
<br />

![class-library](./media/data-lake-analytics-u-sql-programmability-guide/class-library.png)
**Figure 4** : bibliothèque de classes C# U-SQL à côté du fichier de code-behind.  
<br />

![register-code](./media/data-lake-analytics-u-sql-programmability-guide/register-code.png)
**Figure 5** : comment inscrire le projet de code C# U-SQL
<br />

La boîte de dialogue d’inscription (voir Étape 2 de la Figure 5) offre la possibilité de choisir l’emplacement où inscrire l’assembly (compte Data Lake Analytics et base de données) et son nom (le chemin d’accès de l’assembly local est inséré par l’outil). Elle comprend également une option pour réinscrire un assembly déjà inscrit, et deux options pour ajouter des dépendances :

* *Dépendances gérées* : affiche les assemblys managés également nécessaires. Chaque assembly sélectionné est inscrit individuellement et devient référençable dans les scripts. Utilisez cette options pour d’autres assemblys .Net.

* *Fichiers supplémentaires* : permet d’ajouter des fichiers de ressources supplémentaires dont l’assembly a besoin. Elles sont inscrites avec l’assembly et chargées automatiquement lorsque l’assembly est référencé. Utilisez cette option pour les fichiers de configuration, les assemblys natifs, d’autres runtimes de langage et leurs ressources, etc...

Nous utilisons ces deux options dans les exemples ci-dessous. Le [billet de blog récent sur le traitement d’image](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/18/introducing-image-processing-in-u-sql/) est un autre exemple illustrant l’utilisation d’un assembly prédéfini qui peut utiliser ces options pour l’inscription.

À présent, vous pouvez faire référence aux assemblys inscrits à partir de tout script U-SQL disposant d’autorisations sur la base de données des assemblys inscrits (voir le code dans le script U-SQL à la Figure 4). Vous devez ajouter une référence pour chaque assembly inscrit séparément. Des fichiers de ressources supplémentaires sont automatiquement déployés. Ce script ne doit plus avoir de fichier de code-behind pour le code dans les assemblys référencés, mais le fichier code-behind peut toujours fournir un autre code.

### <a name="registering-assemblies-via-adl-tools-in-visual-studio-and-in-u-sql-scripts"></a>Inscription des assemblys via les outils ADL dans Visual Studio et dans les scripts U-SQL
Bien que les outils ADL dans Visual Studio facilitent l’inscription d’un assembly, vous pouvez également la faire avec un script (de la même façon que les outils le font pour vous), par exemple, si vous développez sur une autre plateforme et avez des assemblys déjà compilés que vous voulez charger et inscrire. Vous suivez essentiellement les étapes suivantes :

1. Vous chargez votre DLL d’assembly ainsi que l’ensemble des DLL et fichiers de ressources non-système requis dans un emplacement de votre choix sur votre compte de stockage Azure Data Lake, voire sur un compte Stockage Blob Microsoft Azure lié à votre compte Azure Data Lake. Vous pouvez utiliser l’un des nombreux outils de chargement disponibles (par exemple, des commandes Powershell, un chargement via l’explorateur Data Lake de l’outil ADL de Visual Studio, une commande de chargement de votre Kit de développement logiciel (SDK) favori ou le portail Azure).

1. Après avoir téléchargé les DLL, vous utilisez les instructions [CREATE ASSEMBLY](https://msdn.microsoft.com/library/azure/mt763293.aspx) pour les inscrire.

Nous utilisons cette approche dans l’exemple spatial ci-dessous.

### <a name="registering-assemblies-that-use-other-net-assemblies-based-on-the-json-and-xml-sample-library"></a>Inscription d’assemblys qui utilisent d’autres assemblys .Net (basés sur la bibliothèque d’exemples JSON et XML)
Notre [site Github U-SQL](https://github.com/Azure/usql/) offre un ensemble d’exemples d’assemblys partagés que vous pouvez utiliser. L’un des assemblys, nommé [Microsoft.Analytics.Samples.Formats](https://github.com/Azure/usql/tree/master/Examples/DataFormats), fournit des extracteurs, des fonctions et des générateurs de sortie pour gérer les documents JSON et XML. L’assembly Microsoft.Analytics.Samples.Formats dépend de deux assemblys spécifiques du domaine pour traiter respectivement le JSON et le XML. Il utilise la bibliothèque [Newtonsoft Json.Net](http://www.newtonsoft.com/) pour traiter les documents JSON et l’assembly [System.Xml](https://msdn.microsoft.com/data/bb291078.aspx) pour traiter le XML. Utilisons-le pour montrer comment inscrire et utiliser les assemblys dans nos scripts.

Tout d’abord, nous téléchargeons le [projet Visual Studio](https://github.com/Azure/usql/tree/master/Examples/DataFormats) dans notre environnement de développement local (par exemple, en effectuant une copie locale avec l’outil GitHub pour Windows). Ensuite, nous ouvrons la solution dans Visual Studio et cliquons avec le bouton droit sur le projet, comme expliqué plus haut, pour inscrire l’assembly. Bien que cet assembly ait deux dépendances, nous ne devons inclure que la dépendance Newtonsoft, car System.Xml est déjà disponible dans Azure Data Lake (il doit cependant être référencé explicitement). La Figure 6 montre comment nommer l’assembly (vous pouvez également choisir un autre nom sans points) et ajouter la DLL Newtonsoft. Chacun des deux assemblys est désormais inscrit individuellement dans la base de données (par exemple, JSONBlog).

![register-assembly](./media/data-lake-analytics-u-sql-programmability-guide/register-assembly.png)

**Figure 6** : procédure d’inscription de l’assembly Microsoft.Analytics.Samples.Formats à partir de Visual Studio
<br />

Si vous ou d’autres personnes avec lesquelles vous avez partagé des assemblys en leur accordant l’accès en lecture à la base de données voulez à présent utiliser la fonctionnalité JSON dans vos propres scripts, ajoutez les deux références suivantes à celui-ci :

```
REFERENCE ASSEMBLY JSONBlog.[NewtonSoft.Json];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

Si vous souhaitez utiliser la fonctionnalité XML, ajoutez une référence d’assembly système et une référence à l’assembly inscrit :

```
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY JSONBlog.[Microsoft.Analytics.Samples.Formats];
```

Pour plus d’informations sur l’utilisation de la fonctionnalité JSON, voir [ce billet de blog](https://blogs.msdn.microsoft.com/mrys/?p=755).

### <a name="registering-assemblies-that-use-native-c-assemblies-using-the-sql-server-2016-spatial-type-assembly-from-the-feature-pack"></a>Inscription d’assemblys utilisant des assemblys C++ natifs (à l’aide de l’assembly de type spatial SQL Server 2016 disponible dans le Feature Pack)
Examinons à présent un scénario légèrement différent. Supposons que l’assembly que nous souhaitons utiliser dépend d’un code non basé sur .Net. Plus précisément, l’assembly dépend d’un assembly C++ natif. Un exemple d’un tel assembly est l’assembly de type SQL Server, [Microsoft.SqlServer.Types.dll](https://www.microsoft.com/download/details.aspx?id=52676), qui fournit des implémentations basées sur .Net des types hierarchyID, geometry et geography SQL Server que les applications SQL Server côté client doivent utiliser pour gérer les types SQL Server (c’était également à l’origine l’assembly fournissant l’implémentation pour les types spatiaux SQL Server avant la publication de SQL Server 2016).

Voyons comment inscrire cet assembly dans U-SQL.

Tout d’abord, nous téléchargeons et installons l’assembly à partir du [Feature Pack SQL Server 2016](https://www.microsoft.com/download/details.aspx?id=52676). Nous sélectionnons la version 64 bits du programme d’installation (ENU\x64\SQLSysClrTypes.msi), car nous voulons nous assurer de disposer de la version 64 bits des bibliothèques.

Le programme d’installation installe l’assembly managé Microsoft.SqlServer.Types.dll dans C:\Program Files (x86)\Microsoft SQL Server\130\SDK\Assemblies et l’assembly natif SqlServerSpatial130.dll dans \Windows\System32\. À présent, nous chargeons les assemblys dans notre Azure Data Lake Store (par exemple, dans un dossier nommé /upload/asm/spatial).

Étant donné que le programme d’installation a installé la bibliothèque native dans le dossier système C:\Windows\System32, nous devons veiller à faire une copie du fichier SqlServerSpatial130.dll à partir de ce dossier avant de le charger, ou nous assurer que l’outil que nous utilisons n’opère pas de [redirection du système de fichiers](https://msdn.microsoft.com/library/windows/desktop/aa384187(v=vs.85).aspx) des dossiers système. Par exemple, si vous souhaitez le charger avec l’Explorateur de fichiers ADL actuel de Visual Studio, vous devez d’abord copier le fichier dans un autre répertoire, sans quoi (au moment de la rédaction de ce blog) la version 32 bits est chargée (puisque Visual Studio est une application 32 bits qui opère une redirection du système de fichiers dans sa fenêtre de sélection de fichier à charger ADL) et, lorsque vous exécutez un script U-SQL qui appelle l’assembly natif, vous obtenez l’erreur (interne) suivante lors de l’exécution :

*Inner exception from user expression: An attempt was made to load a program with an incorrect format. (Exception from HRESULT: 0x8007000B)*

Après avoir chargé les deux fichiers d’assembly, nous les inscrivons dans une base de données SQLSpatial avec le script suivant :

```sql
DECLARE @ASSEMBLY_PATH string = "/upload/asm/spatial/";
DECLARE @SPATIAL_ASM string = @ASSEMBLY_PATH+"Microsoft.SqlServer.Types.dll";
DECLARE @SPATIAL_NATIVEDLL string = @ASSEMBLY_PATH+"SqlServerSpatial130.dll";

CREATE DATABASE IF NOT EXISTS SQLSpatial;
USE DATABASE SQLSpatial;

DROP ASSEMBLY IF EXISTS SqlSpatial;
CREATE ASSEMBLY SqlSpatial
FROM @SPATIAL_ASM
WITH ADDITIONAL_FILES =
     (
         @SPATIAL_NATIVEDLL
     );
```

Dans ce cas, nous inscrivons un seul assembly U-SQL, et incluons l’assembly natif en tant que dépendance de chaîne dans l’assembly U-SQL. Pour utiliser les assemblys spatiaux, il nous suffit de référencer l’assembly U-SQL. Le fichier supplémentaire sera automatiquement rendu disponible pour l’assembly. Voici un exemple simple de script utilisant l’assembly spatial :

```sql
REFERENCE SYSTEM ASSEMBLY [System.Xml];
REFERENCE ASSEMBLY SQLSpatial.SqlSpatial;

USING Geometry = Microsoft.SqlServer.Types.SqlGeometry;
USING Geography = Microsoft.SqlServer.Types.SqlGeography;
USING SqlChars = System.Data.SqlTypes.SqlChars;

@spatial =
    SELECT * FROM (VALUES
                   // The following expression is not using the native DDL
                   ( Geometry.Point(1.0,1.0,0).ToString()),    
                   // The following expression is using the native DDL
                   ( Geometry.STGeomFromText(new SqlChars("LINESTRING (100 100, 20 180, 180 180)"), 0).ToString())
                  ) AS T(geom);

OUTPUT @spatial
TO "/output/spatial.csv"
USING Outputters.Csv();
```

La bibliothèque de types SQL dépendant de l’assembly System.XML, nous devons référencer celui-ci. En outre, certaines méthodes utilisent les types System.Data.SqlTypes au lieu des types C# intégrés. System.Data étant inclus par défaut, je peux simplement référencer le type SQL nécessaire. Le code ci-dessus est disponible sur notre [site Github](https://github.com/Azure/usql/tree/master/Examples/SQLSpatialExample).


### <a name="assembly-versioning-and-other-points"></a>Contrôle de version des assemblys et autres points
Actuellement, U-SQL utilise .NET Framework version 4.5. Par conséquent, vérifiez que vos propres assemblys sont compatibles avec cette version du runtime.

Comme mentionné ci-dessus, U-SQL exécute le code dans un format 64 bits (x64). Par conséquent, assurez-vous que votre code est compilé pour s’exécuter sur x64. Dans le cas contraire, vous recevez l’erreur de format incorrect ci-dessus.

Aucun des DLL d’assembly, fichiers de ressources (tel qu’un runtime différent), assemblys natif, fichiers de configuration et autres éléments chargés ne peut avoir une taille supérieure à 400 Mo, et la taille totale des ressources déployées via l’instruction DEPLOY RESOURCE ou via des assemblys de référence et leurs fichiers supplémentaires ne peut pas dépasser 3 Go.

Enfin, notez que chaque base de données U-SQL ne peut contenir qu’une seule version d’un assembly donné. Par exemple, si vous avez besoin à la fois des versions 7 et 8 de la bibliothèque NewtonSoft Json.Net, vous devez les enregistrer dans deux bases de données distinctes. En outre, chaque script ne peut faire référence qu’à une seule version d’une DLL d’assembly donnée. À cet égard, U-SQL suit la sémantique de gestion et de contrôle de version d’assembly C#.


## <a name="user-defined-functions---udf"></a>Fonctions définies par l’utilisateur (UDF)
Les fonctions U-SQL définies par l’utilisateur (ou UDF) sont des routines de programmation qui acceptent des paramètres, effectuent une action telle qu’un calcul complexe, et retournent le résultat de celle-ci en tant que valeur. La valeur de retour d’une fonction définie par l’utilisateur ne peut être qu’une valeur scalaire unique. Une fonction définie par l’utilisateur U-SQL peut être appelée dans un script U-SQL de base comme toute autre fonction scalaire C#.

Les fonctions définies par l’utilisateur U-SQL doivent être initialisées en tant que publiques et **statiques**.

```c#
        public static string MyFunction(string param1)
        {
            return "my result";
        }
```

Examinons d’abord l’exemple simple de création d’une fonction définie par l’utilisateur.

Dans ce scénario d’utilisation, nous devons déterminer la période fiscale, le trimestre et le mois fiscal de la première connexion de l’utilisateur spécifique. Le premier mois fiscal de l’année dans notre scénario est juin.

Pour calculer la période fiscale, nous introduisons la fonction C# suivante.

```c#
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
        }
```

La fonction calcule simplement le trimestre et le mois fiscaux, puis retourne une valeur de chaîne. Pour juin (premier mois du premier trimestre fiscal), « Q1:P1 », pour juillet, « Q1:P2 », etc.

Il s’agit d’une fonction C# standard que nous allons utiliser dans notre projet U-SQL.

La section de code-behind dans ce scénario se présente comme suit :

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }

    }

}
```

Nous allons maintenant appeler cette fonction à partir du script U-SQL de base. Pour ce faire, nous devons fournir le nom complet de la fonction, qui inclut l’espace de noms NameSpace.Class.Function(paramètre)

```sql
    USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Voici le script base U-SQL réel

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
        MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Voici le fichier de sortie résultant de l’exécution du script

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Cet exemple illustre une utilisation simple d’une fonction définie par l’utilisateur inline dans U-SQL.

### <a name="keeping-state-between-udf-invocations"></a>Conservation de l’état entre des appels de fonction définie par l’utilisateur
Des objets de programmabilité C# U-SQL peuvent être plus sophistiqués en utilisant une interactivité via des variables globales de code-behind. Examinons le scénario d’utilisation professionnelle suivant :

Dans les grandes organisations, les utilisateurs peuvent basculer entre divers types d’applications internes. Celles-ci peuvent inclure Microsoft Dynamics CRM, Power BI, etc... Le client souhaite appliquer une analyse de télémétrie en relation avec la manière dont les utilisateurs basculent entre les différentes applications, la tendance d’utilisation, et ainsi de suite. L’objectif final de l’entreprise est d’optimiser l’utilisation des applications. Et éventuellement de combiner différentes applications ou routines de connexion spécifiques.

Pour atteindre cet objectif, nous devons déterminer un ID de session et un décalage entre les dernières sessions.

Nous devons trouver une ouverture de session précédente, puis affecter cette ouverture de session à toutes les sessions générées pour la même application. La première difficulté est que le script U-SQL de base ne nous permet pas d’appliquer des calculs à une colonne déjà calculée avec la fonction LAG. La deuxième difficulté est que nous devons garder la session spécifique pour toutes les sessions au cours de la même période.

Pour résoudre ce problème, nous allons utiliser une variable globale à l’intérieur de la section de code-behind : `static public string globalSession;`.

Cette variable globale est appliquée à l’ensemble de lignes lors de l’exécution de notre script.

Voici la section de code-behind de notre programme U-SQL

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60)
                    return Session;
                else
                    return Guid.NewGuid().ToString();
            }
            else
                return Guid.NewGuid().ToString();

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session))
            {
                globalSession = Session;
            }
            return globalSession;
        }

    }
}
```

La variable globale `static public string globalSession;` est utilisée dans la fonction `getStampUserSession` et réinitialisée à chaque modification du paramètre Session.

Script U-SQL de base

```sql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT EventDateTime,
           UserName,
LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
           USQLApplication21.UserSession.StampUserSession
           (
               EventDateTime,
               LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
               LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           )
           AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT EventDateTime,
           UserName,
           LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
           string.IsNullOrEmpty(LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
           USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
TO @out2
ORDER BY UserName, EventDateTime ASC
USING Outputters.Csv();
```

La fonction `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` est appelée ici pendant le deuxième calcul d’ensemble de lignes de mémoire. Elle transmet la colonne `UserSessionTimestamp` et retourne la valeur jusqu’à ce que `UserSessionTimestamp` soit modifié.

Fichier de sortie

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Cet exemple illustre un scénario d’utilisation plus compliqué dans lequel nous utilisons une variable globale à l’intérieur de la section de code-behind appliquée à l’ensemble de lignes de mémoire.

## <a name="using-user-defined-types---udt"></a>Utilisation de types définis par l’utilisateur (UDT)
Les types définis par l’utilisateur (ou UDT) sont une autre fonctionnalité de programmabilité de U-SQL. Un type défini par l’utilisateur U-SQL agit comme un type défini par l’utilisateur C# standard. C# est un langage fortement typé qui permet d’utiliser des types définis par l’utilisateur intégrés et personnalisés.

U-SQL ne peut pas implicitement sérialiser/désérialiser des types définis par l’utilisateur arbitraires lorsque le type défini par l’utilisateur est transmis entre les sommets des jeux de lignes. Par conséquent, l’utilisateur doit fournir un formateur explicite à l’aide de l’interface IFormatter. U-SQL connaît ainsi les méthodes de sérialisation et désérialisation pour le type défini par l’utilisateur. 

> [!NOTE]
> Les extracteurs et générateurs de sortie intégrés à U-SQL ne peuvent pas actuellement sérialiser/désérialiser les données du type défini par l’utilisateur vers/à partir de fichiers, même avec le jeu IFormatter.  Par conséquent, lors de l’écriture des données de type défini par l’utilisateur dans un fichier avec l’instruction OUTPUT ou lors de la lecture avec un extracteur, l’utilisateur doit les transmettre sous la forme d’unee chaîne ou d’un tableau d’octets et appeler explicitement le code de sérialisation et de désérialisation (par exemple la méthode ToString() du type défini par l’utilisateur). Les extracteurs et les générateurs de sortie définis par l’utilisateur quant à eux peuvent lire et écrire les types définis par l’utilisateur.

Si nous essayons d’utiliser un type défini par l’utilisateur dans un EXTRACTEUR ou un GÉNÉRATEUR DE SORTIE (à partir de l’instruction SELECT précédente)

```sql
@rs1 =
    SELECT MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

nous recevons l’erreur suivante

```
    Error    1    E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
    MyNameSpace.Myfunction_Returning_UDT.

    Description:

    Outputters.Text only supports built-in types.

    Resolution:

    Implement a custom outputter that knows how to serialize this type or call a serialization method on the type in
    the preceding SELECT.    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
    USQL-Programmability\Types.usql    52    1    USQL-Programmability
```

Pour utiliser un type défini par l’utilisateur dans un générateur de sortie, nous devons soit le sérialiser en une chaîne avec la méthode ToString(), soit créer un générateur de sortie personnalisé.

Il n’est actuellement pas possible d’utiliser des types définis par l’utilisateur dans une instruction GROUP BY. Si un type défini par l’utilisateur est utilisé dans une instruction GROUP BY, l’erreur suivante est levée :

```
    Error    1    E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
    for column myfield

    Description:

    GROUP BY doesn't support UDT or Complex types.

    Resolution:

    Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
    C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
    62    5    USQL-Programmability
```

Pour définir un type défini par l’utilisateur, nous devons effectuer les opérations suivantes :

* Ajouter les espaces de noms suivants

```c#
    using Microsoft.Analytics.Interfaces
    using System.IO;
```

* `Microsoft.Analytics.Interfaces` requis pour les interfaces du type défini par l’utilisateur et `System.IO` peut être nécessaire pour définir l’interface IFormatter

* Définir un type défini par l’utilisateur avec l’attribut SqlUserDefinedType

**SqlUserDefinedType** est utilisé pour marquer une définition de type dans un assembly en tant que type défini par l’utilisateur (UDT) dans U-SQL. Les propriétés de l’attribut reflètent les caractéristiques physiques du type défini par l’utilisateur. Cette classe ne peut pas être héritée.

SqlUserDefinedType est un attribut obligatoire pour la définition du type défini par l’utilisateur.

Le constructeur de la classe  

* SqlUserDefinedTypeAttribute(formateur de type)

* Formateur de type : paramètre requis pour définir un formateur de type défini par l’utilisateur. Plus spécifiquement, le type de l’interface `IFormatter` doit être transmis ici.

```c#
    [SqlUserDefinedType(typeof(MyTypeFormatter))]
      public class MyType
              {
             …
           }
```

* Un type défini par l’utilisateur standard nécessite également une définition de l’interface IFormatter

```c#
       public class MyTypeFormatter : IFormatter<MyType>
        {
            public void Serialize(MyType instance, IColumnWriter writer,
                           ISerializationContext context)
            {
        …
            }

            public MyType Deserialize(IColumnReader reader,
                                  ISerializationContext context)
            {
        …
            }
        }
```

Interface `IFormatter` pour sérialiser et désérialiser un graphique d’objet avec le type de racine \<typeparamref name="T">.

\<typeparam name="T">Type de racine pour le graphique d’objet à sérialiser et désérialiser.

* Désérialiser : désérialise les données sur le flux fourni et reconstitue le graphique d’objets.

* Sérialiser : sérialise un objet ou graphique d’objets avec la racine donnée dans le flux fourni.

Instance `MyType` : instance du type  
Enregistreur `IColumnWriter`/lecteur `IColumnReader` : flux de la colonne sous-jacente.  
Contexte `ISerializationContext` : énumération définissant un jeu d’indicateurs qui spécifie le contexte source ou cible pour le flux pendant la sérialisation. 
 
   * *Intermediate* - Spécifie que le contexte source ou cible n’est pas un magasin persistant

   * *Persistence* - Spécifie que le contexte source ou cible est un magasin persistant

En tant que type C# standard, une définition de type défini par l’utilisateur U-SQL peut inclure des remplacements pour des opérateurs tels que +/==/!=, etc. Peut inclure des méthodes statiques et ainsi de suite. Par exemple, si nous prévoyons d’utiliser ce type défini par l’utilisateur en tant que paramètre pour la fonction d’agrégation U-SQL MIN, nous devons définir un remplacement de l’opérateur <.

Plus haut dans ce guide, nous avons présenté un exemple d’identification de période fiscale à partir de la date spécifiée au format Qn:Pn (Q1:P10). L’exemple suivant montre comment définir un type personnalisé pour les valeurs de période fiscale.

Section de code-behind avec un type défini par l’utilisateur et une interface personnalisée IFormatter pour celui-ci :

```c#
        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
```

Le type défini inclut deux numéros, respectivement pour le trimestre et le mois. Les opérateurs ==/!=/>/< et la méthode statique ToString() sont définis ici.

Comme mentionné précédemment, le type défini par l’utilisateur peut être utilisé dans une expression SELECT, mais pas dans un GÉNÉRATEUR DE SORTIE/EXTRACTEUR sans sérialisation personnalisée. Il doit être sérialisé en tant que chaîne avec ToString(), ou utilisé avec un GÉNÉRATEUR DE SORTIE/EXTRACTEUR personnalisé.

Voyons à présent l’usage d’un type défini par l’utilisateur. Dans la section code-behind, nous avons modifié notre fonction GetFiscalPeriod en

```c#
        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }
```

Comme vous pouvez le voir, elle retourne la valeur de notre type FiscalPeriod.

Voici comment nous l’utilisons dans un script U-SQL de base. Cet exemple illustre différentes façons d’appeler un type défini par l’utilisateur à partir d’un script U-SQL.

```sql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT guid AS start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs0;

@rs2 =
    SELECT start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,
       
       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

Section de code behind complète :

```c#
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }

    }
}
```

## <a name="user-defined-aggregates--udagg"></a>Agrégats définis par l’utilisateur – UDAGG
Les agrégats définis par l’utilisateur sont toutes les fonctions d’agrégation qui ne sont pas fournies prêtes à l’emploi avec U-SQL. L’exemple peut être un agrégat effectuant un calcul mathématique personnalisé, effectuant des concaténations de chaînes ou des manipulations de chaînes, etc.

Une définition de classe de base d’agrégat défini par l’utilisateur est

```c#
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** indique que le type doit être inscrit en tant qu’agrégat défini par l’utilisateur. Cette classe ne peut pas être héritée.

L’attribut SqlUserDefinedType est **facultatif** pour la définition d’UDAGG


La classe de base permet de transmettre trois paramètres abstraits : deux d’entrée et un de résultat. Les types de données sont variables et doivent être définis lors de l’héritage de classe.

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
        …
        }

        public override void Accumulate(string guid, string user)
        {
        …
        }

        public override string Terminate()
        {
        …
        }

    }
```

* Init fournit une routine d’initialisation pour chaque groupe d’agrégation. Elle appelle une seule fois chaque groupe pendant le calcul.
* Accumulate : exécutée une fois pour chaque valeur. Fournit la fonctionnalité principale pour l’algorithme d’agrégation. Peut être utilisée pour agréger des valeurs de différents types de données définis lors de l’héritage de classe. Peut accepter deux paramètres de types de données variables.
* Terminate : exécutée une fois par groupe d’agrégation à la fin du traitement pour générer le résultat de chaque groupe

Pour déclarer des types de données d’entrée et de sortie corrects, utilisez une définition de classe

```c#
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1 : premier paramètre pour la routine Accumulate
* T2 : premier paramètre pour la routine Accumulate
* TResult : type de retour de la routine Terminate

par exemple

```c#
    public class GuidAggregate : IAggregate<string, int, int>
```

ou

```c#
    public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="using-udagg-in-u-sql"></a>Utilisation d’UDAGG dans U-SQL
Pour utiliser UDAGG, commencez par le définir dans le code-behind ou par le référencer à partir de la DLL de programmabilité existante, comme décrit précédemment.

Utilisez ensuite la syntaxe suivante.

```c#
    AGG<UDAGG_functionname>(param1,param2)
```

Voici l’exemple d’UDAGG

```c#
    public class GuidAggregate : IAggregate<string, string, string>
    {
        string guid_agg;

        public override void Init()
        {
            guid_agg = "";
        }

        public override void Accumulate(string guid, string user)
        {
            if (user.ToUpper()== "USER1")
            {
                guid_agg += "{" + guid + "}";
            }
        }

        public override string Terminate()
        {
            return guid_agg;
        }

    }
```

et le script U-SQL de base

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Dans ce scénario d’utilisation, nous concaténons des GUID de classe pour les utilisateurs spécifiés.

## <a name="user-defined-objects--udo"></a>Objets définis par l’utilisateur (UDO)
U-SQL permet de définir des objets de programmabilité personnalisés, appelés objets définis par l’utilisateur (ou UDO).

Voici la liste des UDO dans U-SQL

* Extracteurs définis par l’utilisateur
    * Extraient ligne par ligne
    * Utilisés pour implémenter une extraction de données à partir de fichiers structurés personnalisés

* Générateurs de sortie définis par l’utilisateur
    * Sortent ligne par ligne
    * Utilisés pour sortir des types de données personnalisées ou pour personnaliser le format de fichier

* Processeurs définis par l’utilisateur
    * Prennent une ligne et produisent une ligne
    * Utilisés pour réduire le nombre de colonnes ou produire une nouvelle colonne avec des valeurs dérivées d’un ensemble de colonnes

* Applicateurs définis par l’utilisateur
    * Prennent une ligne et produisent de 0 à n lignes
    * Utilisés avec OUTER/CROSS APPLY

* Combinateurs définis par l’utilisateur
    * Combinent des ensembles de lignes et des jointures définies par l’utilisateur

* Réducteurs définis par l’utilisateur
    * Prennent n lignes et produisent une ligne
    * Utilisés pour réduire le nombre de lignes

UDO généralement appelé explicitement dans un script U-SQL dans le cadre des instructions U-SQL suivantes :

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

## <a name="user-defined-extractor"></a>Extracteur défini par l’utilisateur
U-SQL vous permet d’importer des données externes en utilisant l’instruction EXTRACT. L’instruction EXTRACT peut utiliser des extracteurs UDO intégrés  

* *Extractors.Text()* : produit une extraction à partir de fichiers texte délimités de codages différents.

* *Extractors.Csv()* : produit une extraction à partir de fichiers de valeurs séparées par des virgules (CSV) de codages différents.

* *Extractors.Tsv()* : produit une extraction à partir de fichiers de valeurs séparées par des tabulations (TSV) de codages différents.

Toutefois, il peut être utile développer un extracteur personnalisé. C’est le cas lors de l’importation de données si vous souhaitez

* Modifier des données d’entrée en fractionnant des colonnes de façon à modifier des valeurs individuelles. Il est préférable de combiner des colonnes à l’aide de la fonctionnalité PROCESSOR.
* Analyser des données non structurées telles que des pages web et des messages électroniques, ou des données semi-structurées telles que des fichiers XML/JSON.
* Analyser des données dans un codage non pris en charge

Pour définir un extracteur défini par l’utilisateur (ou UDE), nous devons créer une interface `IExtractor`. Tous les paramètres d’entrée de l’extracteur, tels que les séparateurs de colonnes/lignes, le codage, etc., doivent être définis dans le constructeur de la classe. L’interface `IExtractor` doit également contenir une définition pour le remplacement de `IEnumerable<IRow>`

```c#
     [SqlUserDefinedExtractor]
     public class SampleExtractor : IExtractor
     {
         public SampleExtractor(string row_delimiter, char col_delimiter)
         {
            …

         }

         public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
         {
             …
         }
     }
```

L’attribut **SqlUserDefinedExtractor** indique que le type doit être inscrit en tant qu’extracteur défini par l’utilisateur. Cette classe ne peut pas être héritée.

SqlUserDefinedExtractor est un attribut facultatif pour la définition d’UDE. Il est utilisé pour définir la propriété AtomicFileProcessing pour l’objet UDE.

* bool     AtomicFileProcessing   

* **true**  = indique que cet extracteur requiert des fichiers d’entrée atomique (JSON, XML, ...)
* **false** = indique que cet extracteur peut traiter des fichiers fractionnés/distribués (CSV, SEQ, ...)

Les principaux objets de programmabilité d’UDE sont l’*entrée* et la *sortie*. L’objet d’entrée permet d’énumérer les données d’entrée en tant que `IUnstructuredReader`, et l’objet de *sortie* de définir les données de sortie en tant que résultat de l’activité de l’extracteur.

Les données d’entrée sont accessibles via `System.IO.Stream` et `System.IO.StreamReader`.

Pour l’énumération des colonnes d’entrée, nous fractionnons d’abord le flux d’entrée à l’aide du séparateur de lignes.

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
    }
```

Ensuite, nous fractionnons la ligne d’entrée en parties de colonne.

```c#
    foreach (Stream current in input.Split(my_row_delimiter))
    {
    …
        string[] parts = line.Split(my_column_delimiter);
               foreach (string part in parts)
        {
        …
        }
    }
```

Pour définir les données de sortie, nous utilisons la méthode `output.Set`

Il est important de comprendre qu’un extracteur personnalisé génère uniquement des colonnes/valeurs définies avec une sortie. Définissez l’appel de méthode.

```c#
    output.Set<string>(count, part);
```

La sortie réelle de l’extracteur est déclenchée par l’appel de `yield return output.AsReadOnly();`.

Voici l’exemple d’extracteur

```c#
    [SqlUserDefinedExtractor(AtomicFileProcessing = true)]
    public class FullDescriptionExtractor : IExtractor
    {
         private Encoding _encoding;
         private byte[] _row_delim;
         private char _col_delim;

        public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
        {
             this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
             this._row_delim = this._encoding.GetBytes(row_delim);
             this._col_delim = col_delim;

        }

        public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
        {
             string line;
             //Read the input line by line
             foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
             {
                using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
                 {
                     line = streamReader.ReadToEnd().Trim();
                     //Split the input by the column delimiter
                     string[] parts = line.Split(this._col_delim);
                     int count = 0; // start with first column
                     foreach (string part in parts)
                     {
    if (count == 0)
                         {  // for column “guid”, re-generated guid
                             Guid new_guid = Guid.NewGuid();
                             output.Set<Guid>(count, new_guid);
                         }
                         else if (count == 2)
                         {
                             // for column “user”, convert to UPPER case
                             output.Set<string>(count, part.ToUpper());

                         }
                         else
                         {
                             // keep the rest of the columns as-is
                             output.Set<string>(count, part);
                         }
                         count += 1;
                     }

                 }
                 yield return output.AsReadOnly();
             }
             yield break;
         }
     }
```

Dans ce scénario d’utilisation, l’extracteur régénère un GUID pour la colonne « guid », et convertit les valeurs de la colonne « utilisateur » en MAJUSCULES. Des extracteurs personnalisés peuvent produire des résultats plus complexes en analysant et manipulant des données d’entrée.

Script U-SQL de base utilisant un extracteur personnalisé

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="user-defined-outputter"></a>Générateur de sortie défini par l’utilisateur
Le générateur de sortie défini par l’utilisateur est un autre UDO U-SQL qui permet d’étendre une fonctionnalité U-SQL intégrée. Comme pour l’extracteur, il existe plusieurs générateurs de sortie intégrés.

* *Outputters.Text()* : écrit des données dans des fichiers texte délimités de codages différents.
* *Outputters.Csv()* : écrit des données dans des fichiers de valeurs séparées par des virgules (CSV) de codages différents.
* *Outputters.Tsv()* : écrit des données dans des fichiers de valeur séparées par des tabulations (TSV) de codages différents.

Un générateur de sortie personnalisé vous permet d’écrire des données dans un format défini personnalisé. Cela peut être utile pour

* Écrire des données dans des fichiers semi-structurés ou non structurés
* Écrire des données dans des codages non pris en charge
* Modifier des données de sortie ou ajouter des attributs personnalisés

Pour définir un générateur de sortie défini par l’utilisateur, nous devons créer une interface `IOutputter`.

Implémentation de la classe `IOutputter` de base

```c#
    public abstract class IOutputter : IUserDefinedOperator
    {
        protected IOutputter();

        public virtual void Close();
        public abstract void Output(IRow input, IUnstructuredWriter output);
    }
```

Tous les paramètres d’entrée du générateur de sortie, tels que les séparateurs de colonnes/lignes, le codage, etc., doivent être définis dans le constructeur de la classe. L’interface `IOutputter` doit également contenir une définition pour le remplacement de `void Output`. L’attribut `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` peut éventuellement être défini pour le traitement de fichier atomique. Voir les détails ci-dessous.

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class MyOutputter : IOutputter
        {

            public MyOutputter(myparam1, myparam2)
            {
              …
            }

            public override void Close()
            {
              …
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
              …
            }
        }
```

* `Output` appelé pour chaque ligne d’entrée. Retourne l’ensemble de lignes `IUnstructuredWriter output`
* La classe de constructeur est utilisée pour transmettre des paramètres au générateur de sortie défini par l’utilisateur
* Remplacement éventuel de `Close` pour libérer d’un état coûteux ou savoir quand la dernière ligne a été écrite

L’attribut **SqlUserDefinedOutputter** indique que le type doit être inscrit en tant que générateur de sortie défini par l’utilisateur. Cette classe ne peut pas être héritée.

SqlUserDefinedOutputter est un attribut facultatif pour la définition d’un générateur de sortie défini par l’utilisateur. Il est utilisé pour définir la propriété de AtomicFileProcessing.

* bool     AtomicFileProcessing   

* **true**  = indique que ce générateur de sortie requiert des fichiers de sortie atomique (JSON, XML, ...)
* **false** = indique que ce générateur de sortie peut traiter des fichiers fractionnés/distribués (CSV, SEQ, ...)

Les principaux objets de programmabilité sont *ligne* et *sortie*. L’objet *ligne* permet d’énumérer les données de sortie en tant qu’interface `IRow`, et l’objet *sortie* de définir les données de sortie dans le fichier cible.

Les données de sortie sont accessibles via l’interface `IRow`. Les données de sortie sont transmises une ligne à la fois.

Les valeurs individuelles sont énumérées en appelant la méthode Get de l’interface IRow.

```c#
    row.Get<string>("column_name")
```

Les noms des colonnes peuvent être déterminés en appelant `row.Schema`

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Cette approche permet de créer un générateur de sortie flexible pour tout schéma de métadonnées.

Les données de sortie sont écrites dans le fichier en utilisant `System.IO.StreamWriter` avec le paramètre stream défini sur `output.BaseStrea` dans `IUnstructuredWriter output`

Remarque importante : pour vider le tampon de données dans un fichier après chaque itération de ligne, l’objet `StreamWriter` doit être utilisé avec l’attribut Disposable activé (par défaut) et *en utilisant* un mot clé

```c#
    using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
            {
        …
            }
```

Autrement, appelez explicitement la méthode Flush() après chaque itération. Cela est illustré plus loin dans cet exemple :

### <a name="setting-header-and-footer-for-user-defined-outputter"></a>Définition d’en-tête et de pied de page pour un générateur de sortie défini par l’utilisateur
Pour définir un en-tête, utilisez un flux d’exécution d’itération unique.

```c#
            public override void Output(IRow row, IUnstructuredWriter output)
            {
             …
                if (isHeaderRow)
                {
                    …                
                }

             …
                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
             …
            }
        }
```

Le code du premier bloc `if (isHeaderRow)` n’est exécuté qu’une seule fois.

Pour le pied de page, utilisez une référence à l’instance de l’objet `System.IO.Stream` (`output.BaseStream`), et écrivez le pied de page dans la méthode Close() de l’interface `IOutputter` Voir l’exemple ci-dessous.

Voici l’exemple de générateur de sortie défini par l’utilisateur

```c#
        [SqlUserDefinedOutputter(AtomicFileProcessing = true)]
        public class HTMLOutputter : IOutputter
        {
            // Local variables initialization
            private string row_delimiter;
            private char col_delimiter;
            private bool isHeaderRow;
            private Encoding encoding;
            private bool IsTableHeader = true;
            private Stream g_writer;

            // Parameters definition            
            public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                this.isHeaderRow = isHeader;
                this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
            }

            // Close method is used to write footer to file - executed only once after all rows
            public override void Close()
            {
                //Reference to IO.Stream object - g_writer
                StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
                streamWriter.Write("</table>");
                streamWriter.Flush();
                streamWriter.Close();
            }

            public override void Output(IRow row, IUnstructuredWriter output)
            {
                System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

                // Metadata schema initialization to enumerate column names
                ISchema schema = row.Schema;

                // This is data independent header - HTML table definition
                if (IsTableHeader)
                {
                    streamWriter.Write("<table border=1>");
                    IsTableHeader = false;
                }

                // HTML table attributes
                string header_wrapper_on = "<th>";
                string header_wrapper_off = "</th>";
                string data_wrapper_on = "<td>";
                string data_wrapper_off = "</td>";

                // Header row output - runs only once
                if (isHeaderRow)
                {
                    streamWriter.Write("<tr>");
                    for (int i = 0; i < schema.Count(); i++)
                    {
                        var col = schema[i];
                        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
                    }
                    streamWriter.Write("</tr>");
                }

                // Data row output
                streamWriter.Write("<tr>");                
                for (int i = 0; i < schema.Count(); i++)
                {
                    var col = schema[i];
                    string val = "";
                    try
                    {
                        // Data type enumeration - require to match the distinct list of types form OUTPUT statement
                        switch (col.Type.Name.ToString().ToLower())
                        {
                            case "string": val = row.Get<string>(col.Name).ToString(); break;
                            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
                            default: break;
                        }
                    }
                    // Hnadling NULL values - keeping them empty
                    catch (System.NullReferenceException)
                    {
                    }
                    streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
                }
                streamWriter.Write("</tr>");

                if (isHeaderRow)
                {
                    isHeaderRow = false;
                }
                // Reference to the instance of the IO.Stream object for footer generation
                g_writer = output.BaseStream;
                streamWriter.Flush();
            }
        }

        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

et le script U-SQL de base

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
    USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Il s’agit d’un générateur de sortie HTML qui crée un fichier HTML avec les données de la table.

### <a name="calling-outputter-from-u-sql-base-script"></a>Appel d’un générateur de sortie à partir du script U-SQL de base
Pour appeler un générateur de sortie personnalisé à partir du script U-SQL de base, la nouvelle instance de l’objet générateur de sortie doit être créée.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Pour éviter de créer une instance de l’objet dans le script de base, nous pouvons créer un wrapper de fonction. À partir de ci-dessus

```c#
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

Dans ce cas, l’appel d’origine ressemble à

```sql
OUTPUT @rs0 TO @output_file USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="user-defined-processor"></a>Processeur défini par l’utilisateur
Un processeur défini par l’utilisateur (ou UDP) est un type d’UDO U-SQL qui permet de traiter les lignes entrantes en appliquant des fonctionnalités de programmabilité. Un UDP permet de combiner des colonnes, de modifier des valeurs et d’ajouter des colonnes si nécessaire. En fait, il permet de traiter un ensemble de lignes pour produire des éléments de données requis.

Pour définir un UDP, nous devons créer une interface `IProcessor` avec l’attribut `SqlUserDefinedProcessor` qui est facultatif pour l’UDP.

Cette interface doit contenir une définition pour le remplacement de l’ensemble de lignes de l’interface `IRow`

```c#
    [SqlUserDefinedProcessor]
     public class MyProcessor: IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
            …
         }
     }
```

**SqlUserDefinedProcessor** indique que le type doit être inscrit en tant que processeur défini par l’utilisateur. Cette classe ne peut pas être héritée.

L’attribut SqlUserDefinedProcessor est **facultatif** pour la définition d’UDP

Les principaux objets de programmabilité sont l’*entrée* et la *sortie*. L’objet d’entrée permet d’énumérer les données d’entrée, et l’objet de sortie de définir les données de sortie en tant que résultat de l’activité du processeur.

Pour l’énumération des colonnes d’entrée, nous utilisons la méthode `input.Get`

```c#
    string column_name = input.Get<string>("column_name");
```

Le paramètre pour la méthode `input.Get` est une colonne transmise dans une clause `PRODUCE` de l’instruction ` PROCESS` du script U-SQL de base. Nous devons utiliser le type de données ici.

Pour la sortie : méthode output.Set

Il est important de comprendre qu’un producteur personnalisé génère uniquement les colonnes/valeurs définies avec l’appel de la méthode output.Set.

```c#
    output.Set<string>("mycolumn", mycolumn);
```

La sortie réelle du processeur est déclenchée par l’appel de `return output.AsReadOnly()`.

Voici un exemple de processeur

```c#
     [SqlUserDefinedProcessor]
     public class FullDescriptionProcessor : IProcessor
     {
        public override IRow Process(IRow input, IUpdatableRow output)
         {
             string user = input.Get<string>("user");
             string des = input.Get<string>("des");
             string full_description = user.ToUpper() + "=>" + des;
             output.Set<string>("dt", input.Get<string>("dt"));
             output.Set<string>("full_description", full_description);
             output.Set<Guid>("new_guid", Guid.NewGuid());
             output.Set<Guid>("guid", input.Get<Guid>("guid"));
             return output.AsReadOnly();
         }
     }
```

Dans ce scénario d’utilisation, le processeur génère une nouvelle colonne « full_description » en combinant les colonnes existantes : « utilisateur » en majuscules et « des ». Il génère également un GUID et retourne les valeurs de GUID d’origine et nouvelles.

Comme vous pouvez le constater ci-dessus, vous pouvez appeler des méthodes C# durant l’appel de output.Set.

Script U-SQL de base utilisant un processeur personnalisé

```sql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="user-defined-applier"></a>Applicateur défini par l’utilisateur
Un applicateur U-SQL défini par l’utilisateur permet d’appeler une fonction C# personnalisée pour chaque ligne retournée par une expression de table externe d’une requête. L’entrée appropriée est évaluée pour chaque ligne à partir de l’entrée de gauche, et les lignes produites sont combinées pour la sortie finale. La liste des colonnes produites par l’opérateur APPLY est la combinaison des jeux de colonnes dans les entrées de gauche et de droite.

Un applicateur défini par l’utilisateur est appelé dans l’expression U-SQL SELECT.

L’appel standard à l’applicateur défini par l’utilisateur ressemble à ceci

```sql
    SELECT …
    FROM …
    CROSS APPLY
    new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Pour plus d’informations sur l’utilisation d’applicateurs dans une expression SELECT, voir [U-SQL SELECT Selecting from CROSS APPLY and OUTER APPLY](https://msdn.microsoft.com/library/azure/mt621307.aspx) (Sélection d’U-SQL SELECT à partir de CROSS APPLY et de OUTER APPLY).

Une définition de classe de base d’applicateur défini par l’utilisateur est

```c#
    public abstract class IApplier : IUserDefinedOperator
    {
        protected IApplier();

        public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
    }
```

Pour définir un applicateur défini par l’utilisateur, nous devons créer une interface `IApplier` avec l’attribut [`SqlUserDefinedCombiner`] qui est facultatif pour la définition d’applicateur défini par l’utilisateur.

```c#
    [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {

        public ParserApplier()
        {
        …
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {
        …
        }
    }
```

* Apply appelé pour chaque ligne de la table externe. Retourne l’ensemble de lignes de sortie `IUpdatableRow`
* Une classe de constructeur est utilisée pour transmettre des paramètres à l’applicateur défini par l’utilisateur

**SqlUserDefinedApplier** indique que le type doit être inscrit en tant qu’un applicateur défini par l’utilisateur. Cette classe ne peut pas être héritée.

L’attribut SqlUserDefinedApplier est **facultatif** pour la définition d’applicateur défini par l’utilisateur


Les principaux objets de programmabilité sont

```c#
        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Les ensembles de lignes d’entrée sont transmis en tant qu’entrée `IRow`. Les lignes de sortie sont générées en tant qu’interface de sortie `IUpdatableRow`

Les noms des colonnes peuvent être déterminés en appelant la méthode de schéma `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

Pour obtenir les valeurs de données réelles du `IRow` entrant, nous utilisons la méthode Get() de l’interface `IRow`

```c#
    mycolumn = row.Get<int>("mycolumn")
```

ou en utilisant un nom de colonne de schéma

```c#
    row.Get<int>(row.Schema[0].Name)
```

Les valeurs de sortie doivent être définies avec la sortie `IUpdatableRow`

```c#
    output.Set<int>("mycolumn", mycolumn)
```

Il est important de comprendre qu’un applicateur personnalisé génère uniquement les colonnes/valeurs définies avec l’appel de la méthode output.Set.

La sortie réelle est déclenchée par l’appel de `yield return output.AsReadOnly()`.

Les paramètres de l’applicateur défini par l’utilisateur peuvent être transmis au constructeur. Un applicateur peut retourner un nombre variable de colonnes, qui doit être défini durant l’appel de l’applicateur dans le script U-SQL de base.

```c#
  new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Voici l’exemple d’applicateur défini par l’utilisateur :

```c#
   [SqlUserDefinedApplier]
    public class ParserApplier : IApplier
    {
        private string parsingPart;

        public ParserApplier(string parsingPart)
        {
            if (parsingPart.ToUpper().Contains("ALL")
                || parsingPart.ToUpper().Contains("MAKE")
                || parsingPart.ToUpper().Contains("MODEL")
                || parsingPart.ToUpper().Contains("YEAR")
                || parsingPart.ToUpper().Contains("TYPE")
                || parsingPart.ToUpper().Contains("MILLAGE")
                )
            {
                this.parsingPart = parsingPart;
            }
            else
            {
                throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
            }
        }

        public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
        {

            string[] properties = input.Get<string>("properties").Split(',');

            //  only process with correct number of properties
            if (properties.Count() == 5)
            {

                string make = properties[0];
                string model = properties[1];
                string year = properties[2];
                string type = properties[3];
                int millage = -1;

                // Only return millage if it is number, otherwise, -1
                if (!int.TryParse(properties[4], out millage))
                {
                    millage = -1;
                }

                if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
                if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
                if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
                if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
                if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
            }
            yield return output.AsReadOnly();            
        }
    }
```

et le script U-SQL de base pour cette applicateur défini par l’utilisateur

```sql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

Dans ce scénario d’utilisation, l’applicateur défini par l’utilisateur agit comme un analyseur de valeurs délimitées par des virgules pour les propriétés d’une flotte de voitures. Les lignes du fichier d’entrée ressemblent à ceci :

```
103    Z1AB2CD123XY45889    Ford,Explorer,2005,SUV,152345
303    Y0AB2CD34XY458890    Shevrolet,Cruise,2010,4Dr,32455
210    X5AB2CD45XY458893    Nissan,Altima,2011,4Dr,74000
```

Il s’agit d’un fichier TSV standard de valeurs délimitées par des tabulations avec une colonne Properties contenant des propriétés de voitures telles que la marque, le modèle, etc... Ces propriétés doivent être analysées dans les colonnes de table. L’applicateur fourni permet également de générer un nombre dynamique de propriétés dans l’ensemble de lignes de résultat, en fonction du paramètre transmis : soit toutes les propriétés, soit un ensemble spécifique de propriétés.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

L’applicateur défini par l’utilisateur peut être appelé soit comme une nouvelle instance de l’objet applicateur

```c#
    CROSS APPLY new MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

ou par l’appel d’une méthode de fabrique de wrapper

```c#
    CROSS APPLY MyNameSpace.MyApplier (parameter: “value”) AS alias([columns types]…);
```

## <a name="user-defined-combiner"></a>Combinateur défini par l’utilisateur
Un combinateur défini par l’utilisateur (ou UDC) permet de combiner des lignes des ensembles de lignes gauche et droit, selon une logique personnalisée. Un combinateur défini par l’utilisateur est utilisé avec l’expression COMBINE.

Un combinateur est appelé avec l’expression COMBINE qui fournit les informations nécessaires sur les ensembles de lignes d’entrée, les colonnes de regroupement, le schéma de résultat attendu et des informations supplémentaires.

Pour appeler un combinateur dans un script U-SQL de base, nous utilisons la syntaxe suivante

```sql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Pour plus d’informations, voir [Expression COMBINE (U-SQL)](https://msdn.microsoft.com/library/azure/mt621339.aspx).

Pour définir un combinateur défini par l’utilisateur, nous devons créer une interface `ICombiner` avec l’attribut [`SqlUserDefinedCombiner`], qui est facultatif pour la définition de combinateur de défini par l’utilisateur.

Définition de classe `ICombiner` de base

```c#
    public abstract class ICombiner : IUserDefinedOperator
    {
        protected ICombiner();
        public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
               IUpdatableRow output);
        public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
               IUpdatableRow output);
    }
```

L’implémentation personnalisée de l’interface `ICombiner` doit contenir une définition pour le remplacement de `IEnumerable<IRow>` Combine.

```c#
    [SqlUserDefinedCombiner]
    public class MyCombiner : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
        …
        }
    }
```

L’attribut **SqlUserDefinedCombiner** indique que le type doit être inscrit en tant que combinateur défini par l’utilisateur. Cette classe ne peut pas être héritée.

SqlUserDefinedCombiner est un attribut facultatif pour la définition de combinateur défini par l’utilisateur. Il est utilisé pour définir la propriété du mode Combinateur.

CombinerMode     Mode

L’énumération CombinerMode peut prendre les valeurs suivantes :

* Complète (0) Chaque ligne de sortie dépend potentiellement de toutes les lignes d’entrée de gauche et de droite avec la même valeur de clé

* Gauche (1) Chaque ligne de sortie dépend d’une seule ligne d’entrée de gauche (et potentiellement de toutes les lignes de droite avec la même valeur de clé)

* Droite (2) Chaque ligne de sortie dépend d’une seule ligne d’entrée de droite (et potentiellement de toutes les lignes de gauche avec la même valeur de clé)

* Interne (3) Chaque ligne de sortie dépend d’une seule ligne d’entrée de gauche et droite avec la même valeur

Exemple :    [`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


Les principaux objets de programmabilité sont

```c#
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Les ensembles de lignes d’entrée sont transmis en tant que type d’interface *gauche* et *droit* `IRowset`. Les deux ensembles de lignes doivent être énumérés pour le traitement. Il n’est possible de les énumérer qu’une seule fois pour chaque interface. Nous devons donc les énumérer et les mettre en cache si nécessaire.

Pour la mise en cache, nous pouvons créer un type List\<T\> de structure de mémoire résultant de l’exécution de la requête LINQ. Plus précisément <`IRow`>. Le type de données anonymes peut également être utilisé pendant l’énumération.

Pour plus d’informations sur les requêtes LINQ, voir [Introduction aux requêtes LINQ (C#)](https://msdn.microsoft.com/library/bb397906.aspx) et, pour plus d’informations sur l’interface IEnumerable\<T\>, voir [Interface IEnumerable\<T\>](https://msdn.microsoft.com/library/9eekhta0(v=vs.110).aspx).

Pour obtenir les valeurs de données réelles du `IRowset` entrant, nous utilisons la méthode Get() de l’interface `IRow`

```c#
    mycolumn = row.Get<int>("mycolumn")
```

Les noms des colonnes peuvent être déterminés en appelant la méthode de schéma `IRow`.

```c#
    ISchema schema = row.Schema;
    var col = schema[i];
    string val = row.Get<string>(col.Name)
```

ou en utilisant un nom de colonne de schéma

```
    c# row.Get<int>(row.Schema[0].Name)
```

L’énumération générale avec LINQ ressemble à ceci :

```c#
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Après l’énumération des deux ensembles de lignes, nous allons exécuter une boucle sur toutes les lignes et, pour chaque ligne de l’ensemble de lignes de gauche, rechercher toutes les lignes remplissant la condition de notre combinateur.

Les valeurs de sortie doivent être définies avec la sortie `IUpdatableRow`

```c#
    output.Set<int>("mycolumn", mycolumn)
```

La sortie réelle est déclenchée par l’appel de `yield return output.AsReadOnly()`.

Voici un exemple de combinateur

```c#
    [SqlUserDefinedCombiner]
    public class CombineSales : ICombiner
    {

        public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output)
        {
            var internetSales =
            (from row in left.Rows
                          select new
                          {
                              ProductKey = row.Get<int>("ProductKey"),
                              OrderDateKey = row.Get<int>("OrderDateKey"),
                              SalesAmount = row.Get<decimal>("SalesAmount"),
                              TaxAmt = row.Get<decimal>("TaxAmt")
                          }).ToList();

            var resellerSales =
            (from row in right.Rows
             select new
             {
                 ProductKey = row.Get<int>("ProductKey"),
                 OrderDateKey = row.Get<int>("OrderDateKey"),
                 SalesAmount = row.Get<decimal>("SalesAmount"),
                 TaxAmt = row.Get<decimal>("TaxAmt")
             }).ToList();

            foreach (var row_i in internetSales)
            {
                foreach (var row_r in resellerSales)
                {

                    if (
                        row_i.OrderDateKey > 0
                        && row_i.OrderDateKey < row_r.OrderDateKey
                        && row_i.OrderDateKey == 20010701
                        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
                    {
                        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
                        output.Set<int>("ProductKey", row_i.ProductKey);
                        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
                        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
                    }

                }
            }
            yield return output.AsReadOnly();
        }
    }
```

Dans ce scénario d’utilisation, nous créons un rapport analytique pour le détaillant. L’objectif est de rechercher tous les produits dont le prix est supérieur à&20; 000 USD, et les ventes via le site web plus rapides que via le détaillant standard au cours d’une période déterminée.

Voici le script U-SQL de base. Vous pouvez comparer la logique entre une commande JOIN standard et un combinateur :

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Le combinateur défini par l’utilisateur peut être appelé soit comme une nouvelle instance de l’objet applicateur

```c#
    USING new MyNameSpace.MyCombiner();
```


ou par l’appel d’une méthode de fabrique de wrapper

```c#
    USING MyNameSpace.MyCombiner();
```

## <a name="user-defined-reducer"></a>Réducteur défini par l’utilisateur
U-SQL offre la possibilité d’écrire des réducteurs d’ensemble de lignes personnalisés en C#, utilisant l’infrastructure d’extensibilité d’opérateur définie par l’utilisateur en implémentant une interface IReducer.

Un réducteur défini par l’utilisateur (ou UDR) permet d’éliminer les lignes inutiles lors de l’extraction de données (importation). Il permet également de manipuler et d’évaluer des lignes ou colonnes, et, sur la base d’une logique de programmabilité, de définir les lignes à extraire.

Pour définir une classe UDR, nous devons créer une interface `IReducer` avec l’attribut facultatif `SqlUserDefinedReducer`

Cette interface de classe doit contenir une définition pour le remplacement de l’ensemble de lignes d’interface `IEnumerable`

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
            …
            }

        }
```

L’attribut **SqlUserDefinedReducer** indique que le type doit être inscrit en tant que réducteur défini par l’utilisateur. Cette classe ne peut pas être héritée.

SqlUserDefinedReducer est un attribut facultatif pour la définition de réducteur défini par l’utilisateur. Il est utilisé pour définir la propriété de IsRecursive.

* bool     IsRecursive    
* **true**  = indique si cette réduction est idempotente

Les principaux objets de programmabilité sont l’*entrée* et la *sortie*. L’objet d’entrée permet d’énumérer les lignes d’entrée, et l’objet de sortie de définir les lignes de sortie suite à l’activité de réduction.

Pour l’énumération des lignes d’entrée, nous utilisons la méthode `Row.Get`

```c#
            foreach (IRow row in input.Rows)
            {
                        row.Get<string>("mycolumn");
            }
```

Quelques points importants à ce sujet. Le paramètre pour la méthode `Row.Get` est une colonne transmise dans une clause `PRODUCE` de l’instruction `REDUCE` du script U-SQL de base. Nous devons utiliser le type de données correct ici également.

Pour la sortie : méthode output.Set

Il est important de comprendre qu’un réducteur personnalisé génère uniquement les valeurs définies avec l’appel de la méthode output.Set.

```c#
    output.Set<string>("mycolumn", guid);
```

La sortie réelle du réducteur est déclenchée par l’appel de `yield return output.AsReadOnly()`.

Voici un exemple de réducteur

```c#
        [SqlUserDefinedReducer]
        public class EmptyUserReducer : IReducer
        {

            public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
            {
                string guid;
                DateTime dt;
                string user;
                string des;

                foreach (IRow row in input.Rows)
                {
                    guid = row.Get<string>("guid");
                    dt = row.Get<DateTime>("dt");
                    user = row.Get<string>("user");
                    des = row.Get<string>("des");

                    if (user.Length > 0)
                    {
                        output.Set<string>("guid", guid);
                        output.Set<DateTime>("dt", dt);
                        output.Set<string>("user", user);
                        output.Set<string>("des", des);

                        yield return output.AsReadOnly();
                    }
                }
            }

        }
```

Dans ce scénario d’utilisation, le réducteur ignore les lignes dans lesquelles le nom d’utilisateur est vide. Pour chaque ligne de l’ensemble de lignes, il lit chaque colonne requise, puis évalue la longueur du nom d’utilisateur et génère la ligne réelle uniquement si la longueur du nom utilisateur est supérieure à 0.

Script U-SQL de base utilisant un réducteur personnalisé

```sql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 TO @output_file USING Outputters.Text();
```

