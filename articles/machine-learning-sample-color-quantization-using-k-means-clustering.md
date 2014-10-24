<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Machine Learning Sample: Color quantization using K-Means clustering | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Exemple Azure Machine Learning : quantification des couleurs à l'aide de l'algorithme des k-moyennes

*Vous pouvez retrouver l'exemple d'expérience associé à ce modèle dans la section **EXPÉRIENCE** de l'onglet **EXEMPLES**. Le nom de l'expérience est :*

    Sample Experiment - Color Based Image Compression using K-Means Clustering - Development

## Description du problème

La [quantification des couleurs][quantification des couleurs] est le processus de réduction du nombre de couleurs distinctes dans une image, ce qui la compresse. Normalement, l'objectif est de conserver autant que possible l'apparence des couleurs de l'image, tout en réduisant le nombre de couleurs, que ce soit pour respecter des limites de mémoire ou de compression.

## Données

Dans cet exemple, imaginons que nous ayons une image RVB 24 bits avec 256 x 256 x 256 couleurs possibles. Bien sûr, nous pouvons construire des histogrammes de couleurs standard en fonction de ces valeurs d'intensité. Cependant, une autre approche consiste à quantifier l'image et à *réduire* le nombre de couleurs à 16 ou 64. Cela crée un espace plus petit et, dans l'idéal, moins de bruit et de variations. Pour cela, nous avons passé les données des pixels (chaque pixel en tant que ligne de jeu de données) dans notre module d'algorithme des k-moyennes.

## Modèle

Le modèle est créé comme indiqué dans l'image ci-dessous :

![Modèle][Modèle]

Nous avons exécuté l'algorithme des k-moyennes, où K=10 à 500 dans 5 branches différentes. D'abord, nous avons calculé les clusters, puis nous avons agrégé le clustering à la moyenne des couleurs de leurs pixels (à l'aide d'un script R).
Enfin, nous avons associé chaque pixel à la couleur de cluster agrégé, puis envoyé la nouvelle image au format CSV. Pendant ce temps, nous avons également calculé la différence moyenne quadratique des nouvelles couleurs de pixel avec l'image d'origine, et nous les avons affichées sous forme de graphique R (à l'aide du module Exécuter un script R).

## Résultats

Nous avons testé le résultat sur plusieurs clusters (couleurs) différents, comme illustré dans le modèle d'expérience ci-dessous. On peut voir que plus il y a de clustering, plus les images sont d'une qualité élevée, avec une compression réduite :

<table>
<tr><th>Image d'origine</th>
<td><img alt="Image d'origine" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg"></td>
</tr>
<tr><th>K=10</th>
<td><img alt="K=10" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png"></td>
</tr>
<tr><th>K=20</th>
<td><img alt="K=20" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png"></td>
</tr>
<tr><th>K=50</th>
<td><img alt="K=50" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png"></td>
</tr>
<tr><th>K=100</th>
<td><img alt="K=100" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png"></td>
</tr>
<tr><th>K=500</th>
<td><img alt="K=500" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png"></td>
</tr>
</table>
Nous avons également mesuré l'exactitude à l'aide de la différence moyenne quadratique sur les couleurs de l'image d'origine. Cela est visible sur le deuxième port de sortie du module « Exécuter un script R » :

![Output of Execute R Script module][Output of Execute R Script module]

Comme on peut le voir, plus il y a de clusters de couleurs, plus les couleurs correspondent aux images d'origine (meilleure qualité).

## Code pour convertir les images en CSV et vice-versa

Afin de passer les images dans ML Studio, nous avons écrit un code de conversion simple qui permet de convertir les fichiers image au format CSV, utilisable par ML Studio. C'est également un format qui permet de reconvertir en image. N'hésitez pas à utiliser le code suivant. Nous comptons prochainement ajouter un module permettant également de lire dans les images.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Drawing;
    using System.Drawing.Imaging;
    using System.IO;
     
    namespace Text2Image
    {
        class Program
        {
            static void img2csv(string img_path)
            {
                FileInfo img_info = new FileInfo(img_path);
                string destination_file_directory = img_info.DirectoryName + "\\";
                string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
                string destination_file_path = destination_file_directory + destination_file_name + ".csv";
     
                // Read the image
                Bitmap img = new Bitmap(img_path);
     
                // Create the CSV File and write the header values
                System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
                file.WriteLine("X,Y,R,G,B");
     
                // Write the Pixel values
                for (int x = 0; x < img.Width; x++)
                    for (int y = 0; y < img.Height; y++)
                    {
                        string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
                        file.WriteLine(line);
                    }
     
                file.Close();
            }
     
            static void csv2img(string csv_path)
            {
                FileInfo csv_info = new FileInfo(csv_path);
                string destination_file_directory = csv_info.DirectoryName + "\\";
                string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
                string destination_file_path = destination_file_directory + destination_file_name + ".png";
                
                // Read all the lines in the CSV file
                string[] lines = System.IO.File.ReadAllLines(csv_path);
     
                // set a new bitmap image with the provided width and height in the header
                string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
                int img_width = Convert.ToInt32(wh[0])+1;
                int img_height = Convert.ToInt32(wh[1])+1;
     
                Bitmap bmp_img = new Bitmap(img_width, img_height);
     
                for (int i = 1; i < lines.Length ;i++ )
                {
                    string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
                    if (values.Length < 3)
                        continue;
     
                    int x = Convert.ToInt16(values[0]);
                    int y = Convert.ToInt32(values[1]);
                    int r = Convert.ToInt32(values[2]);
                    int g = Convert.ToInt32(values[3]);
                    int b = Convert.ToInt32(values[4]);
     
                    bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
                }
     
                bmp_img.Save(destination_file_path);
            }
     
            static void Main(string[] args)
            {
                string source_path = args[1];
                FileInfo source_info = new FileInfo(source_path);
     
                if (source_info.Extension == ".csv")
                    csv2img(source_path);
                else
                    img2csv(source_path);
            }
        }
    }

  [quantification des couleurs]: http://en.wikipedia.org/wiki/Color_quantization "quantification des couleurs"
  [Modèle]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
  [Image d'origine]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
  [K=10]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
  [K=20]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
  [K=50]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
  [K=100]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
  [K=500]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
  [Output of Execute R Script module]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png
