<properties 
	pageTitle="Référence sur les stratégies Gestion des API Azure" 
	description="Découvrez les stratégies disponibles pour configurer Gestion des API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Référence sur les stratégies Gestion des API Azure

Cette rubrique contient des informations de référence sur les stratégies Gestion des API Azure (version préliminaire) suivantes. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API][Stratégies dans Gestion des API].

-   [Accès aux stratégies de restriction][Accès aux stratégies de restriction]

    -   [Usage quota][Usage quota] : vous permet d'appliquer un volume et/ou un quota de bande passante renouvelable ou illimité.
    -   [Rate limit][Rate limit] : empêche les pics d'utilisation de l'API en limitant les appels et/ou le taux de consommation de la bande passante.
    -   [Caller IP restriction][Caller IP restriction]  : filtre (autorise/rejette) les appels de certaines adresses IP spécifiques ou de certaines plages d'adresses.
-   [Stratégies de transformation du contenu][Stratégies de transformation du contenu]

    -   [Set HTTP header][Set HTTP header] : affecte une valeur à un en-tête de réponse et/ou de demande existant ou bien ajoute un nouvel en-tête de réponse et/ou de demande.
    -   [Convert XML to JSON][Convert XML to JSON] : convertit le corps de la demande ou de la réponse de XML en JSON ou en une forme fidèle au XML de JSON.
    -   [Replace string in body][Replace string in body] : recherche une sous-chaîne de demande ou de réponse et la remplace par une autre sous-chaîne.
    -   [Set query string parameter][Set query string parameter] : ajoute, supprime un paramètre de chaîne de requête de la demande ou le remplace par une autre valeur.
-   [Stratégies de mise en cache][Stratégies de mise en cache]

    -   [Store to cache][Store to cache] : met en cache la réponse en fonction de la configuration de cache spécifiée.
    -   [Get from cache][Get from cache] : effectue une recherche dans le cache et renvoie une réponse mise en cache valide si elle est disponible.
-   [Autres stratégies][Autres stratégies]

    -   [Rewrite URI][Rewrite URI] : convertit une URL de demande de sa forme publique en une forme attendue par le service web.
    -   [Mask URLS in content][Mask URLS in content] : réécrit (masque) les liens dans le corps de la réponse et dans l'en-tête de l'emplacement afin qu'ils pointent vers un lien équivalent via le proxy.
    -   [Allow cross-domain calls][Allow cross-domain calls] : rend l'API accessible depuis les navigateurs clients utilisant Adobe Flash et Microsoft Silverlight.
    -   [JSONP][JSONP] : ajoute une prise en charge de JSON avec remplissage (JSONP) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients utilisant JavaScript.
    -   [CORS][CORS] : ajoute une prise en charge partage des ressources cross-origin (CORS) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients.

## <a name="access-restriction-policies"> </a>Accès aux stratégies de restriction

-   [Usage quota][Usage quota] : vous permet d'appliquer un volume et/ou un quota de bande passante renouvelable ou illimité.
-   [Rate limit][Rate limit] : empêche les pics d'utilisation de l'API en limitant les appels et/ou le taux de consommation de la bande passante.
-   [Caller IP restriction][Caller IP restriction]  : filtre (autorise/rejette) les appels de certaines adresses IP spécifiques ou de certaines plages d'adresses.

### <a name="usage-quota"> </a> Usage quota

**Description :**
vous permet d'appliquer un volume d'appels et/ou un quota de bande passante renouvelable ou illimité.

**Instruction de la stratégie :**

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
        <api name="name" calls="number" bandwidth="kilobytes">
            <operation name="name" calls="number" bandwidth="kilobytes" />
        </api>
    </quota>

**Exemple :**

    <policies>
        <inbound>
            <base />
            <quota calls="10000" bandwidth="40000" renewal-period="3600" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Champ d'application :**
utilisée dans la section entrante au niveau de la portée du produit.

**Champ d'application :**
lorsque l'utilisation des opérations et/ou des API doit être limitée pour un produit.

