# Guide du contributeur à la documentation technique Azure

Vous avez trouvé le dépôt GitHub qui héberge la source de la documentation technique publiée dans le Centre de documentation Azure à l’adresse [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Ce dépôt contient également des conseils pour vous aider à contribuer à notre documentation technique. Pour obtenir la liste des articles contenus dans le guide du contributeur, consultez [l’index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## Contribuer à la documentation Azure

Merci de l’intérêt que vous portez à la documentation Azure.

* [Comment contribuer ?](#ways-to-contribute)
* [À propos de vos contributions au contenu Azure](#about-your-contributions-to-azure-content)
* [Organisation du dépôt](#repository-organization)
* [Utiliser GitHub, Git et ce dépôt](#use-github-git-and-this-repository)
* [Comment utiliser Markdown pour mettre en forme votre rubrique](#how-to-use-markdown-to-format-your-topic)
* [Autres ressources](#more-resources)
* [Index de tous les articles du guide du contributeur](./contributor-guide/contributor-guide-index.md) (ouvre une nouvelle page)

## Comment contribuer ?

Vous pouvez facilement contribuer aux articles techniques dans l’interface utilisateur de GitHub. Recherchez l’article dans ce dépôt ou consultez l’article à l’adresse [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) et cliquez sur le lien dans l’article qui mène à la source GitHub de l’article.

##À propos de vos contributions au contenu Azure

###Corrections mineures

Les corrections mineures ou les clarifications que vous soumettez pour la documentation, ainsi que les exemples de code dans ce dépôt, sont couverts par les [Conditions d’Utilisation du Site Web Microsoft Azure](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###Corrections majeures

Si vous envoyez une requête de tirage avec des ajouts ou d’importantes modifications de la documentation et des exemples de code, nous vous enverrons un commentaire dans GitHub pour vous inviter à soumettre un contrat de licence de contribution en ligne si vous appartenez à l’un de ces groupes :

* Membres du groupe Microsoft Open Technologies
* Contributeurs qui ne travaillent pour Microsoft

Vous devrez remplir le formulaire en ligne pour que nous puissions accepter votre requête de tirage.

Tous les détails sont disponibles à l’adresse [http://azure.github.io/guidelines.html#cla](http://azure.github.io/guidelines.html#cla).

## Organisation du dépôt

Le contenu du dépôt azure-content suit l’organisation de la documentation sur [Azure.Microsoft.com](http://azure.microsoft.com). Ce dépôt contient deux dossiers racines :

### \articles

Le dossier *\articles* contient les articles de la documentation mis en forme en tant que fichiers Markdown avec une extension *.md*.


* **Noms de fichier des articles :** consultez [nos instructions sur l’affectation des noms de fichier](./contributor-guide/file-names-and-locations.md).

Les articles dans leur propre dossier de service sont publiés dans Azure.Microsoft.com. Chemin :
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/*

* **Sous-dossiers de fichiers multimédias :** le dossier *\articles* contient le dossier *\media* pour les fichiers multimédias des articles du répertoire racine, à l’intérieur duquel figurent des sous-dossiers contenant les images de chaque article. Les dossiers de service contiennent un dossier de fichiers multimédias distinct pour les articles dans chaque dossier de service. Le dossier d’images d’un article porte le même nom que le fichier de l’article, moins l’extension de fichier *.md*.

### \includes

Vous pouvez créer des sections de contenu réutilisables à inclure dans un ou plusieurs articles. Consultez [Extensions personnalisées utilisées dans notre contenu technique](./contributor-guide/custom-markdown-extensions.md).

### \markdown templates

Ce dossier contient notre modèle Markdown standard avec la mise en forme Markdown de base dont vous avez besoin pour un article.

### \contributor-guide

Ce dossier contient des articles qui font partie de notre guide du contributeur.

## Utiliser GitHub, Git et ce dépôt

Pour plus d’informations sur la façon de contribuer, d’utiliser l’interface utilisateur de GitHub pour apporter des modifications mineures, et de répliquer et cloner le dépôt pour effectuer des contributions majeures, consultez [Installer et configurer les outils de création dans GitHub](./contributor-guide/tools-and-setup.md).

Si vous installez GitBash et que vous choisissez de travailler localement, les étapes permettant de créer une branche de travail locale, d’apporter des modifications et de les renvoyer vers la branche principale sont répertoriées dans [Commandes Git pour créer un article ou mettre à jour un article existant](./contributor-guide/git-commands-for-master.md).

### Branches

Nous vous recommandons de créer des branches de travail locales qui ciblent une étendue de modification spécifique. Chaque branche doit être limitée à un seul article/concept, à la fois pour rationaliser le flux de travail et pour réduire les risques de conflits de fusion. Les opérations appropriées pour une nouvelle branche sont les suivantes :

* Nouvel article (et images associées)
* Corrections de la grammaire et de l’orthographe dans un article
* Application d’une seule modification de mise en forme dans un grand nombre d’articles (par exemple nouveau pied de page de copyright)

## Comment utiliser Markdown pour mettre en forme votre rubrique

Tous les articles dans ce dépôt font appel à Markdown dans GitHub. Voici une liste de ressources.

- [Principes fondamentaux de Markdown](https://help.github.com/articles/markdown-basics/)

- [Fiche récapitulative imprimable sur Markdown](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Pour obtenir notre liste d’éditeurs Markdown, consultez la rubrique sur [les outils et l’installation](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Métadonnées d’articles

Les métadonnées d’articles activent certaines fonctionnalités sur le site web azure.microsoft.com, telles que l’attribution de l’auteur, l’attribution du contributeur, les vues miniatures, les descriptions des articles et les optimisations du référencement d’un site auprès d’un moteur de recherche, ainsi que la création de rapports Microsoft pour évaluer les performances du contenu. Les métadonnées sont donc très importantes. [Voici quelques instructions qui vous aideront à optimiser vos métadonnées](./contributor-guide/article-metadata.md).

## Autres ressources

Pour découvrir toutes nos rubriques d’aide, consultez l’[index du guide du contributeur](./contributor-guide/contributor-guide-index.md).

<!----HONumber=AcomDC_0307_2016-->