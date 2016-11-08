---
title: Utilisation du service de gestion des API pour générer des requêtes HTTP
description: Apprenez à utiliser des stratégies de requête et de réponse dans le service de gestion des API pour appeler des services externes depuis votre API
services: api-management
documentationcenter: ''
author: darrelmiller
manager: ''
editor: ''

ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2016
ms.author: darrmi

---
# Utilisation de services externes à partir du service de gestion des API Azure
Les stratégies disponibles dans le service de gestion des API Azure permettent de réaliser un large éventail de tâches utiles purement selon la requête entrante, la réponse sortante et les informations de configuration de base. En revanche, la possibilité d’interagir avec des services externes à partir des stratégies de gestion des API ouvre bien davantage d’opportunités.

Nous avons vu précédemment comment interagir avec le service [Azure Event Hub pour la journalisation, la surveillance et l’analyse](api-management-log-to-eventhub-sample.md). Dans cet article, nous allons décrire les stratégies qui vous permettent d’interagir avec n’importe quel service HTTP externe. Ces stratégies peuvent être utilisées pour déclencher des événements à distance ou récupérer des informations servant à manipuler la requête d’origine et la réponse d’une certaine façon.

## Send-One-Way-Request (Envoyer une requête à sens unique)
L’interaction externe la plus simple est peut-être le style « fire and forget » d’une demande qui permet à un service externe d’être notifié d’un type d’événement important. Nous pouvons utiliser la stratégie de flux de contrôle `choose` pour détecter tout type de condition qui nous intéresse et puis, si la condition est remplie, nous pouvons effectuer une requête HTTP externe à l’aide de la stratégie [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest). Il peut s’agir d’une requête destinée à un système de messagerie comme Hipchat ou Slack, ou encore à une API de messagerie telle que SendGrid ou MailChimp, ou à quelque chose comme PagerDuty pour les incidents de support critiques. Tous ces systèmes de messagerie ont des API HTTP simples que nous pouvons facilement appeler.

### Alerte avec Slack
L’exemple suivant montre comment envoyer un message à une salle de conversation Slack si le code d’état de la réponse HTTP est supérieur ou égal à 500. Une erreur incluse dans la plage 500 indique un problème avec notre API principale que le client de notre API ne peut pas résoudre lui-même. Elle nécessite généralement une intervention de notre part.

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Slack inclut la notion de Webhook entrant. Quand vous configurez un Webhook entrant, Slack génère une URL spéciale qui vous permet de faire une requête POST simple et de transmettre un message dans le canal Slack. Le corps JSON que nous créons se base sur un format défini par Slack.