**Application ou non :**
pour définir les limites d'utilisation d'un produit en fonction du temps et de la bande passante. Lorsqu'une API atteint la limite de quota définie, les appels qui suivent sont rejetés avec un message d'erreur. Les quotas sont normalement définis selon le nombre de messages de demande autorisés dans une période définie et une limite de bande passante donnée.

| Attribut                 | Description                                                                                                                                                             |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| quota calls="number"     | Nombre maximal d'appels d'abonnement autorisés au cours de la période de renouvellement (soit 10000)                                                                    |
| bandwidth="kilobytes"    | Nombre maximal de kilo-octets qui peuvent être consommés pour ce produit, cette API ou cette opération au cours de la période de renouvellement spécifiée (soit 40000). |
| renewal-period="seconds" | Durée en secondes pendant laquelle le quota s'applique (soit 3600).                                                                                                     |
| api name="name"          | Nom de l'appel de l'API auquel s'applique le quota.                                                                                                                     |
| calls="number"           | Nombre maximal d'appels à l'API ou à l'opération qui peuvent être passés au cours de la période de renouvellement spécifiée (soit 5000).                                |
| operation name="name"    | Nom de l'opération à laquelle s'applique le quota.                                                                                                                      |

### <a name="rate-limit"> </a> Rate limit

**Description :**
empêche les pics d'utilisation de l'API en limitant le débit des demandes transmises à une API et éventuellement à une opération d'API spécifique. Lorsque la stratégie est déclenchée, le consommateur reçoit un code d'état de réponse `429 Too Many Requests`.

**Instruction de la stratégie :**

    <rate-limit calls="number" renewal-period="seconds">
        <api name="name" calls="number" renewal-period="seconds">
            <operation name="name" calls="number" renewal-period="seconds" />
        </api>
    </rate-limit>

**Exemple :**

    <policies>
        <inbound>
            <base />
            <rate-limit calls="20" renewal-period="90" />
        </inbound>
        <outbound>
            <base />        
        </outbound>
    </policies>

**Champ d'application :**
utilisée dans la section entrante au niveau de la portée du produit.

| Élément/attribut         | Description                                                            |
|--------------------------|------------------------------------------------------------------------|
| calls="number"           | nombre d'appels autorisés au cours de la période de renouvellement     |
| renewal-period="seconds" | période à la fin de laquelle le quota de limite d'appels se renouvelle |
| api name="name"          | nom de l'API à laquelle la limite s'applique                           |
| operation name="name"    | Nom de l'opération à laquelle la limite s'applique                     |

### <a name="caller-ip-restriction"> </a> Caller IP restriction

**Description :**
filtre (autorise/rejette) les appels de certaines adresses IP spécifiques ou de certaines plages d'adresses.

**Instruction de la stratégie :**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Exemple :**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Champ d'application :**
utilisée dans la section entrante d'une portée.

**Moment de l'application :**
utilisez cette stratégie lorsque vous avez besoin d'un contrôle spécifique sur les utilisateurs qui ont accès à votre service.

**Application ou non :**
cette stratégie n'est obligatoire que lorsqu'un contrôle strict de l'accès est nécessaire (par exemple pour les services avec des conditions de sécurité très sévères) selon les hôtes ou selon une plage d'hôtes.

| Attribut                                  | Description                                                                        |
|-------------------------------------------|------------------------------------------------------------------------------------|
| ip-filter action="allow | forbid"         | Spécifie si les appels doivent être autorisés ou non pour les adresses spécifiées. |
| address="address"                         | Une ou plusieurs adresses IP pour lesquelles autoriser ou refuser l'accès.         |
| address-range from="address" to="address" | Plage d'adresses IP pour lesquelles autoriser ou refuser l'accès.                  |

## <a name="content-transformation-policies"> </a>Stratégies de transformation du contenu

-   [Set HTTP header][Set HTTP header] : affecte une valeur à un en-tête de réponse et/ou de demande existant ou bien ajoute un nouvel en-tête de réponse et/ou de demande.
-   [Convert XML to JSON][Convert XML to JSON] : convertit le corps de la demande ou de la réponse de XML en JSON ou en une forme fidèle au XML de JSON.
-   [Replace string in body][Replace string in body] : recherche une sous-chaîne de demande ou de réponse et la remplace par une autre sous-chaîne.
-   [Set query string parameter][Set query string parameter] : ajoute, supprime un paramètre de chaîne de requête de la demande ou le remplace par une autre valeur.

