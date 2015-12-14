<properties 
	pageTitle="Informations sur les fonctionnalités et conditions de correspondance du moteur de règles Azure Content Delivery Network (CDN)" 
	description="Cette rubrique répertorie les descriptions détaillées des fonctionnalités et conditions de correspondance disponibles pour le moteur de règles Azure Content Delivery Network (CDN)." 
	services="cdn" 
	documentationCenter="" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# Informations sur les fonctionnalités et conditions de correspondance du moteur de règles CDN
	
Cette rubrique répertorie les descriptions détaillées des fonctionnalités et conditions de correspondance disponibles pour le [moteur de règles](cdn-rules-engine.md) Azure Content Delivery Network (CDN).

> [AZURE.NOTE]Le moteur de règles exige le niveau CDN Premium. Pour plus d’informations sur les fonctionnalités des niveaux CDN Standard et Premium, consultez [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md).

## Conditions de correspondance

Une condition de correspondance identifie des types spécifiques de requêtes pour lesquelles un ensemble de fonctionnalités est exécuté.

Par exemple, elle peut être utilisée pour filtrer les requêtes de contenu à un emplacement particulier, les requêtes générées à partir d’une adresse IP ou d’un pays particulier, ou filtrer les requête selon leurs informations d’en-tête.

### Toujours

La condition de correspondance Toujours est conçue pour appliquer un ensemble de fonctionnalités par défaut à toutes les requêtes.

### Appareil

La condition de correspondance Appareil identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés.

### Emplacement

Ces conditions de correspondance sont conçues pour identifier les requêtes selon l’emplacement du demandeur.

Nom | Objectif
-----|--------
Numéro AS | Identifie les requêtes issues d’un réseau particulier.
Pays | Identifie les requêtes provenant des pays spécifiés.
 

### Origine

Ces conditions de correspondance sont conçues pour identifier les requêtes qui pointent vers le stockage CDN ou le serveur d’origine d’un client.

Nom | Objectif
-----|--------
Origine CDN | Identifie les requêtes de contenu stocké sur le stockage CDN.
Origine client | Identifie les requêtes de contenu stocké sur le serveur d’origine d’un client spécifique.


### Demande

Ces conditions de correspondance sont conçues pour identifier les requêtes selon leurs propriétés.

Nom | Objectif
-----|--------
Adresse IP du client | Identifie les requêtes issues d’une adresse IP particulière.
Paramètre de cookie | Recherche la valeur spécifiée dans les cookies associés à chaque requête.
Expression régulière de paramètre de cookie | Recherche l’expression régulière spécifiée dans les cookies associés à chaque requête.
Cname Edge | Identifie les requêtes qui pointent vers un CNAME de périphérie spécifique.
Domaine de référence | Identifie les requêtes qui ont été référencées à partir des noms d’hôte spécifiés.
Littéral d’en-tête de requête | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur spécifiée.
Expression régulière d’en-tête de requête | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
Caractère générique d’en-tête de requête | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur qui correspond au modèle spécifié.
Méthode de requête | Identifie les requêtes par leur méthode HTTP.
Modèle de requête | Identifie les requêtes par leur protocole HTTP.

### URL

Ces conditions de correspondance sont conçues pour identifier les requêtes selon leurs URL.

Nom | Objectif
-----|--------
Répertoire du chemin d’URL | Identifie les requêtes par leur chemin relatif.
Extension du chemin d’URL | Identifie les requêtes par leur extension de nom de fichier.
Nom de fichier du chemin d’URL | Identifie les requêtes par leur nom de fichier.
Littéral du chemin d’URL | Compare le chemin relatif d’une requête à la valeur spécifiée.
Expression régulière du chemin d’URL | Compare le chemin relatif d’une requête à l’expression régulière spécifiée.
Caractère générique du chemin d’URL | Compare le chemin relatif d’une requête au modèle spécifié.
Littéral de requête d’URL | Compare la chaîne de requête d’une requête à la valeur spécifiée.
Paramètre de requête d’URL | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond au modèle spécifié.
Expression régulière de requête d’URL | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
Caractère générique de requête d’URL | Compare les valeurs spécifiées à la chaîne de requête de la requête.

