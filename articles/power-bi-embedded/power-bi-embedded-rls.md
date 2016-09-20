<properties
   pageTitle="Sécurité au niveau des lignes avec Power BI Embedded"
   description="Détails sur la sécurité au niveau des lignes avec Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="mgblythe"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/05/2016"
   ms.author="mblythe"/>

# Sécurité au niveau des lignes avec Power BI Embedded

La sécurité au niveau des lignes (RLS) peut être utilisée pour restreindre l’accès des utilisateurs à des données particulières au sein d’un rapport ou d’un jeu de données, permettant à plusieurs utilisateurs différents d’utiliser le même rapport pour voir des données différentes. Power BI Embedded prend désormais en charge les jeux de données configurés avec la fonction RLS.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

Pour tirer parti de la fonction RLS, il est important de comprendre trois concepts principaux : les utilisateurs, les rôles et les règles. Examinons-les de plus près :

**Utilisateurs** : il s’agit des utilisateurs finaux réels visualisant les rapports. Dans Power BI Embedded, les utilisateurs sont identifiés par la propriété de nom d’utilisateur dans un jeton d’application.

**Rôles** : les utilisateurs appartiennent à des rôles. Un rôle fait office de conteneur pour les règles et peut avoir pour nom « Directeur des ventes » ou « Représentant ». Dans Power BI Embedded, les utilisateurs sont identifiés par la propriété de rôle dans un jeton d’application.

**Règles** : les rôles ont des règles, et ces règles sont les filtres réels qui seront appliqués aux données. Cela peut être aussi simple que « Pays = États-Unis » ou bien quelque chose de plus dynamique.

### Exemple

Pour le reste de cet article, nous fournirons un exemple de création RLS et d’utilisation au sein d’une application intégrée. Notre exemple utilise le fichier PBIX [Exemple d’analyse des données de vente](http://go.microsoft.com/fwlink/?LinkID=780547).

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Notre exemple d’analyse des données de vente affiche les ventes pour tous les magasins dans une chaîne de magasins spécifique. Sans RLS, peu importe le directeur régional qui se connecte et visualise le rapport, les données affichées sont identiques. La direction a déterminé que chaque directeur régional devait uniquement voir les ventes des magasins qu’il gérait, et pour ce faire, nous devons donc avoir recours à RLS.

RLS est créé dans Power BI Desktop. Lorsque le jeu de données et les rapports sont ouverts, nous pouvons basculer vers la vue schématique pour afficher le schéma :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Voici quelques points à noter concernant ce schéma :

-	Toutes les mesures, telles que **Total des ventes**, sont stockées dans la table des faits **Ventes**.
-	Il existe quatre tables de dimension connexes supplémentaires : **Élément**, **Date**, **Magasin** et **Région**.
-	Les flèches sur les lignes de relation indiquent de quelle façon les filtres peuvent circuler d’une table à l’autre. Par exemple, si un filtre est placé sur **Date**, dans le schéma actuel, il filtre uniquement sur les valeurs dans la table **Ventes**. Aucune autre table n’est affectée par ce filtre, car tous les flèches sur les lignes de relation pointent vers la table des ventes (et pas en direction opposée).
-	La table **Région** indique qui est le directeur pour chaque région :

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

Selon ce schéma, si nous appliquons un filtre à la colonne **Directeur régional** dans la table Région, et si ce filtre correspond à l’utilisateur qui consulte le rapport, ce filtre sera également appliqué aux tables **Magasin** et **Ventes** de façon à afficher uniquement les données de ce directeur régional en particulier.

Voici comment procéder :

1.	Dans l’onglet de modélisation, cliquez sur **Gérer les rôles**. ![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.	Créer un rôle nommé **Directeur**. ![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.	Dans la table **Région**, entrez l’expression DAX suivante : **[Directeur régional] = USERNAME()** ![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.	Pour vous assurer que les règles fonctionnent, dans l’onglet **Modélisation**, cliquez sur **Afficher comme rôles**, puis entrez les éléments suivants : ![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    Les rapports affichent désormais les données comme si vous étiez connecté en tant que **Andrew Ma**.

L’application du filtre comme nous l’avons indiqué renverra tous les enregistrements dans les tables **Région**, **Magasin** et **Ventes**. Toutefois, en raison de la direction du filtre sur les relations entre **Ventes** et **Date**, **Ventes** et **Élément**, et **Élément** et **Date**, les tables ne seront pas filtrées.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Cela n’est peut-être pas problématique dans le cadre de notre recherche actuelle, mais si nous ne voulons pas que les directeurs voient des éléments dont la vente ne les concerne pas, nous pouvons activer le filtrage croisé bidirectionnel pour la relation et appliquer le filtre de sécurité dans les deux directions. Cela est possible en modifiant la relation entre **Ventes** et **Élément**, comme ceci :

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

À présent, les filtres peuvent également circuler entre la table Ventes et la table **Élément** :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Remarque** Si vous utilisez le mode DirectQuery pour vos données, vous devez activer le filtrage croisé bidirectionnel en sélectionnant ces deux options :

1.	**Fichier** -> **Options et paramètres** -> **Fonctionnalités préliminaires** -> **Activer le filtrage croisé dans les deux directions pour DirectQuery**.
2.	**Fichier** -> **Options et paramètres** -> **DirectQuery** -> **Autoriser la mesure sans restriction en mode DirectQuery**.


Pour en savoir plus sur le filtrage croisé bidirectionnel, téléchargez le livre blanc [Filtrage croisé bidirectionnel dans SQL Server Analysis Services 2016 et Power BI Desktop] (http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx).

Cela conclut le travail qui doit être fait dans Power BI Desktop, mais plusieurs autres tâches doivent encore être effectuées pour que les règles RLS définies puissent fonctionner dans Power BI Embedded. Les utilisateurs sont authentifiés et autorisés par votre application, et les jetons d’application sont utilisés pour accorder l’accès utilisateur à un rapport Power BI Embedded spécifique. Power BI Embedded ne dispose pas d’informations spécifiques relatives à l’utilisateur. Pour que RLS fonctionne, vous devez transmettre des informations supplémentaires dans le cadre du jeton d’application :
-	**username** (facultatif) : utilisé avec la fonction RLS, ceci est une chaîne qui peut aider à identifier l’utilisateur lors de l’application des règles RLS. Voir Utilisation de la sécurité au niveau des lignes avec Power BI Embedded
-	**roles** : chaîne contenant les rôles à sélectionner lors de l’application des règles de sécurité au niveau des lignes. Si vous transmettez plusieurs rôles, ils doivent l’être en tant que table de chaînes.

Si la propriété de nom d’utilisateur est présente, vous devez également transmettre au moins une valeur dans les rôles.

Le jeton d’application complet ressemblera à ceci :

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

Avec tous ces éléments, lorsqu’un utilisateur se connecte à notre application pour afficher ce rapport, il pourra seulement voir les données qu’il est autorisé à voir, comme défini par notre sécurité au niveau des lignes.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## Voir aussi
[Sécurité au niveau des lignes (RLS) avec Power](https://powerbi.microsoft.com/fr-FR/documentation/powerbi-admin-rls/)

<!---HONumber=AcomDC_0907_2016-->