### <a name="set-http-header"> </a> Set HTTP header

**Description :**
affecte une valeur à un en-tête de réponse et/ou de demande existant ou bien ajoute un nouvel en-tête de réponse et/ou de demande.

Insère une liste d'en-têtes HTTP dans un message HTTP. Lorsqu'elle est placée dans un pipeline entrant, cette stratégie définit les en-têtes HTTP pour la demande transmise au service cible. Lorsqu'elle est placée dans un pipeline sortant, cette stratégie définit les en-têtes HTTP pour la réponse envoyée au client du proxy.

**Instruction de la stratégie :**

    <set-header name="header name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple headers with the same name add additional value elements-->
    </set-header>

**Exemple :**

    <set-header exists-action="override">
        <header name="some value to set" exists-action="override">
        <value>20</value> 
        </header>
    </set-header>

**Champ d'application :**
à utiliser dans les sections entrante et sortante au niveau de n'importe quelle portée autre que celle de l'*éditeur*.

**Moment d'application :**
utilisez cette stratégie pour spécifier les paramètres de fonctionnement et/ou les valeurs de retour d'une transaction HTTP.

**Application ou non :**
la plupart des demandes et de nombreuses réponses HTTP demandent aux en-têtes de spécifier les paramètres d'entrée et de réponse. Cette stratégie est donc susceptible d'être applicable à la plupart de vos services.

| Attribut                                 | Description                                                                                                                                                                                                                                                                                                              |
|------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| reconcile-action="override|skip|append"  | Spécifie l'action à entreprendre lorsqu'un en-tête est déjà spécifié. Remarque : lorsque la valeur est « override », l'ajout de plusieurs entrées portant le même nom fait que l'en-tête est défini selon toutes les entrées (qui figurent plusieurs fois). Seules les valeurs listées seront définies dans le résultat. |
| header name="header name"                | Spécifie le nom de l'en-tête à définir. Obligatoire.                                                                                                                                                                                                                                                                     |
| exists-action="override | skip | append" | Spécifie l'action à entreprendre lorsque l'en-tête est déjà spécifié.                                                                                                                                                                                                                                                    |
| value="value"                            | Spécifie la valeur de l'en-tête à définir.                                                                                                                                                                                                                                                                               |

### <a name="convert-xml-to-json"> </a> Convert XML to JSON

**Description :**
convertit le corps de la demande ou de la réponse de XML en JSON.

**Instruction de la stratégie :**

    <xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>

**Exemple :**

    <policies>
        <inbound>
            <base />
        </inbound>
        <outbound>
            <base />
            <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
        </outbound>
    </policies>

**Champ d'application :**
à utiliser dans la section entrante ou dans la section sortante au niveau de la portée de l'API ou de l'opération.

**Application ou non :**
pour moderniser les API basées sur des services web basés uniquement sur XML.

| Attribut                              | Description                                                                                                                                |
|---------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| kind="javascript-friendly | direct    | Le JSON converti présente un format familier aux développeurs JavaScript | Le JSON converti reflète la structure originale du document XML |
| apply= always | content-type-xml      | Toujours convertir | Ne convertir que si l'en-tête `Content-Type` indique la présence de XML                                               |
| consider-accept-header="true | false" | Appliquer la conversion basée sur la valeur de l'en-tête `Accept` | Toujours convertir                                                     |

### <a name="replace-string-in-body"> </a> Replace string in body

**Description :**
recherche une sous-chaîne dans la demande ou la réponse et la remplace par une autre sous-chaîne.

**Instruction de la stratégie :**

    <find-and-replace from="what to replace" to="replacement" />

**Exemple :**

    <find-and-replace from="notebook" to="laptop" />

**Champ d'application :**
utilisée dans la section entrante et sortante d'une portée.

| Attribut               | Description                                      |
|------------------------|--------------------------------------------------|
| from="what to replace" | Chaîne à rechercher.                             |
| to="replacement"       | Chaîne à mettre à la place de la chaîne trouvée. |

