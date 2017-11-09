
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Azure Data Factory est un service d’intégration de données. Il vous permet de créer des flux de travail pilotés par des données dans le cloud. Un flux de travail est implémenté comme un ou plusieurs *pipelines*. Les pipelines orchestrent et automatisent le déplacement des données et leur transformation. Les pipelines peuvent suivre la séquence suivante avec vos données :

1. Ingérez des données à partir de différents magasins de données.
2. Transformez ou traitez les données à l’aide de services de calcul tels que les suivants :
    - Azure HDInsight Hadoop
    - Spark
    - Service Analytique Azure Data Lake
    - Azure Machine Learning
3. Publiez des données de sortie dans des magasins de données.
    - La cible de la publication peut être un Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser. 

Vous pouvez planifier des pipelines à l’aide de Data Factory.