![Webhook Slack](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### Le style « fire and forget » est-il suffisant ?
L’utilisation d’un style « fire and forget » de requête implique certains compromis. Si, pour une raison ou une autre, la requête échoue, alors l’échec n’est pas signalé. Dans ce cas particulier, la complexité d’avoir un système de signalement des échecs secondaire et le coût des performances supplémentaires liées à l’attente de la réponse ne sont pas justifiés. Pour les scénarios où il est indispensable de vérifier la réponse, la stratégie [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) constitue une meilleure option.

## Send-Request (Envoyer une requête)
La stratégie `send-request` permet d’utiliser un service externe pour exécuter des fonctions de traitement complexes et retourner des données au service Gestion des API qui peuvent être utilisées pour d’autres traitements de stratégie.

### Autorisation des jetons de référence
Une fonction majeure de la gestion des API consiste à protéger les ressources principales. Si le serveur d’autorisation utilisé par votre API crée des [jetons JWT](http://jwt.io/) dans le cadre de son flux OAuth2, comme le fait [Azure Active Directory](../active-directory/active-directory-aadconnect.md), vous pouvez utiliser la stratégie `validate-jwt` pour vérifier la validité du jeton. Toutefois, certains serveurs d’autorisation créent des [jetons de référence](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) qui ne peuvent pas être vérifiés sans rappeler le serveur d’autorisation.

### Introspection normalisée
Par le passé, il n’existait aucun moyen normalisé de vérifier un jeton de référence avec un serveur d’autorisation. Néanmoins, une norme récemment proposée, [RFC 7662](https://tools.ietf.org/html/rfc7662), qui définit comment un serveur de ressources peut vérifier la validité d’un jeton, a été publiée par l’IETF.

### Extraction du jeton
La première étape consiste à extraire le jeton de l’en-tête d’autorisation. La valeur d’en-tête doit être mise en forme à l’aide du modèle d’autorisation `Bearer`, d’un seul espace, puis du jeton d’autorisation conformément à la norme [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Malheureusement, il existe des cas où le modèle d’autorisation est omis. Pour en tenir compte lors de l’analyse, nous fractionnons la valeur d’en-tête sur un espace et sélectionnons la dernière chaîne dans le tableau de chaînes retourné. Une solution de contournement est ainsi trouvée pour les en-têtes d’autorisation mal formés.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### Requête de validation
Une fois que nous avons le jeton d’autorisation, nous pouvons faire la requête pour valider le jeton. La norme RFC 7662 appelle ce processus « introspection » et vous oblige à appliquer une commande `POST` de formulaire HTML à la ressource d’introspection. Le formulaire HTML doit contenir au moins une paire clé/valeur avec la clé `token`. Cette requête adressée au serveur d’autorisation doit également être authentifiée pour veiller à ce qu’aucun client malveillant ne puisse obtenir des jetons valides.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### Vérification de la réponse
L’attribut `response-variable-name` est utilisé pour accéder à la réponse retournée. Le nom défini dans cette propriété peut être utilisé comme clé dans le dictionnaire `context.Variables` pour accéder à l’objet `IResponse`.

À partir de l’objet réponse, nous pouvons récupérer le corps et la norme RFC 7622 nous indique que la réponse doit être un objet JSON et contenir au moins une propriété appelée `active` qui est une valeur booléenne. Quand `active` a la valeur true, alors le jeton est considéré comme valide.

### Signalement d’un échec
Nous utilisons une stratégie `<choose>` pour détecter si le jeton n’est pas valide et le cas échéant, retourner une réponse 401.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

Conformément à la norme [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) qui décrit comment les jetons `bearer` doivent être utilisés, nous retournons également un en-tête `WWW-Authenticate` avec la réponse 401. L’élément WWW-Authenticate a pour but d’informer un client sur la manière de créer une requête dûment autorisée. En raison de la grande variété d’approches possibles avec l’infrastructure OAuth2, il est difficile de communiquer toutes les informations nécessaires. Heureusement, tous les efforts sont déployés pour aider les [clients à découvrir comment autoriser correctement les requêtes adressées à un serveur de ressources](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### Solution finale
En rassemblant tous les éléments, nous obtenons la stratégie suivante :

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
              <!-- Check active property in response -->
              <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                  <!-- Return 401 Unauthorized with http-problem payload -->
                  <return-response response-variable-name="existing response variable">
                      <set-status code="401" reason="Unauthorized" />
                      <set-header name="WWW-Authenticate" exists-action="override">
                          <value>Bearer error="invalid_token"</value>
                      </set-header>
                  </return-response>
              </when>
          </choose>
      <base />
    </inbound>

Il ne s’agit que d’un des nombreux exemples d’utilisation de la stratégie `send-request` pour intégrer des services externes utiles dans le processus des requêtes et réponses transitant par le service de gestion des API.

## Rédaction de réponse
La stratégie `send-request` peut être utilisée pour améliorer une requête principale adressée à un système principal, comme nous l’avons vu dans l’exemple précédent, ou elle peut remplacer totalement l’appel du serveur principal. À l’aide de cette technique, nous pouvons facilement créer des ressources composites qui sont agrégées à partir de plusieurs systèmes différents.

### Génération d’un tableau de bord
Vous avez parfois besoin d’exposer des informations qui existent dans plusieurs systèmes principaux, par exemple, pour piloter un tableau de bord. Les indicateurs de performance clés proviennent de tous les services principaux différents, mais vous préférez ne pas y fournir d’accès direct et il serait intéressant que toutes les informations puissent être récupérées dans une seule requête. Certaines informations principales ont peut-être besoin d’être coupées en rondelles ou en tranches, voire d’être assainies dans un premier temps. La possibilité de mettre en cache cette ressource composite s’avère utile pour réduire la charge principale puisque vous savez que les utilisateurs ont l’habitude de marteler la touche F5 pour voir si leurs mesures peu performantes peuvent changer.

### Simulation de la ressource
La première étape pour générer notre ressource de tableau de bord consiste à configurer une nouvelle opération dans le portail des éditeurs de la gestion des API. Il s’agit d’une opération d’espace réservé utilisée pour configurer notre stratégie de rédaction afin de générer notre ressource dynamique.

![Opération de tableau de bord](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### Construction des requêtes
Une fois l’opération `dashboard` créée, nous pouvons configurer une stratégie spécifiquement pour cette opération.

![Opération de tableau de bord](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

La première étape consiste à extraire les paramètres de requête à partir de la requête entrante, de sorte à pouvoir les transférer vers notre serveur principal. Dans cet exemple, notre tableau de bord affiche des informations selon une période ; il comporte donc un paramètre `fromDate` et `toDate`. Nous pouvons utiliser la stratégie `set-variable` pour extraire les informations de l’URL de la requête.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Une fois que nous avons ces informations, nous pouvons faire des requêtes auprès de tous les systèmes principaux. Chaque requête construit une nouvelle URL avec les informations de paramètre, puis appelle son serveur respectif et enregistre la réponse dans une variable de contexte.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Ces requêtes s’exécutent en séquence, ce qui n’est pas idéal. Dans une prochaine version, nous introduirons une nouvelle stratégie nommée `wait` qui permettra à toutes ces requêtes de s’exécuter en parallèle.

### Réponse
Pour construire la réponse composite, nous pouvons utiliser la stratégie [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse). L’élément `set-body` peut utiliser une expression pour construire un nouveau `JObject` avec toutes les représentations de composant incorporées en tant que propriétés.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

La stratégie complète se présente comme suit :

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

Pendant la configuration de l’opération d’espace réservé, nous pouvons configurer la ressource de tableau de bord de sorte à la mettre en cache pendant au moins une heure, car nous sommes conscients que la nature des données signifie que même si elle est en retard d’une heure, elle sera quand même suffisamment efficace pour transmettre des informations utiles aux utilisateurs.

## Résumé
Le service de gestion des API Azure offre des stratégies flexibles que vous pouvez appliquer de façon sélective au trafic HTTP et permet de composer des services principaux. Que vous vouliez améliorer votre passerelle API avec des fonctions d’alerte, des fonctionnalités de vérification et de validation ou créer des ressources composites reposant sur plusieurs services principaux, la stratégie `send-request` et les stratégies associées vous ouvrent un monde de possibilités.

## Regarder une vidéo de présentation de ces stratégies.
Pour plus d'informations sur les stratégies [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) et [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) abordées dans cet article, regardez la vidéo suivante.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Send-Request-and-Return-Response-Policies/player]
> 
> 

<!---HONumber=AcomDC_0810_2016-->