### <a name="set-query-string-parameter"> </a> Set query string parameter

**Description :**
ajoute, supprime un paramètre de chaîne de requête de la demande ou le remplace par une autre valeur.

**Instruction de la stratégie :**

    <set-query-parameter name="param name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
    </set-query-parameter>

**Exemple :**

    <policies>
        <inbound>
            <base />
            <set-query-parameter>
                <parameter name="api-key" exists-action="skip">
                    <value>12345678901</value>
                </parameter>
                <!-- for multiple parameters with the same name add additional value elements -->
            </set-query-parameter>
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Champ d'application :**
utilisée dans la section entrante d'une portée.

**Application ou non :**
pour transmettre les paramètres de requête attendus par le service principal et qui sont facultatifs ou ne sont jamais présents dans la demande.

| Élément/attribut         | Description                                                       |
|--------------------------|-------------------------------------------------------------------|
| name="name"              | Chaîne donnant un nom au paramètre                                |
| exists-action="override" | remplace la valeur du paramètre, s'il est présent dans la demande |
| exists-action="skip"     | ne fait rien si le paramètre est présent dans la demande          |
| exists-action="append"   | ajoute la valeur au paramètre existant de la demande              |
| exists-action="delete"   | supprime le paramètre de la demande*                             |
| value="value"            | définit la valeur du paramètre dans l'élément de fermeture        |

## <a name="caching-policies"> </a>Stratégies de mise en cache

-   [Store to cache][Store to cache] : met en cache la réponse en fonction de la configuration de cache spécifiée.
-   [Get from cache][Get from cache] : effectue une recherche dans le cache et renvoie une réponse mise en cache valide si elle est disponible.

### <a name="store-to-cache"> </a> Store to cache

**Description :**
met en cache la réponse en fonction des paramètres de cache spécifiés. Doit avoir une stratégie [Get From cache][Get from cache] correspondante.

**Instruction de la stratégie :**

    cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />

**Exemple :**

    <policies>
        <inbound>
            <base />
              <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
                    <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
                    <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
            </cache-lookup>
        </inbound>
        <outbound>
            <base />
                <cache-store duration="3600" caching-mode="cache-on" />
        </outbound>
    </policies>

**Champ d'application :**
peut être présente dans la section sortante au niveau de la portée de l'API ou de l'opération, ou bien à ces deux portées.

**Moment de l'application :**
à appliquer dans les cas où le contenu de la réponse reste statique pendant un certain temps.

**Application ou non :**
la mise en cache de la réponse réduit les besoins en bande passante et en calcul imposés par le serveur web principal et limite la latence perçue par les consommateurs de l'API.

| Élément/attribut        | Description                                                                                                                                                |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| secondes                | Durée de vie des entrées mises en cache (en secondes, par défaut=3600)                                                                                     |
| cache-on | do-not-cache | Les réponses sont mises en cache | Les réponses ne sont pas mises en cache et les en-têtes liés au cache ne peuvent pas effectuer de mise en cache en aval |

### <a name="get-from-cache"> </a> Get from cache

**Description :**
effectue une recherche dans le cache et renvoie une réponse mise en cache valide si elle est disponible. Répond aux demandes de validation du cache par les consommateurs de l'API. Doit avoir une stratégie [Store To cache][Store to cache] correspondante.

**Instruction de la stratégie :**

    <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
        <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
        <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
    </cache-lookup>

**Exemple :**

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
                <vary-by-query-parameter>version</vary-by-query-parameter>
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>         
        </inbound>
        <outbound>
            <cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />
            <base />        
        </outbound>
    </policies>

**Champ d'application :**
peut être présente dans la section entrante au niveau de la portée de l'API ou de l'opération, ou bien à ces deux portées.

**Moment de l'application :**
à appliquer dans les cas où le contenu de la réponse reste statique pendant un certain temps.

**Application ou non :**
la mise en cache de la réponse réduit les besoins en bande passante et en calcul imposés par le serveur web principal et limite la latence perçue par les consommateurs de l'API.