## Caractéristiques

Une fonctionnalité définit le type d’action appliqué au type de requête identifié par un ensemble de conditions de correspondance.

### Access

Ces fonctionnalités sont conçues pour contrôler l’accès au contenu.

Nom | Objectif
-----|--------
Refuser l’accès | Détermine si toutes les requêtes sont rejetées avec une réponse 403 Interdit.
Authentification de jeton | Détermine si l’authentification basée sur les jetons est appliquée à une requête. 
Code de refus d’authentification de jeton | Détermine le type de réponse à retourner à un utilisateur quand une requête est refusée en raison de l’authentification basée sur les jetons. 
Ignorer la casse de l’URL pour l’authentification de jeton | Détermine si les comparaisons d’URL effectuées par l’authentification basée sur les jetons respectent la casse. 
Paramètre d’authentification de jeton | Détermine si le paramètre de chaîne de requête de l’authentification basée sur les jetons doit être renommé.

### Mise en cache

Ces fonctionnalités sont conçues pour personnaliser la mise en cache du contenu.

Nom | Objectif
-----|--------
Paramètres de bande passante | Détermine si les paramètres de limitation de bande passante (c.-à-d., ec\_rate et ec\_prebuf) sont actifs.
Limitation de bande passante | Limite la bande passante pour la réponse fournie par nos serveurs Edge. 
Ignorer le cache | Détermine si la requête peut exploiter notre technologie de mise en cache.
Traitement d’en-tête de contrôle de cache | Contrôle la génération des en-têtes de contrôle de cache par le serveur Edge quand la fonctionnalité Âge maximal externe est active.
Chaîne de requête de clé de cache | Détermine si la clé de cache inclut ou exclut les paramètres de chaîne de requête associés à une requête. 
Réécriture de la clé de cache | Réécrit la clé de cache associée à une requête. 
Remplissage de cache complet | Détermine ce qui se passe quand une requête génère une absence de cache partielle sur un serveur Edge. 
Compresser les types de fichiers | Définit les formats de fichier à compresser sur le serveur. 
Âge maximal interne par défaut | Détermine l’intervalle d’âge maximal par défaut pour la revalidation du cache entre le serveur Edge et le serveur d’origine.
Expiration du traitement d’en-tête | Contrôle la génération de l’expiration des en-têtes par un serveur Edge quand la fonctionnalité Âge maximal externe est active. 
Âge maximal externe | Détermine l’intervalle d’âge maximal pour la revalidation du cache entre le navigateur et le serveur Edge. 
Forcer l’âge maximal interne | Détermine l’intervalle d’âge maximal pour la revalidation du cache entre le serveur Edge et le serveur d’origine. 
Prise en charge H.264 (téléchargement progressif HTTP) | Détermine les types de formats de fichier H.264 qui peuvent être utilisés pour diffuser du contenu. 
Honorer la requête non-cache | Détermine si les requêtes non-cache d’un client HTTP sont transmises au serveur d’origine.
Ignorer la requête non-cache d’origine | Détermine si notre CDN ignore certaines directives remises par un serveur d’origine.
Ignorer les plages inacceptables | Détermine la réponse à retourner aux clients quand une requête génère un code d’état 416 Plage demandée non satisfaisante. 
Obsolescence maximale interne | Contrôle la durée après l’expiration normale d’une ressource mise en cache pendant laquelle cette ressource peut être remise depuis un serveur Edge quand ce serveur Edge ne parvient pas à la revalider avec le serveur d’origine.
Partage de cache partiel | Détermine si une requête peut générer du contenu partiellement mis en cache. 
Prévalider le contenu mis en cache | Détermine si du contenu mis en cache peut faire l’objet d’une revalidation anticipée avant l’expiration de sa durée de vie. 
Actualiser les fichiers de cache de zéro octet | Détermine la façon dont nos serveurs Edge gèrent la requête d’un client HTTP liée à une ressource de cache de 0 octet.
Définir les codes d’état pouvant être mis en cache | Définit l’ensemble des codes d’état qui peuvent générer du contenu mis en cache. 
Rendre obsolète la remise de contenu en cas d’erreur | Détermine si du contenu mis en cache qui a expiré est remis quand une erreur se produit pendant la revalidation du cache ou l’extraction du contenu demandé à partir du serveur d’origine du client.
Rendre obsolète pendant la revalidation | Améliore les performances en permettant à nos serveurs Edge de rendre obsolète le client pour le demandeur pendant la revalidation. 
Commentaire | La fonctionnalité Commentaire permet d’ajouter une remarque dans une règle. 

