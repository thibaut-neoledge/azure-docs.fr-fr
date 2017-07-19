2017-07-07

Ces conditions d’utilisation et d’affichage s’appliquent à votre implémentation du contenu et aux informations associées (par exemple, les relations, les métadonnées et les autres signaux) disponibles au moyen d’un recours aux API recherche Web Bing, recherche d’images, recherche de vidéos, recherche d’actualités et recherche d’entités, ainsi qu’aux API Vérification orthographique Bing et Suggestion automatique Bing. Des détails sur l’implémentation associés à ces conditions sont disponibles dans la documentation pour des fonctionnalités et des résultats spécifiques.

## <a name="1-bing-spell-check-api-and-bing-autosuggest-api"></a>1. API VÉRIFICATION ORTHOGRAPHIQUE BING ET API SUGGESTION AUTOMATIQUE BING.

Vous ne devez pas :

•   copier, stocker ou mettre en cache des données provenant des API Vérification orthographique Bing ou Suggestion automatique Bing ; ni 
  
•   utiliser les données provenant des API Vérification orthographique Bing ou Suggestion automatique Bing dans le cadre de Machine Learning ou d’une activité algorithmique similaire pour former, évaluer ou améliorer des services nouveaux ou existants que vous ou des parties tierces pourriez proposer.

## <a name="2-search-apis"></a>2. API RECHERCHE

La configuration requise dans les sections 3 à 7 s’applique aux API recherche Web Bing, recherche d’images, recherche de vidéos, recherche d’actualités et recherche d’entités («**API Recherche**»). Les définitions suivantes s’appliquent aux sections 3 à 7 :

•  « answer » fait référence à une catégorie de résultats renvoyés dans une réponse. Par exemple, une réponse de l’API Recherche web Bing peut inclure des réponses dans les catégories des résultats de la page web, des images, des vidéos et des actualités ;  
  
•  « response » signifie toutes les réponses et toutes les données associées reçues en réponse à un seul appel à un API de recherche ;  
  
•  « result » fait référence à un élément d’information dans une réponse. Par exemple, le jeu de données connecté à un article d’actualités est un résultat dans une réponse d’actualités.

## <a name="3-bing-entity-search-api"></a>3. API RECHERCHE D’ENTITÉS BING

Les données retournées par l’API recherche d’entités Bing doivent être :

•   proposées uniquement aux utilisateurs situés aux États-Unis ;  

•   affichées uniquement comme une indication de l’intérêt et de l’intention de recherche de l’utilisateur (par exemple, les requêtes de recherche indiquées par l’utilisateur) ;  
  
•   accompagnées d’un lien visible vers l’URL bing.com fournie dans la réponse, pour permettre à l’utilisateur d’accéder aux résultats de la recherche de la requête pertinente sur bing.com ;  
  
•   utilisées conformément à toutes les autres mesures appropriées pour garantir que l’utilisation des données reçues à partir de l’API recherche d’entités Bing ne viole pas la législation applicable ou les droits des tiers. Par exemple,  
  
  - en s’assurant que les images miniatures sont miniatures par rapport à l’affichage de l’utilisateur ; ou  
    
  - dans le cas d’une licence Creative Commons, en garantissant l’attribution appropriée de la licence et du texte.

Vous ne devez pas :

•   copier, stocker ou mettre en cache des données provenant de l’API recherche d’entités Bing ; ni  
  
•   utiliser les données provenant de l’API recherche d’entités Bing dans le cadre de Machine Learning ou d’une activité algorithmique similaire pour former, évaluer ou améliorer des services nouveaux ou existants que vous ou des parties tierces pourriez proposer.

## <a name="4-bing-web-search-image-search-news-search-and-video-search-apis"></a>4. API RECHERCHE WEB BING, RECHERCHE D’IMAGES, RECHERCHE D’ACTUALITÉS ET RECHERCHE DE VIDÉOS :

**Expérience de recherche Internet.** Toutes les données renvoyées dans les réponses des API recherche Web, d’images, d’actualités et de vidéos sont utilisables uniquement dans les expériences de recherche Internet. Une expérience de recherche Internet signifie que le contenu affiché, le cas échéant :

•   est pertinent et réactif à la requête directe de l’utilisateur final ou autre indication de l’intérêt et de l’intention de recherche de l’utilisateur (par exemple, les requêtes de recherche indiquées par l’utilisateur) ;  
  