| Élément/attribut                                  | Description                                                                                                                                                                                                                                                |
|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| vary-by-developer="true | false"                  | Définir sur *true* pour commencer la mise en cache des réponses selon la clé de développeur. *false* par défaut.                                                                                                                                       |
| vary-by-developer-groups="true | false"           | Définir sur *true* pour commencer la mise en cache des réponses selon le rôle de l'utilisateur. *false* par défaut.                                                                                                                                    |
| downstream-caching-type="none | private | public" | *none* - la mise en cache en aval n'est pas autorisée ; valeur par défaut | *private* - mise en cache privée en aval autorisée | *public* - mise en cache privée et partagée en aval autorisée.                                                      |
| vary-by-header : "Accept"                         | Commencer la mise en cache des réponses selon la valeur de l'en-tête `Accept`                                                                                                                                                                              |
| vary-by-header : Accept-Charset"                  | Commencer la mise en cache des réponses selon la valeur de l'en-tête `Accept-Charset`                                                                                                                                                                      |
| vary-by-header : "header name"                    | Commencer la mise en cache des réponses selon la valeur de l'en-tête, par exemple `Accept | Accept-Charset | Accept-Encoding | Accept-Language | Authorization | Expect | From | Host | If-Match`                                                          |
| vary-by-query-parameter : "parameter name"        | Commencer la mise en cache des réponses selon la valeur des paramètres de requête spécifiés. Entrez un ou plusieurs paramètres. Utilisez un point-virgule comme séparateur. Si vous n'en spécifiez aucun, tous les paramètres de la requête sont utilisés. |

## <a name="other-policies"> </a>Autres stratégies

-   [Rewrite URI][Rewrite URI] : convertit une URL de demande de sa forme publique en une forme attendue par le service web.
-   [Mask URLS in content][Mask URLS in content] : réécrit (masque) les liens dans le corps de la réponse et dans l'en-tête de l'emplacement afin qu'ils pointent vers un lien équivalent via le proxy.
-   [Allow cross-domain calls][Allow cross-domain calls] : rend l'API accessible depuis les navigateurs clients utilisant Adobe Flash et Microsoft Silverlight.
-   [JSONP][JSONP] : ajoute une prise en charge de JSON avec remplissage (JSONP) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients utilisant JavaScript.
-   [CORS][CORS] : ajoute une prise en charge partage des ressources cross-origin (CORS) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients.

### <a name="rewrite-uri"> </a> Rewrite URI

**Description :**
convertit une URL de demande de sa forme publique en une forme attendue par le service web.

**URL publique :** <http://api.example.com/storenumber/ordernumber>

**URL de demande :** <http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State>.

N'ajoutez pas de paramètres de chemin de modèle dans l'URL de réécriture. Vous ne pouvez ajouter que des paramètres de chaîne de requête.

**Instruction de la stratégie :**

    <rewrite-uri template="uri template" />

**Exemple :**

    <policies>
        <inbound>
            <base />
            <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Champ d'application :**
utilisée dans la section entrante au niveau de la portée de l'opération uniquement.

**Moment de l'application :**
à utiliser lorsqu'une URL humaine et/ou compréhensible par un navigateur doit être transformée selon le format d'URL attendu par le service web.

**Application ou non :**
à utiliser uniquement lors de l'utilisation d'un autre format d'URL. Les URL propres, les URL RESTful, les URL conviviales ou les URL adaptées au SEO sont des URL purement structurelles qui ne contiennent pas de chaîne de requête et ne contiennent que le chemin de la ressource (après le schéma et l'autorité). C'est souvent le cas pour des raisons d'esthétique, de simplicité d'utilisation ou bien pour l'optimisation des résultats de recherche (SEO).

| Attribut                | Description                                                        |
|-------------------------|--------------------------------------------------------------------|
| template="uri template" | URL de service web réelle avec les paramètres de chaîne de requête |

### <a name="mask-urls-in-content"> </a> Mask URLS in content

**Description :**
réécrit (masque) les liens dans le corps de la réponse et dans l'en-tête de l'emplacement afin qu'ils pointent vers un lien équivalent via le proxy.

**Instruction de la stratégie :**

    <redirect-body-urls />

**Exemple :**

    <redirect-body-urls />

