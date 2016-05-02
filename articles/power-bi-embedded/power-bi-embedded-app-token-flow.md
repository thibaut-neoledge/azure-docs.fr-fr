<properties
   pageTitle="À propos du flux de jetons d’application dans Power BI Embedded"
   description="Power BI Embedded, à propos des jetons d'application pour l'authentification et d'autorisation"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# À propos du flux de jetons d’application dans Power BI Embedded

Le service **Power BI Embedded** utilise des **jetons d’application** pour l’authentification et l’autorisation des utilisateurs finaux. Il n’utilise pas l’authentification explicite. Dans ce modèle de **jetons d’application**, votre application gère l’authentification et l’autorisation de vos utilisateurs finaux.. Quand cela est nécessaire, votre application crée et envoie les **jetons d’application** à notre service pour lui indiquer d’afficher le rendu du rapport demandé. Avec cette conception, votre application peut gérer l’authentification et l’autorisation des utilisateurs sans passer par **Azure Active Directory**. Cette option est toutefois possible.

**Voici comment fonctionne le flux de clé de jeton d’application**

1. Copiez les clés API dans votre application. Vous pouvez obtenir les clés à partir du **portail Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Le jeton envoie une revendication et comporte un délai d'expiration.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Le jeton est signé à l’aide des clés d'accès API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. L’utilisateur demande à consulter un rapport.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.	Le jeton est validé à l’aide d’une clé d'accès API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.	Power BI Embedded envoie un rapport à l'utilisateur.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Après que **Power BI Embedded** envoie un rapport à l'utilisateur, ce dernier peut afficher le rapport dans votre application personnalisée. Par exemple, si vous avez importé l’[exemple PBIX Analyse des données de vente](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l’exemple d’application a l’aspect suivant :

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## Voir aussi
- [Prise en main de l’exemple Microsoft Power BI Embedded Preview](power-bi-embedded-get-started-sample.md)
- [Présentation de Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Scénarios Microsoft Power BI Embedded Preview courants](power-bi-embedded-scenarios.md)
- [Prise en main de la version préliminaire de Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0420_2016-->