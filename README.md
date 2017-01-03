# <a name="azure-technical-documentation-contributor-guide"></a>Guide du contributeur à la documentation technique Azure
Vous avez trouvé le dépôt GitHub qui héberge la source de la documentation technique publiée dans le Centre de documentation Azure, à l’adresse [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure).

Ce dépôt contient également des conseils pour vous aider à contribuer à notre documentation technique. Pour obtenir la liste des articles dans le guide du contributeur, consultez l’[index](contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Contribuer à la documentation Azure
Merci de l’intérêt que vous portez à la documentation Azure.

* [Comment contribuer ?](#ways-to-contribute)
* [Code de conduite](#code-of-conduct)
* [À propos de vos contributions au contenu Azure](#about-your-contributions-to-azure-content)
* [Organisation du dépôt](#repository-organization)
* [Utiliser GitHub, Git et ce dépôt](#use-github-git-and-this-repository)
* [Comment utiliser Markdown pour mettre en forme votre rubrique](#how-to-use-markdown-to-format-your-topic)
* [Retour d’information, commentaires et support](./contributor-guide/feedback-and-comments.md)
* [Autres ressources](#more-resources)
* [Index de tous les articles du guide du contributeur](contributor-guide/contributor-guide-index.md) (ouvre une nouvelle page)

## <a name="ways-to-contribute"></a>Comment contribuer ?
Pour envoyer des mises à jour de la [documentation Azure](https://docs.microsoft.com/azure), procédez comme suit :

* Vous pouvez facilement contribuer aux articles techniques dans l’interface utilisateur de GitHub. Recherchez l’article dans ce dépôt, ou consultez l’article à l’adresse [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) et cliquez sur le lien dans l’article qui mène à la source GitHub de l’article.
* Si vous apportez des modifications substantielles à un article existant, si vous ajoutez ou modifiez des images ou si vous ajoutez un nouvel article, vous devez forker ce dépôt, installer GitBash et MarkdownPad, puis apprendre à utiliser certaines commandes git.

## <a name="code-of-conduct"></a>Code de conduite
Ce projet a adopté le [Code de conduite open source de Microsoft](https://opensource.microsoft.com/codeofconduct/). Pour plus d’informations, consultez la [FAQ sur le code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou envoyez vos questions ou vos commentaires à [opencode@microsoft.com](mailto:opencode@microsoft.com).

## <a name="about-your-contributions-to-azure-content"></a>À propos de vos contributions au contenu Azure
### <a name="minor-corrections"></a>Corrections mineures
Les corrections mineures ou les clarifications que vous soumettez pour la documentation, ainsi que les exemples de code dans ce dépôt, sont couvertes par les [Conditions d’utilisation du site web docs.microsoft.com](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Corrections majeures
Si vous envoyez une demande de tirage avec des ajouts ou d’importantes modifications de la documentation et des exemples de code, nous vous enverrons un commentaire dans GitHub pour vous inviter à signer un contrat de licence de contribution en ligne si vous n’êtes pas un employé de Microsoft. Vous devrez remplir le formulaire en ligne pour que nous puissions accepter votre demande de tirage (pull request).

## <a name="repository-organization"></a>Organisation du dépôt
Le contenu du dépôt azure-docs suit l’organisation de la documentation sur https://docs.microsoft.com/azure. Ce dépôt contient deux dossiers racines :

### <a name="articles"></a>\articles
Le dossier *\articles* contient les articles de la documentation au format de fichiers Markdown avec une extension *.md*. En général, les articles sont regroupés par service Azure.

Les articles doivent respecter les instructions d’affectation de noms de fichier. Pour plus d’informations, consultez [nos instructions d’affectation de noms de fichier](contributor-guide/file-names-and-locations.md).

Le dossier *\articles* contient le dossier *\media* des fichiers multimédias des articles du répertoire racine, lequel contient les sous-dossiers comprenant les images de chaque article.  Les dossiers de service contiennent un dossier de fichiers multimédias distinct pour les articles dans chaque dossier de service. Le dossier d’images d’un article porte le même nom que le fichier de l’article, moins l’extension de fichier *.md* .

### <a name="includes"></a>\includes
Vous pouvez créer des sections de contenu réutilisables à inclure dans un ou plusieurs articles. Consultez [Extensions personnalisées utilisées dans notre contenu technique](contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>\markdown templates
Ce dossier contient notre modèle Markdown standard avec la mise en forme Markdown de base dont vous avez besoin pour un article.

### <a name="contributor-guide"></a>\contributor-guide
Ce dossier contient des articles qui font partie de notre guide du contributeur.

## <a name="use-github-git-and-this-repository"></a>Utiliser GitHub, Git et ce dépôt
Pour plus d’informations sur la façon de contribuer, d’utiliser l’interface de GitHub pour apporter des modifications mineures, et de forker et cloner le dépôt pour effectuer des contributions majeures, consultez [Installer et configurer les outils de création dans GitHub](contributor-guide/tools-and-setup.md).

Si vous installez GitBash et que vous choisissez de travailler localement, les étapes permettant de créer une branche de travail locale, d’apporter des modifications et de les renvoyer vers la branche principale sont répertoriées dans [Commandes Git pour créer un article ou mettre à jour un article existant](contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Branches
Nous vous recommandons de créer des branches de travail locales qui ciblent une étendue de modification spécifique. Chaque branche doit être limitée à un seul article/concept, à la fois pour rationaliser le flux de travail et pour réduire les risques de conflits de fusion.  Les opérations appropriées pour une nouvelle branche sont les suivantes :

* Nouvel article (et images associées)
* Corrections de la grammaire et de l’orthographe dans un article
* Application d’une seule modification de mise en forme dans un grand nombre d’articles (par exemple nouveau pied de page de copyright)

## <a name="how-to-use-markdown-to-format-your-topic"></a>Comment utiliser Markdown pour mettre en forme votre rubrique
Tous les articles dans ce dépôt font appel à Markdown dans GitHub.  Voici une liste de ressources.

* [Les bases de Markdown](https://help.github.com/articles/markdown-basics/)
* [Fiche récapitulative imprimable sur Markdown](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

## <a name="article-metadata"></a>Métadonnées d’articles
Les métadonnées d’articles permettent d’utiliser certaines fonctionnalités, telles que l’attribution de l’auteur, l’attribution du contributeur, les vues miniatures, les descriptions d’article, les optimisations du référencement d’un site auprès d’un moteur de recherche, ainsi que la création de rapports sur les processus utilisés par Microsoft pour évaluer les performances du contenu. Les métadonnées sont donc très importantes. [Voici quelques instructions qui vous aideront à optimiser vos métadonnées](contributor-guide/article-metadata.md).

### <a name="labels"></a>Étiquettes
Les étiquettes automatisées sont affectées à des demandes de tirage pour nous aider à gérer ces opérations et à vous informer sur l’état de vos demandes :

* Contrat de licence de contribution associé
  * CLA-not-required : la modification est relativement mineure et ne nécessite pas la signature d’un CLA.
  * CLA-required : l’ampleur de la modification est relativement importante et requiert la signature d’un CLA.
  * CLA-signed : le contributeur a signé le CLA et la demande de tirage peut maintenant être examinée.
* Étiquettes de pilier : les étiquettes de type PnP, Applications modernes et TDC identifient les demandes de tirage en fonction de l’organisation interne qui doit examiner la demande.
* Modification envoyée à l’auteur : l’auteur a été informé de la demande de tirage en attente.

## <a name="more-resources"></a>Autres ressources
Pour découvrir toutes nos rubriques d’aide, consultez l’[index du guide du contributeur](contributor-guide/contributor-guide-index.md).



<!--HONumber=Dec16_HO3-->