**Champ d'application :**
appliquer au niveau des portées de l'*API* et de l'*opération*. Peut être appliquée à la section entrante ou à la section sortante.

### <a name="allow-cross-domain-calls"> </a> Allow cross-domain calls

**Description :**
rend l'API accessible depuis les navigateurs clients utilisant Adobe Flash et Microsoft Silverlight.

**Instruction de la stratégie :**

    <cross-domain />

**Exemple :**

    <cross-domain />

**Champ d'application :**
utilisée dans la section entrante au niveau de la portée des *éditeurs*.

### <a name="jsonp"> </a> JSONP

**Description :**
ajoute une prise en charge de JSON avec remplissage (JSONP) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients utilisant JavaScript. JSONP est une méthode utilisée par les programmes JavaScript pour demander des données à un serveur se trouvant dans un autre domaine. JSONP passe outre la limite appliquée par la plupart des navigateurs web, selon laquelle l'accès aux pages web doit se trouver dans le même domaine.

**Instruction de la stratégie :**

    <jsonp callback-parameter-name="callback function name" />

**Exemple :**

    <jsonp callback-parameter-name="cb" />

Si vous appelez la méthode sans le paramètre de rappel `?cb=XXX`, elle renvoie un code JSON simple (sans enveloppe d'appel de fonction).

Si vous ajoutez le paramètre de rappel `?cb=XXX`, il renvoie un résultat JSONP, enveloppant les résultats JSON d'origine autour de la fonction de rappel, comme dans `XXX('<json result goes here>')`;

**Champ d'application :**
à utiliser dans la section sortante uniquement.

**Moment de l'application :**
pour demander des données à un serveur se trouvant dans un autre domaine.

| Attribut                | Description                                                                                                              |
|-------------------------|--------------------------------------------------------------------------------------------------------------------------|
| callback-parameter-name | Appel de fonction JavaScript interdomaines avec comme préfixe le nom de domaine complet de l'emplacement de la fonction. |

### <a name="cors"> </a> CORS

**Description :**
ajoute une prise en charge partage des ressources cross-origin (CORS) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients.

CORS permet à un navigateur et à un serveur d'interagir et de déterminer si les demandes cross-origin doivent être autorisées ou non, par exemple dans le cas d'appels XMLHttpRequests passés via JavaScript sur une page web vers d'autres domaines). Cette stratégie offre plus de flexibilité que de simplement autoriser les demandes de même origine, mais elle est plus sûre que d'autoriser toutes les demandes cross-origin.

**Instruction de la stratégie :**

     <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>URI</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
     </cors>

**Exemple :**

    <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>http://contoso.com:81</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
    </cors>

**Champ d'application :**
à utiliser dans la section entrante, uniquement au niveau des portées de l'*API* et de l'*opération*.

| Attribut             | Description                                                                                                                                     |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| <origin>*</origin>  | Autoriser n'importe quel URI ou une liste spécifique d'URI                                                                                      |
| <origin>URI</origin> | L'URI doit comprendre un modèle, un hôte et un port. Si le port n'est pas spécifié, le port 80 est utilisé pour HTTP et le port 443 pour HTTPS. |

  [Stratégies dans Gestion des API]: ../api-management-howto-policies
  [Accès aux stratégies de restriction]: #access-restriction-policies
  [Usage quota]: #usage-quota
  [Rate limit]: #rate-limit
  [Caller IP restriction]: #caller-ip-restriction
  [Stratégies de transformation du contenu]: #content-transformation-policies
  [Set HTTP header]: #set-http-header
  [Convert XML to JSON]: #convert-xml-to-json
  [Replace string in body]: #replace-string-in-body
  [Set query string parameter]: #set-query-string-parameter
  [Stratégies de mise en cache]: #caching-policies
  [Store to cache]: #store-to-cache
  [Get from cache]: #get-from-cache
  [Autres stratégies]: #other-policies
  [Rewrite URI]: #rewrite-uri
  [Mask URLS in content]: #mask-urls-in-content
  [Allow cross-domain calls]: #allow-cross-domain-calls
  [JSONP]: #jsonp
  [CORS]: #cors

<!--HONumber=46--> 
