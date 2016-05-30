


## Balisage d’une machine virtuelle via des modèles

Voyons d’abord le balisage via des modèles. [Ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) place des balises sur les ressources suivantes : Calcul (Machine virtuelle), Stockage (Compte de stockage) et Réseau (Adresse IP publique, Réseau virtuel et Interface réseau). (Cet exemple de modèle est destiné à une machine virtuelle Windows, mais il peut être ADapté pour les machines virtuelles Linux.)

Cliquez sur le bouton **Déployer sur Azure** à partir du [lien du modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Ceci permet d'accéder au [portail Azure](https://portal.azure.com/), où vous pouvez déployer ce modèle.

![Déploiement simple avec des balises](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Ce modèle inclut les balises suivantes :*Service**Application* et *Créé par*. Vous pouvez ajouter/modifier ces balises directement dans le modèle si vous voulez d’autres noms de balises.

![Balises Azure dans un modèle](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Comme vous pouvez le voir, les balises sont définies en tant que paires clé/valeur, séparées par un signe deux-points (:). Les balises doivent être définies dans ce format :

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Enregistrez le fichier de modèle après l’avoir modifié avec les balises de votre choix.

Ensuite, dans la section **Modifier les paramètres**, vous pouvez entrer les valeurs de vos balises.

![Modifier des balises dans le portail Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Cliquez sur **Créer** pour déployer ce modèle avec vos valeurs pour les balises.


## Balisage via le portail

Après avoir créé vos ressources avec des balises, vous pouvez afficher, ajouter et supprimer des balises dans le portail.

Sélectionnez l’icône Balises pour afficher vos balises :

![Icône Balises dans le portail Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Ajoutez une nouvelle balise via le portail en définissant votre propre paire clé/valeur, puis enregistrez-la.

![Ajouter une nouvelle balise dans le portail Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Votre nouvelle balise doit maintenant apparaître dans la liste des balises pour votre ressource.

![Nouvelle balise enregistrée dans le portail Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)
