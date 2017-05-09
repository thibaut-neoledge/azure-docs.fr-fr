## <a name="incremental-and-complete-deployments"></a>Déploiements incrémentiels et complets
Lorsque vous déployez vos ressources, vous spécifiez que le déploiement est soit une mise à jour incrémentielle, soit une mise à jour complète. La principale différence entre ces deux modes réside dans la manière dont Resource Manager gère les ressources existantes dans le groupe de ressources qui ne se trouvent pas dans le modèle :

* En mode complet, Resource Manager **supprime** les ressources qui existent dans le groupe de ressources, mais qui ne sont pas spécifiées dans le modèle. 
* En mode incrémentiel, Resource Manager **conserve telles quelles** les ressources qui existent dans le groupe de ressources, mais qui ne sont pas spécifiées dans le modèle.

Pour les deux modes, Resource Manager tente de configurer toutes les ressources spécifiées dans le modèle. Si la ressource existe déjà dans le groupe de ressources et si ses paramètres sont conservés, l’opération n’entraîne aucune modification. Si vous modifiez les paramètres d’une ressource, la ressource est configurée avec ces nouveaux paramètres. Si vous tentez de mettre à jour l’emplacement ou le type d’une ressource existante, le déploiement échoue avec une erreur. Vous devez dans ce cas déployer une nouvelle ressource avec l’emplacement ou le type dont vous avez besoin.

Par défaut, Resource Manager utilise le mode incrémentiel.

Pour illustrer la différence entre les modes incrémentiel et complet, examinez le scénario suivant.

Un **groupe de ressources existant** contient :

* Ressource A
* Ressource B
* Ressource C

Un **modèle** définit :

* Ressource A
* Ressource B
* Ressource D

Lors du déploiement en mode **incrémentiel**, le groupe de ressources contient :

* Ressource A
* Ressource B
* Ressource C
* Ressource D

Lors du déploiement en mode **complet**, la ressource C est supprimée. Le groupe de ressources contient :

* Ressource A
* Ressource B
* Ressource D