•   permet aux utilisateurs de rechercher des sources de données et d’y accéder (par exemple, les URL fournies sont implémentées en tant que liens hypertexte de sorte que le contenu ou l’attribution est un lien actionnable affichant les données de manière visible) ;  
  
•   inclut plusieurs résultats parmi lesquels l’utilisateur final peut effectuer sa sélection (par exemple, plusieurs résultats ou tous les résultats de la réponse d’actualités sont affichés) ;  
  
•   est limité à une quantité appropriée pour servir l’objectif de recherche (par exemple, les images miniatures sont miniatures par rapport à l’affichage de l’utilisateur) ;  
  
•   inclut une indication visible pour l’utilisateur final signalant que le contenu est issu de résultats de recherche Internet (par exemple, une mention « Extrait du web ») ; et  
  
•   inclut toute autre combinaison de mesures appropriées pour garantir que l’utilisation des données reçues à partir des API de recherche ne viole pas la législation applicable ou les droits des tiers. Consultez vos conseillers juridiques pour déterminer les mesures semblant appropriées.

La seule exception à la condition d’expérience de recherche Internet concerne la détection d’URL, tel que décrit à la section 7 (Détection d’URL non affichées) ci-dessous.

**Généralités.** Vous ne devez pas : 

•   copier, stocker ou mettre en cache les données issues des réponses (sauf en cas de rétention dans la mesure permise par la section « Continuité de service » ci-dessous) ;  
  
•   modifier le contenu des résultats (sauf reformatage n’altérant pas toute autre condition) ;  
  
•   omettre l’attribution et les URL associées au contenu des résultats ;  
  
•   réorganiser (y compris par omission) des résultats affichés dans une réponse lorsqu’un ordre ou un classement est fourni ;  
  
•   afficher d’autres contenus dans une partie d’une réponse de manière à faire croire à l’utilisateur final à croire que ces contenus font partie de la réponse ;  
  
•   afficher des publicités qui ne sont pas fournies par Microsoft sur n’importe quelle page affichant une partie d’une réponse ;  
  
•   utiliser les données provenant des API Recherche dans le cadre de Machine Learning ou d’une activité algorithmique similaire pour former, évaluer ou améliorer des services nouveaux ou existants que vous ou des parties tierces pourriez proposer.

**Publicité.** La publicité (qu’elle soit fournie par Microsoft ou par un autre fournisseur) ne doit pas être affichée avec les réponses (i) des API de recherche d’images, d’actualités ou de vidéos ; ou (ii) avec des réponses filtrées ou principalement (ou exclusivement) limitées aux résultats de type image, actualités et/ou vidéos à partir d’autres API de recherche.

**Personnalisation.** Vous pouvez attribuer chaque réponse (ou partie d’une réponse) affichée à Microsoft, comme décrit à l’adresse https://go.microsoft.com/fwlink/?linkid=833278, sauf indication contraire de Microsoft pour votre usage particulier.

## <a name="5-transferring-responses"></a>5. Transfert des réponses.

Si vous permettez à un utilisateur de transférer une réponse d’un API de recherche à un autre utilisateur, par exemple via une application de messagerie ou à une publication sur les réseaux sociaux, les éléments suivants s’appliquent :

•   Les réponses transférées doivent :  
  
  - Comprendre du contenu non modifié à partir du contenu des réponses affichées à l’utilisateur effectuant le transfert (les modifications de mise en forme sont autorisées) ;  
    
  - Exclure les données sous forme de métadonnées ;  
    
  - Afficher les mentions indiquant que la réponse a été obtenue via une expérience de recherche Internet optimisée par Bing (par exemple, « Optimisé par Bing », « En savoir plus sur cette image sur Bing » ou « Découvrez cette image sur Bing » ou par le biais de l’utilisation du logo Bing) ;  
    
  - Afficher la requête complète utilisée pour générer la réponse, et  
    
  - Inclure un lien visible ou une attribution similaire à la source sous-jacente de la réponse, directement ou par l’intermédiaire de bing.com ou de m.bing.com.  
    
•   Vous n’êtes pas autorisé à automatiser le transfert des réponses. Un transfert doit être déclenché par une action utilisateur attestant clairement une intention de transférer une réponse.  
  
