<properties
	pageTitle="Utilisation d’Azure CDN avec CORS | Microsoft Azure"
	description="Découvrez comment utiliser Azure CDN (Content Delivery Network) avec CORS (Cross-Origin Resource Sharing)."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="casoper"/>
    
# Utilisation d’Azure CDN avec CORS     

## Présentation de CORS

CORS (Cross Origin Resource Sharing) est une fonctionnalité HTTP qui permet à une application web exécutée dans un domaine d’accéder aux ressources d’un autre domaine. Pour réduire le risque d’attaques de script entre sites, tous les navigateurs web modernes implémentent une restriction de sécurité appelée [stratégie de même origine](http://www.w3.org/Security/wiki/Same_Origin_Policy). Celle-ci empêche une page web d’appeler des API dans un autre domaine. CORS permet à un domaine (le domaine d’origine) d’appeler des API dans un autre domaine de façon sécurisée.
 
## Fonctionnement
1.	Le navigateur envoie la demande OPTIONS avec un en-tête HTTP **Origin**. La valeur de cet en-tête est le domaine qui a servi la page parente. Quand une page à partir de https://www.contoso.com tente d’accéder aux données d’un utilisateur dans le domaine fabrikam.com, l’en-tête de demande suivant est envoyé à fabrikam.com :
    
    `Origin: https://www.contoso.com`
 
2.	Le serveur peut répondre des formes suivantes :
    - Un en-tête **Access-Control-Allow-Origin** dans sa réponse indiquant les sites d’origine autorisés. Par exemple :
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Une page d’erreur si le serveur n’autorise pas la demande cross-origin.
    - Un en-tête **Access-Control-Allow-Origin** avec un caractère générique qui autorise tous les domaines :
        
        `Access-Control-Allow-Origin: *`
 
Pour les requêtes HTTP complexes, une demande « préliminaire » est effectuée pour déterminer si la totalité de la demande peut être envoyée.
 
## Scénarios avec caractère générique ou à origine unique

CORS sur Azure CDN fonctionne automatiquement sans aucune configuration supplémentaire quand l’en-tête **Access-Control-Allow-Origin** est défini sur le caractère générique (*) ou une origine unique. Le CDN met en cache la première réponse, et les demandes suivantes utilisent le même en-tête.
 
Si les demandes sont communiquées au CDN avant que CORS ne soit défini sur l’origine, vous devez vider le contenu de votre point de terminaison pour recharger le contenu avec l’en-tête **Access-Control-Allow-Origin**.
 
## Scénarios avec plusieurs origines

Si une liste d’origines spécifique doit être autorisée pour CORS, les choses se compliquent un peu plus. Le problème se produit quand le CDN met en cache l’en-tête **Access-Control-Allow-Origin** pour la première origine CORS. Quand une autre origine CORS effectue une demande ultérieure, le CDN sert l’en-tête **Access-Control-Allow-Origin** mis en cache, qui ne correspond pas. Il existe plusieurs façons de corriger cette situation.
 
### CDN Azure Premium fourni par Verizon

La meilleure façon de procéder consiste à utiliser **CDN Azure Premium fourni par Verizon**, qui expose certaines fonctionnalités avancées.
 
Vous devez [créer une règle](cdn-rules-engine.md) pour vérifier l’en-tête **Origin** dans la demande. S’il s’agit d’une origine valide, votre règle définit l’en-tête **Access-Control-Allow-Origin** avec l’origine fournie dans la demande. Si l’origine spécifiée dans l’en-tête **Origin** n’est pas autorisée, votre règle doit omettre l’en-tête **Access-Control-Allow-Origin**, ce qui amène le navigateur à rejeter la demande.
 
Vous pouvez mettre en place cette procédure de deux façons avec le moteur de règles. Dans les deux cas, l’en-tête **Access-Control-Allow-Origin** issu du serveur d’origine du fichier est ignoré en totalité, et le moteur de règles du CDN gère complètement les origines CORS autorisées.

#### Une expression régulière avec toutes les origines valides
 
Dans ce cas, vous allez créer une expression régulière qui inclut toutes les origines que vous souhaitez autoriser :

	https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] **CDN Azure fourni par Verizon** utilise [PCRE (Perl Compatible Regular Expressions)](http://pcre.org/) comme moteur pour les expressions régulières. Vous pouvez utiliser un outil tel que [Regular Expressions 101](https://regex101.com/) pour valider votre expression régulière. Notez que le caractère « / » est valide dans les expressions régulières et n’a pas besoin d’être échappé ; toutefois, échapper ce caractère est une pratique recommandée et attendue par certains validateurs d’expression régulière.

Si l’expression régulière correspond, votre règle remplace l’en-tête **Access-Control-Allow-Origin** (le cas échéant) de l’origine par l’origine qui a envoyé la demande. Vous pouvez également ajouter des en-têtes CORS, comme **Access-Control-Allow-Methods**.

![Exemple de règles avec expression régulière](./media/cdn-cors/cdn-cors-regex.png)
 
#### Règle d’en-tête de demande pour chaque origine

Au lieu de recourir à des expressions régulières, vous pouvez créer une règle pour chaque origine à autoriser en utilisant la [condition de correspondance](cdn-rules-engine-details.md#match-conditions) **Request Header Wildcard** (Caractère générique pour l’en-tête de la demande). Comme dans le cas de la méthode des expressions régulières, seul le moteur de règles définit les en-têtes CORS.
  
![Exemple de règles sans expression régulière](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] Dans l’exemple ci-dessus, l’utilisation du caractère générique * indique au moteur de règles que HTTP et HTTPS satisfont tous les deux à la condition.
 
### Azure CDN Standard

Sur les profils Azure CDN Standard, le seul mécanisme autorisant plusieurs origines sans recourir à l’origine avec caractère générique consiste à utiliser la [mise en cache de chaîne de requête](cdn-query-string.md). Vous devez activer le paramètre de chaîne de requête pour le point de terminaison CDN, puis utiliser une chaîne de requête unique pour les demandes à partir de chaque domaine autorisé. Ainsi, le CDN met en cache un objet distinct pour chaque chaîne de requête unique. Cette approche n’est pas idéale, toutefois, car plusieurs copies du même fichier sont mises en cache sur le CDN.

<!---HONumber=AcomDC_0803_2016-->