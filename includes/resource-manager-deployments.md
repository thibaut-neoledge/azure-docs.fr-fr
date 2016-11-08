## Déploiements incrémentiels et complets
Par défaut, Resource Manager gère les déploiements sous la forme de mises à jour incrémentielles du groupe de ressources. Dans le cadre d’un déploiement incrémentiel, Resource Manager :

* **conserve telles quelles** les ressources qui existent dans le groupe de ressources, mais qui ne sont pas spécifiées dans le modèle ;
* **ajoute** les ressources qui sont spécifiées dans le modèle, mais qui n’existent pas dans le groupe de ressources ;
* **ne réapprovisionne pas** les ressources qui existent dans le groupe de ressources à l’état défini dans le modèle.
* **réapprovisionne** les ressources existantes pour lesquelles il y a des paramètres mis à jour dans le modèle.

Dans le cadre d’un déploiement complet, Resource Manager :

* **supprime** les ressources qui existent dans le groupe de ressources, mais qui ne sont pas spécifiées dans le modèle ;
* **ajoute** les ressources qui sont spécifiées dans le modèle, mais qui n’existent pas dans le groupe de ressources ;
* **ne réapprovisionne pas** les ressources qui existent dans le groupe de ressources à l’état défini dans le modèle.
* **réapprovisionne** les ressources existantes pour lesquelles il y a des paramètres mis à jour dans le modèle.

Vous spécifiez le type de déploiement par le biais de la propriété **Mode**.

<!---HONumber=AcomDC_0713_2016-->