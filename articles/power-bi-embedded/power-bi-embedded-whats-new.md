<properties
   pageTitle="Nouveautés de Power BI Embedded"
   description="Obtenir les dernières informations sur les nouveautés de Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/06/2016"
   ms.author="owend"/>

# Nouveautés de Power BI Embedded

Les mises à jour de **Power BI Embedded** sont publiées régulièrement. Toutefois, comme certaines versions portent sur les fonctionnalités du service principal, chaque version ne contient pas nécessairement de nouvelles fonctionnalités orientées utilisateur. Nous décrirons les nouvelles fonctionnalités orientées utilisateur ici. Veillez à consulter cette page régulièrement.

## 11 juillet 2016

Inclus dans cette version :

-    **Bonne nouvelle !** Le service Power BI Embedded n’est plus uniquement disponible en version préliminaire, mais a été mis à la disposition générale.
-    Toutes les autres API REST ont été déplacées de **/beta** vers **/v1.0**.
-    Les kits de développement logiciel (SDK) .NET et JavaScript a été mis à jour pour **v1.0**.
-    Les appels de l’API Power BI peuvent désormais être authentifiés directement à l’aide de clés API. Les jetons d’application sont uniquement nécessaires lors de l’incorporation. Par conséquent, les jetons d’approvisionnement et de développement sont déconseillés dans l’API v1.0, mais ils continueront à fonctionner dans la version bêta jusqu’au 30/12/2016. Pour plus d’informations, voir [Authentification et autorisation avec Power BI Embedded](power-bi-embedded-app-token-flow.md).
-    Prise en charge de la sécurité au niveau des lignes (RLS) pour les jetons d’application et les rapports incorporés. Pour plus d’informations, voir [Sécurité au niveau des lignes avec Power BI Embedded](power-bi-embedded-rls.md).
-    Exemple d’application mis à jour pour tous les appels d’API **v1.0**.
-    Prise en charge de Power BI Embedded pour le kit de développement logiciel (SDK) Azure, PowerShell et l’interface de ligne de commande.
-    Les utilisateurs peuvent exporter des données de visualisation vers un **.csv**.
-    Power BI Embedded est désormais pris en charge dans les mêmes langues/paramètres régionaux que Microsoft Azure. Pour plus d’informations, voir [Azure - Langues](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

<!---HONumber=AcomDC_0713_2016-->