### En-têtes

Ces fonctionnalités sont conçues pour ajouter, modifier ou supprimer des en-têtes de la demande ou réponse.

Nom | Objectif
-----|--------
En-tête de réponse Age | Détermine si un en-tête de réponse Age est inclus dans la réponse envoyée au demandeur. 
En-têtes de réponse de cache de débogage | Détermine si une réponse peut inclure l’en-tête de réponse X-EC-Debug qui fournit des informations sur la stratégie de cache de la ressource demandée.
Modifier l’en-tête de requête client | Remplace, complète ou supprime un en-tête dans une requête.
Modifier l’en-tête de réponse client | Remplace, complète ou supprime un en-tête dans une réponse.
Définir un en-tête personnalisé d’adresse IP client | Permet d’ajouter l’adresse IP du client demandeur à la requête sous la forme d’en-tête de requête personnalisé. 

### Journaux

Ces fonctionnalités sont conçues pour personnaliser les données stockées dans les fichiers journaux bruts.

Nom | Objectif
-----|--------
Champ de fichier journal personnalisé 1 | Détermine le format et le contenu à attribuer au champ de fichier journal personnalisé dans un fichier journal brut. 
Journaliser la chaîne de requête | Détermine si une chaîne de requête est stockée avec l’URL dans les journaux d’accès.

### Optimisation

Ces fonctionnalités déterminent si une requête subit les optimisations fournies par Edge Optimizer.

Nom | Objectif
-----|--------
Edge Optimizer | Détermine si Edge Optimizer peut être appliqué à une requête. 
Edge Optimizer – Instancier la configuration | Instancie ou active la configuration Edge Optimizer associée à un site. 
 

### Origine

Ces fonctionnalités sont conçues pour contrôler la manière dont le CDN communique avec un serveur d’origine.

Nom | Objectif
-----|--------
Nombre maximal de requêtes toujours actives | Définit le nombre maximal de requêtes pour une connexion toujours active avant sa fermeture. 
En-têtes spéciaux de proxy | Définit l’ensemble des en-têtes de requête propres à CDN à transmettre depuis un serveur Edge vers un serveur d’origine. 

### Spécialité

Ces fonctionnalités sont des fonctionnalités avancées que seuls les utilisateurs expérimentés doivent utiliser.

Nom | Objectif
-----|--------
Méthodes HTTP pouvant être mises en cache | Détermine l’ensemble des méthodes HTTP supplémentaires pouvant être mises en cache sur notre réseau.
Taille de corps de requête pouvant être mise en cache | Définit le seuil permettant de déterminer si une réponse POST peut être mise en cache. 
 

### URL

Ces fonctionnalités permettent de rediriger ou de réécrire une requête vers une URL différente.

Nom | Objectif
-----|--------
Suivre les redirections | Détermine si les requêtes peuvent être redirigées vers le nom d’hôte défini dans l’en-tête Location retourné par un serveur d’origine du client. 
Redirection d'URL | Redirige les requêtes via l’en-tête Location. 
Réécriture d’URL | Réécrit l’URL de la requête. 

### Pare-feu d’applications web

La fonctionnalité Pare-feu d’applications web détermine si une requête est filtrée par un pare-feu d’applications web.

## Voir aussi
* [Vue d'ensemble d'Azure CDN](cdn-overview.md)
* [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-rules-engine.md)

<!---HONumber=AcomDC_1203_2015-->