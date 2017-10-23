---
title: "Sécurité au niveau des lignes avec Collections d’espaces de travail Power BI"
description: "Détails sur la sécurité au niveau des lignes avec Collections d’espaces de travail Power BI"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 8c3ce8bc69a098d3133f27a2604f9d564693ea54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="row-level-security-with-power-bi-workspace-collections"></a>Sécurité au niveau des lignes avec Collections d’espaces de travail Power BI

La sécurité au niveau des lignes (RLS) peut être utilisée pour restreindre l’accès des utilisateurs à des données particulières au sein d’un rapport ou d’un jeu de données, permettant à plusieurs utilisateurs différents d’utiliser le même rapport pour voir des données différentes. Le service Collections d’espaces de travail Power BI prend en charge les jeux de données configurés avec RLS.

![Flux de sécurité au niveau des lignes dans Collections d’espaces de travail Power BI](media/row-level-security/flow-1.png)

> [!IMPORTANT]
> Le service Collections d’espaces de travail Power BI est déprécié et disponible jusqu’en juin 2018 ou jusqu’à la date indiquée sur votre contrat. Nous vous conseillons de planifier votre migration vers Power BI Embedded pour éviter toute interruption dans votre application. Pour plus d’informations sur la façon de migrer vos données vers Power BI Embedded, consultez [Comment migrer le contenu d’une collection d’espaces de travail Power BI Embedded vers Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Pour tirer parti de la fonction RLS, il est important de comprendre trois concepts principaux : les utilisateurs, les rôles et les règles. Examinons-les de plus près :

**Utilisateurs** : il s’agit des utilisateurs finaux réels visualisant les rapports. Dans Collections d’espaces de travail Power BI, les utilisateurs sont identifiés par la propriété de nom d’utilisateur dans un jeton d’application.

**Rôles** : les utilisateurs appartiennent à des rôles. Un rôle fait office de conteneur pour les règles et peut par exemple avoir pour nom « Directeur des ventes » ou « Représentant ». Dans Collections d’espaces de travail Power BI, les utilisateurs sont identifiés par la propriété de rôles dans un jeton d’application.

**Règles** : les rôles ont des règles, et ces règles sont les filtres réels qui seront appliqués aux données. Cela peut être aussi simple que « Pays = États-Unis » ou bien quelque chose de plus dynamique.

### <a name="example"></a>Exemple

Pour le reste de cet article, nous fournissons un exemple de création RLS et d’utilisation au sein d’une application intégrée. Notre exemple utilise le fichier PBIX [Exemple d’analyse des données de vente](http://go.microsoft.com/fwlink/?LinkID=780547) .

![Exemple de rapport de ventes](media/row-level-security/scenario-2.png)

Notre exemple d’analyse des données de vente affiche les ventes pour tous les magasins dans une chaîne de magasins spécifique. Sans RLS, peu importe le directeur régional qui se connecte et visualise le rapport, les données affichées sont identiques. La direction a déterminé que chaque directeur régional devait uniquement voir les ventes des magasins qu’il gérait, et pour ce faire, nous devons donc avoir recours à RLS.

RLS est créé dans Power BI Desktop. Lorsque le jeu de données et les rapports sont ouverts, nous pouvons basculer vers la vue schématique pour afficher le schéma :

![Diagramme de modèle dans Power BI Desktop](media/row-level-security/diagram-view-3.png)

Voici quelques points à noter concernant ce schéma :

* Toutes les mesures, comme **Total Sales**, sont stockées dans la table de faits **Sales**.
* Il existe quatre tables de dimension connexes supplémentaires : **Item**, **Time**, **Store** et **District**.
* Les flèches sur les lignes de relation indiquent de quelle façon les filtres peuvent circuler d’une table à l’autre. Par exemple, si un filtre est placé sur **Time[Date]**, dans le schéma actuel, il filtre uniquement sur les valeurs de la table **Sales**. Aucune autre table n’est affectée par ce filtre, car tous les flèches sur les lignes de relation pointent vers la table des ventes (et pas en direction opposée).
* La table **District** indique qui est le directeur pour chaque région :
  
  ![Lignes de la table District](media/row-level-security/district-table-4.png)

Selon ce schéma, si nous appliquons un filtre à la colonne **District Manager** dans la table District, et si ce filtre correspond à l’utilisateur qui consulte le rapport, ce filtre est également appliqué aux tables **Store** et **Sales** de façon à afficher uniquement les données de ce directeur régional.

Voici comment procéder :

1. Sous l’onglet de modélisation, cliquez sur **Gérer les rôles**.  
   ![Gérer les rôles dans le ruban Modélisation](media/row-level-security/modeling-tab-5.png)
2. Créez un rôle nommé **Manager**.  
   ![Création de rôles dans Power BI Desktop](media/row-level-security/manager-role-6.png)
3. Dans la table **District**, entrez l’expression DAX suivante : **[District Manager] = USERNAME()**  
   ![Expression de filtre DAX pour la table dans le rôle](media/row-level-security/manager-role-7.png)
4. Pour vérifier que les règles fonctionnent, sous l’onglet **Modélisation**, cliquez sur **Afficher comme rôles**, puis entrez ce qui suit :  
   ![Afficher en tant que rôles](media/row-level-security/view-as-roles-8.png)

   Les rapports affichent désormais les données comme si vous étiez connecté en tant que **Andrew Ma**.

L’application du filtre comme nous l’avons indiqué retourne tous les enregistrements filtrés des tables **District**, **Store** et **Sales**. Cependant, en raison de la direction du filtre sur les relations entre **Sales** et **Time**, **Sales** et **Item**, et **Item** et **Time**, les tables ne sont pas filtrées.

![Vue de diagramme avec les relations mises en surbrillance](media/row-level-security/diagram-view-9.png)

Cela n’est peut-être pas problématique dans le cadre de notre recherche actuelle, mais si nous ne voulons pas que les directeurs voient des éléments dont la vente ne les concerne pas, nous pouvons activer le filtrage croisé bidirectionnel pour la relation et appliquer le filtre de sécurité dans les deux directions. Ceci est possible en modifiant la relation entre **Sales** et **Item**, comme ceci :

![Direction du filtre croisé pour la relation](media/row-level-security/edit-relationship-10.png)

À présent, les filtres peuvent également circuler entre la table Sales et la table **Item** :

![Icône de direction de filtre de relation dans la vue de diagramme](media/row-level-security/diagram-view-11.png)

> [!NOTE]
> Si vous utilisez le mode DirectQuery pour vos données, vous devez activer le filtrage croisé bidirectionnel en sélectionnant ces deux options :

1. **Fichier** -> **Options et paramètres** -> **Fonctionnalités en préversion** -> **Activer le filtrage croisé dans les deux directions pour DirectQuery**.
2. **Fichier** -> **Options et paramètres** -> **DirectQuery** -> **Autoriser la mesure sans restriction en mode DirectQuery**.

Pour en savoir plus sur le filtrage croisé bidirectionnel, téléchargez le livre blanc [Bidirectional cross-filtering in SQL Server Analysis Services 2016 and Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx).

Cela conclut le travail qui doit être fait dans Power BI Desktop, mais plusieurs autres tâches doivent encore être effectuées pour que les règles RLS définies puissent fonctionner dans Power BI Embedded. Les utilisateurs sont authentifiés et autorisés par votre application, et les jetons d’application sont utilisés pour accorder l’accès utilisateur à un rapport Power BI Embedded spécifique. Power BI Embedded ne dispose pas d’informations spécifiques relatives à l’utilisateur. Pour que RLS fonctionne, vous devez transmettre des informations supplémentaires dans le cadre du jeton d’application :

* **username** (facultatif) : utilisé avec la fonction RLS, ceci est une chaîne qui peut aider à identifier l’utilisateur lors de l’application des règles RLS. Voir Utilisation de la sécurité au niveau des lignes avec Power BI Embedded
* **roles** : chaîne contenant les rôles à sélectionner lors de l’application des règles de sécurité au niveau des lignes. Si vous transmettez plusieurs rôles, ils doivent l’être en tant que table de chaînes.

Vous créez le jeton à l’aide de la méthode [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#Microsoft_PowerBI_Security_PowerBIToken_CreateReportEmbedToken_System_String_System_String_System_String_System_DateTime_System_String_System_Collections_Generic_IEnumerable_System_String__). Si la propriété de nom d’utilisateur est présente, vous devez également transmettre au moins une valeur dans les rôles.

Par exemple, vous pouvez modifier EmbedSample. La ligne 55 de DashboardController peut être changée, de

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

en

    var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id, "Andrew Ma", ["Manager"]);'

Le jeton d’application complet ressemble à ceci :

![Exemple de jeton web JSON](media/row-level-security/app-token-string-12.png)

Avec tous ces éléments, quand un utilisateur se connecte à notre application pour afficher ce rapport, il voit uniquement les données qu’il est autorisé à voir, comme défini par notre sécurité au niveau des lignes.

![Rapport affiché dans l’application](media/row-level-security/dashboard-13.png)

## <a name="see-also"></a>Voir aussi

[Sécurité au niveau des lignes (RLS) avec Power BI](https://powerbi.microsoft.com/documentation/powerbi-admin-rls/)  
[Authentification et autorisation dans le service Collections d’espaces de travail Power BI](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Exemple d’incorporation JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Des questions ? [Essayer la communauté Power BI](http://community.powerbi.com/)