•   Vous pouvez uniquement activer pour un utilisateur la possibilité de transférer les réponses obtenues comme résultat du transfert de la requête de l’utilisateur.

## <a name="6-continuity-of-service"></a>6. Continuité de service.

Vous ne devez pas copier, stocker ou mettre en cache les données des réponses d’API de recherche. Toutefois, pour permettre la continuité de l’accès au service et le rendu de données, vous pouvez conserver les résultats uniquement dans les conditions suivantes :

**Appareil.** Vous pouvez activer pour un utilisateur final la possibilité de conserver les résultats sur un appareil pour la durée la plus courte entre (i) 24 heures à compter de l’heure de la requête ou (ii) jusqu’à ce qu’un utilisateur final envoie une autre requête pour des résultats mis à jour, à condition que les résultats de rétention soient utilisés uniquement dans les cas suivants :

•   pour permettre à l’utilisateur final d’accéder aux résultats qui lui ont renvoyés précédemment à sur cet appareil (par exemple, en cas d’interruption de service) ; ou  
  
•   pour stocker les résultats renvoyés par votre requête proactive personnalisée en prévision des besoins de l’utilisateur final en fonction de ses signaux (par exemple, en cas d’interruption de service prévue).

**Serveur.** Vous pouvez toutefois conserver des résultats spécifiques à un seul utilisateur final en toute sécurité sur un serveur que vous contrôlez, et afficher les résultats conservés uniquement :

•   pour permettre à l’utilisateur final d’accéder à un rapport historique des résultats précédemment renvoyés à cet utilisateur dans votre solution, à condition que les résultats ne peuvent pas (i) être conservés pendant plus de 21 jours à compter de la requête initiale de l’utilisateur final et (ii) être affichés en réponse à une requête nouvelle ou répétée d’un utilisateur final ; ou  
  
•   pour stocker les résultats renvoyés par votre requête proactive personnalisée en prévision des besoins de l’utilisateur final en fonction des signaux de l’utilisateur final pour la durée la plus courte entre (i) 24 heures à compter de l’heure de la requête ou (ii) jusqu’à ce qu’un utilisateur final envoie une autre requête pour des résultats mis à jour.

Chaque fois qu’ils sont conservés, les résultats pour un utilisateur spécifique ne peuvent pas être confondus avec ceux d’un autre utilisateur. Par exemple, les résultats de chaque utilisateur doivent être conservés et remis séparément.

**Généralités.** Pour la présentation de tous les résultats conservés, vous devez :

•   inclure une mention claire et visible de l’heure d’envoi de la requête,  
  
•   présenter à l’utilisateur un bouton ou un moyen similaire de relancer la requête et d’obtenir des résultats mis à jour,  
  
•   conserver la personnalisation Bing dans la présentation des résultats, et  
  
•   supprimer (et actualiser avec une nouvelle requête si nécessaire) les résultats stockés dans les délais spécifiés.

## <a name="7-non-display-url-discovery"></a>7. Détection d’URL non affichées.

Vous pouvez uniquement utiliser les réponses de recherche dans une expérience de recherche hors Internet, dans le seul but de détecter les URL des sources d’informations réactives à une requête de votre utilisateur ou client. Vous pouvez copier ces URL dans un rapport ou une réponse similaire que vous fournissez (i) uniquement à l’utilisateur ou au client, en réponse à la requête spécifique et (ii) qui inclut un contenu supplémentaire précieux par rapport à la requête. La configuration requise dans les sections 2 à 6 de ces conditions d’utilisation et d’affichage ne s’appliquent pas à ce non-affichage, excepté : 

•   Vous ne devez pas mettre en cache, copier ou stocker des données ou du contenu issus ou dérivés de la réponse de recherche, autre que la copie d’URL limitée décrite ci-dessus ;  
  
•   Vous devez garantir que l’utilisation des données (y compris les URL) reçues à partir des API de recherche ne viole pas la législation applicable ou les droits des tiers, et  
  
•   Vous ne devez pas utiliser les données (y compris les URL) provenant des API Recherche dans le cadre d’un index de recherche, de Machine Learning ou d’une activité algorithmique similaire pour créer, former, évaluer ou améliorer des services que vous ou des parties tierces pourriez proposer.

