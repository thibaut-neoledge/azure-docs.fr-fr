## <a name="microsoft-open-source-code-of-conduct"></a>Code de conduite de Microsoft Open Source

Ce projet a adopté le [Code de conduite open source de Microsoft](https://opensource.microsoft.com/codeofconduct/).
Pour plus d’informations, consultez la [FAQ sur le code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou envoyez vos questions ou vos commentaires à [opencode@microsoft.com](mailto:opencode@microsoft.com).

## <a name="contribute-to-azure-technical-documentation"></a>Contribuer à la documentation technique Azure
Nous apprécions les contributions de notre communauté (utilisateurs, clients, partenaires, employés MSFT en dehors des unités Azure principales, etc.) et d’employés qui travaillent dans les unités Azure principales. La nature de votre contribution dépend de votre identité :

* **Communauté - mises à jour mineures** : si votre contribution concerne des mises à jour d’articles mineures, recherchez l’article dans ce dépôt, ou consultez-le sur [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) et cliquez sur le lien **Modifier**, qui vous mènera à la source GitHub de l’article. Utilisez ensuite simplement l’interface utilisateur de GitHub pour effectuer vos mises à jour. Vous pouvez également dupliquer le référentiel et proposer des mises à jour à partir de votre branche.

* **Communauté - nouveaux articles** : si vous faites partie de la communauté Azure et que vous souhaitez créer un article, vous devez collaborer avec un employé afin d’intégrer ce nouveau contenu via plusieurs opérations dans le référentiel public et privé.

* **Employés** : si vous êtes rédacteur technique, gestionnaire de programmes ou développeur dans l’équipe produit d’un service Azure et que votre travail consiste à contribuer aux articles techniques ou à en rédiger, utilisez le référentiel privé (https://github.com/MicrosoftDocs/azure-docs-pr). Si vous apportez des modifications substantielles à un article existant, si vous ajoutez ou modifiez des images ou si vous ajoutez un nouvel article, vous devez forker ce dépôt, installer GitBash, un éditeur Markdown, puis apprendre à utiliser certaines commandes git. Consultez le [guide du contributeur interne](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) pour plus d’informations.


## <a name="about-your-contributions-to-azure-content"></a>À propos de vos contributions au contenu Azure
### <a name="minor-corrections"></a>Corrections mineures
Les corrections mineures ou les clarifications que vous soumettez pour la documentation, ainsi que les exemples de code dans ce dépôt, sont couvertes par les [Conditions d’utilisation du site web docs.microsoft.com](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Corrections majeures
Si vous envoyez une demande de tirage avec des ajouts ou d’importantes modifications de la documentation et des exemples de code, nous vous enverrons un commentaire dans GitHub pour vous inviter à signer un contrat de licence de contribution en ligne si vous n’êtes pas un employé de Microsoft. Vous devrez remplir le formulaire en ligne pour que nous puissions accepter votre demande de tirage (pull request).

## <a name="tools-and-setup"></a>Outils et configuration
Les contributeurs de la communauté peuvent utiliser l’interface utilisateur de GitHub ou forker le dépôt pour intégrer leur contribution. Les employés devraient consulter le [guide du contributeur interne](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) pour savoir comment contribuer à la documentation technique.

## <a name="repository-organization"></a>Organisation du dépôt
Le contenu du dépôt azure-docs suit l’organisation de la documentation sur https://docs.microsoft.com/azure. Ce dépôt contient deux dossiers racines :

### <a name="articles"></a>\articles
Le dossier *\articles* contient les articles de la documentation au format de fichiers Markdown avec une extension *.md*. En général, les articles sont regroupés par service Azure.

Le dossier *\articles* contient le dossier *\media* des fichiers multimédias des articles du répertoire racine, lequel contient les sous-dossiers comprenant les images de chaque article.  Les dossiers de service contiennent un dossier de fichiers multimédias distinct pour les articles dans chaque dossier de service. Le dossier d’images d’un article porte le même nom que le fichier de l’article, moins l’extension de fichier *.md* .

### <a name="includes"></a>\includes
Vous pouvez créer des sections de contenu réutilisables à inclure dans un ou plusieurs articles. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Comment utiliser Markdown pour mettre en forme votre rubrique
Tous les articles dans ce dépôt font appel à Markdown dans GitHub.  Voici une liste de ressources.

* [Les bases de Markdown](https://help.github.com/articles/markdown-basics/)
* [Fiche récapitulative imprimable sur Markdown](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)


## <a name="labels"></a>Étiquettes
Dans le dépôt public azure-docs, les étiquettes automatisées sont affectées à des demandes de tirage pour nous aider à gérer ces opérations et à vous informer sur l’état de vos demandes :

* Contrat de licence de contribution associé
  * CLA-not-required : la modification est relativement mineure et ne nécessite pas la signature d’un CLA.
  * CLA-required : l’ampleur de la modification est relativement importante et requiert la signature d’un CLA.
  * CLA-signed : le contributeur a signé le CLA et la demande de tirage peut maintenant être examinée.
* Modification envoyée à l’auteur : l’auteur a été informé de la demande de tirage en attente.
* ready-to-merge : prête pour être révisée par notre équipe chargée de la révision des demandes de tirage.


