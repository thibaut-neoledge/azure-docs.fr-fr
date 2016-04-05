<properties
   pageTitle="Gestion des ressources de données | Microsoft Azure"
   description="Article de procédure expliquant comment contrôler la visibilité et la propriété des ressources de données inscrites dans Azure Data Catalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/30/2016"
   ms.author="maroche"/>


# Gestion des ressources de données

## Introduction

**Azure Data Catalog** intègre des fonctionnalités de détection de sources de données qui permettent aux utilisateurs de découvrir et comprendre facilement les sources de données dont ils ont besoin pour effectuer des analyses et prendre des décisions. Ces fonctionnalités de découverte se révèlent tout particulièrement utiles lorsque tous les utilisateurs peuvent effectuer une recherche sur les nombreuses sources de données disponibles et les comprendre. Par conséquent, le comportement par défaut de Data Catalog consiste à rendre visibles et détectables l’ensemble des sources de données inscrites pour tous les utilisateurs du catalogue.

Data Catalog ne permet pas aux utilisateurs d’accéder aux données proprement dites. L’accès aux données est contrôlé par le propriétaire de la source de données. Data Catalog permet aux utilisateurs de découvrir les sources de données et d’afficher les métadonnées relatives aux sources inscrites dans le catalogue.

Il peut arriver, cependant, que certaines sources de données ne doivent être visibles qu’à des utilisateurs spécifiques ou à des membres de groupes spécifiques. Pour ces scénarios, Data Catalog offre aux utilisateurs la possibilité de s’approprier les ressources de données inscrites dans le catalogue et de contrôler ensuite la visibilité des ressources qu’ils détiennent.

> [AZURE.NOTE] La fonctionnalité décrite dans cet article est uniquement disponible dans l’édition Standard d’Azure Data Catalog. L’édition gratuite ne fournit aucune fonctionnalité de propriété ou de restriction de la visibilité des ressources de données.

## Gestion de propriété des ressources de données
Par défaut, les ressources de données inscrites dans Data Catalog ne sont pas associées à un propriétaire ; tout utilisateur autorisé à accéder au catalogue peut découvrir et annoter ces ressources. Les utilisateurs peuvent prendre possession de ressources de données sans propriétaire et limiter ensuite la visibilité des ressources qu’ils possèdent.

Lorsqu’une ressource de données de Data Catalog est affectée à un propriétaire, seuls les utilisateurs autorisés par le propriétaire peuvent découvrir la ressource et afficher ses métadonnées, et seul le propriétaire est habilité à supprimer la ressource de Data Catalog.

> [AZURE.NOTE] La propriété dans Data Catalog affecte uniquement les métadonnées stockées dans le catalogue. Elle ne confère aucune autorisation sur la source de données sous-jacente.

### Prise de possession
Les utilisateurs peuvent prendre possession de ressources de données en sélectionnant l’option « Prendre possession » dans le portail Data Catalog. Aucune autorisation particulière n’est requise pour prendre possession d’une ressource de données sans propriétaire ; tous les utilisateurs peuvent prendre possession d’une ressource de données qui n’est pas affectée à un propriétaire.

### Ajout de propriétaires et de copropriétaires
Si une ressource de données est déjà affectée à un propriétaire, les utilisateurs ne peuvent tout simplement pas en prendre possession : le propriétaire actuel doit les ajouter en tant que copropriétaires. N’importe quel propriétaire peut ajouter des utilisateurs ou groupes de sécurité supplémentaires en tant que copropriétaires.

> [AZURE.NOTE] Il est recommandé de désigner au moins deux propriétaires pour n’importe quelle ressource de données détenue par un propriétaire.

### Suppression de propriétaires
De la même manière que n’importe quel propriétaire de ressources peut ajouter des copropriétaires, n’importe quel propriétaire de ressource a la possibilité de supprimer des copropriétaires.

Si un propriétaire d’une ressource se supprime lui-même en tant que propriétaire, il n’aura plus aucun contrôle sur la ressource. Si un propriétaire d’une ressource se supprime lui-même en tant que propriétaire alors qu’il n’a désigné aucun copropriétaire, la ressource de données ne sera alors plus associée à un propriétaire.

## Visibilité
Les propriétaires de ressources de données peuvent contrôler la visibilité des ressources qu’ils détiennent. Pour définir une visibilité plus restreinte que celle du paramètre par défaut (où tous les utilisateurs de Data Catalog ont la possibilité de découvrir et afficher la ressource de données), le propriétaire de la ressource peut modifier le paramètre de visibilité dans les propriétés de la ressource en remplaçant l’option « Tout le monde » par l’option « Propriétaires et ces utilisateurs ». Les propriétaires peuvent ensuite ajouter des utilisateurs et groupes de sécurité spécifiques.

> [AZURE.NOTE] Dans la mesure du possible, les autorisations de possession et de visibilité des ressources doivent être attribuées à des groupes de sécurité, et non à des utilisateurs individuels.

## Administrateurs de Data Catalog
Les administrateurs de Data Catalog sont implicitement copropriétaires de toutes les ressources de données du catalogue. Les propriétaires des ressources ne peuvent pas supprimer la visibilité des ressources pour les administrateurs de Data Catalog ; de même, les administrateurs peuvent gérer la possession et la visibilité de toutes les ressources de données contenues dans le catalogue.

## Résumé
Data Catalog repose sur un modèle de découverte de données et de métadonnées basé sur la notion de crowdsourcing, qui permet à tous les utilisateurs du catalogue de collaborer et découvrir des ressources. L’édition Standard de Data Catalog intègre des fonctionnalités de possession et de gestion permettant de restreindre la visibilité et l’utilisation de ressources de données spécifiques.

<!---HONumber=AcomDC_0330_2016-->