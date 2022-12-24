# TCC_BIMASTER
Trabalho de conclusão de Curso do BI-Master turma 2020-2 PUC-Rio

<!-- antes de enviar a versão final, solicitamos que todos os comentários, colocados para orientação ao aluno, sejam removidos do arquivo -->

# Reconhecimento de Aeronaves a partir de Imagens de Sensoriameno Remoto

#### Aluno: [David Fernando Castillo Zúñiga](https://github.com/davidfer88).
#### Orientador: [Leonardo Forero Mendoza](https://github.com/leofome8).
---

Trabalho apresentado ao curso [BI MASTER](https://ica.puc-rio.ai/bi-master) como pré-requisito para conclusão de curso e obtenção de crédito na disciplina "Projetos de Sistemas Inteligentes de Apoio à Decisão".

- [Link para o código](https://github.com/davidfer88/TCC_BIMASTER). 


- Trabalhos relacionados: <!-- caso não aplicável, remover estas linhas -->
    - [Nome do Trabalho 1](https://link_do_trabalho.com).
    - [Nome do Trabalho 2](https://link_do_trabalho.com).

---

### Resumo

<!-- trocar o texto abaixo pelo resumo do trabalho, em português -->

O reconhecimento de aeronaves a partir de imagens de sensoriamento remoto tem muitas aplicações na área civil e militar. A deteção dinâmica de aeronaves pode ser uma fonte importante para tomadas de decisão em estrategias militares ou de defesa. No campo civil é muito util a deteção de aeronaves para determinar o nivel de congetionamento de um aeroporto ou para verificar a disponibilidades de aeronaves em uma região em caso de emergencia.

Neste trabalho é feita uma análise exploratoria do conjunto de dados de detecçao de aeronaves da Airbus. Posterioremente é criado, treinado e validado um modelo de rede neural convolucional usando Yolov5. È realizado um estudo paramêtrico a fim de determinar a influência de alguns hiperparâmetros como épocas no desempenho da rede criada,


### Abstract <!-- Opcional! Caso não aplicável, remover esta seção -->

<!-- trocar o texto abaixo pelo resumo do trabalho, em inglês -->

Aircraft recognition from remote sensing images has many civil and military applications. The dynamic detection of aircraft can be an important source for decision making in military or defense strategies. In the civil field, aircraft detection is very useful to determine the level of congestion at an airport or to check the availability of aircraft in a region in case of emergency.

In this work, an exploratory analysis of the Airbus aircraft detection dataset is performed. Subsequently, a convolutional neural network model is created, trained and validated using Yolov5. A parametric study is carried out in order to determine the influence of some hyperparameters such as epochs on the performance of the created network,, .

### Introdução

Ao longo dos últimos anos, os detectores de objetos baseados em Rede Neural Convolucional (CNN) ganharam popularidade na comunidade de pesquisa devido à sua capacidade de calcular automaticamente recursos de imagens contextuais complexas  [8] . Os métodos atuais de vanguarda para detecção de objetos baseados em CNN podem ser amplamente categorizados como detectores de dois estágios baseados em região ou detectores de estágio único baseados em regressão. Exemplos de detectores baseados em região incluem CNN baseado em região (RCNN)  [9] , Fast RCNN  [10] e Faster RCNN  [11] , enquanto detectores como You Only Look Once (YOLO)  [12] e Single Shot MultiBox Detection ( SSD)  [13]são exemplos de detectores baseados em regressão. Os modelos baseados em regressão são geralmente menos precisos em comparação com os detectores baseados em região, no entanto, os detectores baseados em regressão são significativamente mais rápidos em comparação com os detectores baseados em região. Esforços têm sido feitos por pesquisadores para desenvolver novos modelos baseados em CNN para melhorar o desempenho e a eficiência  [14]. 

Ammart et. al. (2021) abordaram o problema de detecção de carros a partir de imagens aéreas usando Redes Neurais Convolucionais (CNNs).Eles avaliaram o desempenho de três algoritmos CNN de última geração, a saber, Faster R-CNN, bem como YOLOv3 e YOLOv4.

### Metodologia



#### Base de dados

Neste trabalho é utilizado como conjunto de dados de entrada o dataset de demostração de detecção de aeronaves Airbus. Este conjunto de dados é uma versão de demonstração de conjuntos de dados de aprendizado profundo maiores e mais avançados criados a partir de imagens de satélite da Airbus. [Airbus Defense and Space Intelligence](https://www.intelligence-airbusds.com/) opera a maior constelação comercial de satélites combinando imagens ópticas de Pléiades, SPOT, Vision-1 e DMC, bem como a constelação de radar (composta por TerraSAR -X, TanDEM-X e PAZ). [OneAtlas](https://oneatlas.airbus.com/) oferece acesso fácil e flexível a imagens de satélite premium da Airbus, análises geoespaciais inovadoras, insights específicos do setor e muito mais.

##### Imagens para treinamento

A pasta `images  contém 103 extratos de imagens das Plêiades com aproximadamente 50 cm de resolução. Cada imagem é armazenada como um arquivo JPEG de tamanho 2560 x 2560 pixels (ou seja, 1280 metros no solo). Os locais são vários aeroportos em todo o mundo. Alguns aeroportos aparecem várias vezes em diferentes datas de aquisição. Algumas imagens também incluem neblina ou nuvem para diversidade.

<img src="img/Aeroporto_mostra.png" style="width: 600px">

####Formato de de dados COCO e Pascal VOC para detecção de objetos.

Uma das tarefas mais importantes em visão computacional é rotular os dados. Existem várias ferramentas disponíveis onde você pode carregar as imagens, rotular os objetos usando segmentação por instância. Isso ajuda na localização precisa do objeto usando caixas delimitadoras ou mascaramento usando polígonos. Essas informações são armazenadas em arquivos de anotação. Arquivos/arquivos de anotação podem estar nos formatos de dados [COCO]http://host.robots.ox.ac.uk/pascal/VOC/voc2012/devkit_doc.pdf) (Everingham, 2012) ou [Pascal VOC](https://arxiv.org/pdf/1405.0312.pdf) (Lin,2014).

https://towardsdatascience.com/coco-data-format-for-object-detection-a4c5eaf518c5

##### Anotações

Todas as aeronaves foram anotadas com caixas delimitadoras nas imagens fornecidas. As anotações são fornecidas na forma de polígonos GeoJSON fechados. Um arquivo CSV chamado `annotations.csv` fornece todas as anotações - uma anotação por linha com o nome de arquivo correspondente da imagem como `image_id` e a classe da anotação, principalmente `Aircraft` ou `Truncated_Aircraft` para aeronaves localizadas na fronteira de a imagem.

##### Imagens extras

Uma pasta chamada `extras' contém 6 imagens extras que não são anotadas, mas podem ser usadas para testar um modelo em imagens novas - nunca vistas antes.


#### Modelo de aprendizado profundo usando YoloV5

YOLO um acrônimo para 'You only look once', é um algoritmo de detecção de objetos que divide imagens em um sistema de grade. Cada célula na grade é responsável por detectar objetos dentro de si. %%YOLO é um dos algoritmos de detecção de objetos mais famosos devido à sua velocidade e precisão. O código-fonte aberto está disponível no [GitHub](https://github.com/ultralytics/yolov5).

Na  arquitetura do YoloV5 destacam 3 componentes: a espinha dorsal (backbone), a cabeça (head) e a detecção (detection). A espinha dorsal é uma rede neura convulocional (CNN) que coleta e modela carateristicas de imagem em diferentes granularidades. O YoloV5 implementa o gargalo (Bottleneck ) de previsão de centro e escala (CSP) para formular recursos de imagem. A cabeça é uma série de camadas para combinar carateristicas (features) de imagem para lançá-los em um processo de previsão. O YoloV5 também implementa o PA-NET para agregação de carateristicas. A detecção é um processo que utiliza recursos do cabeça (head) e realiza etapas de previsão de caixa e classe (Ieamsaard, 2021). Um diagrama da arquitetura YoloV5 é mostrado na seguinte figura.

<img src="img/Overview of model structure about YOLOv5.jpg" style="width: 600px">

#### Treinamento do modelo

As imagens do conjunto de dados de entrada são muito grandes para um correto aprendizado pelo YOLO. É necessario s subdividisão das imagens em imegens menores ou ladrilhos (tiles). São gerados blocos antecipadamente  com um tamanho setado em 512 pixels por 512 pixels. paa garantir que todas a s aeronaves possam ser vistas pela rede na integra , é permitida uma sobreposição de 64 pixels entre os blocos.

Para a base de treino foram setados o:s seguintes parâmetros: epochs=10, batch_size=16, imgsz=512

train: weights=yolov5s.pt, cfg=, data=dataset.yaml, hyp=yolov5/data/hyps/hyp.scratch-low.yaml, epochs=10, batch_size=16, imgsz=512, rect=False, resume=False, nosave=False, noval=False, noautoanchor=False, noplots=False, evolve=None, bucket=, cache=None, image_weights=False, device=, multi_scale=False, single_cls=False, optimizer=SGD, sync_bn=False, workers=8, project=yolov5/runs/train, name=exp, exist_ok=False, quad=False, cos_lr=False, label_smoothing=0.0, patience=100, freeze=[0], save_period=-1, seed=0, local_rank=-1, entity=None, upload_dataset=False, bbox_interval=-1, artifact_alias=latest

### Resultados obtidos

As seguintes metricas foram usadas para avaliar o resultados 

loss_bbox: uma perda que mede o quão "apertadas" as caixas delimitadoras previstas são para o objeto de verdade básica (geralmente uma perda de regressão, L1, smoothL1etc.).

loss_cls: uma perda que mede a exatidão da classificação de cada caixa delimitadora prevista: cada caixa pode conter uma classe de objeto ou um "background". Essa perda é geralmente chamada de perda de entropia cruzada.

Precisão é uma medida de quando "" seu modelo prevê com que frequência ele prevê corretamente ?"" Indica o quanto podemos confiar nas previsões positivas do modelo.

Recall é uma medida de "" seu modelo previu todas as vezes que deveria ter previsto? "" Indica quaisquer previsões que não deveriam ter sido perdidas se o modelo estiver ausente. 

Precisão Média Média (mAP). O mAP é usado como uma métrica padrão para analizar a precisão de um modelo de deteção de objetos.  O calculo de mAP é baseada nas seguintes submétricas: matriz de confusão, Interseção sobre a União (IoU), recall e precisão. The mAP incorpora um compromisso entre precisão e recall e considera tanto falsos positivos, como falsos negativos. Os verdadeiros e falsos positivos da tarefa de detecção de objetos são classificados usando o limite IoU.

Para acompanhamento das metricas do trinamento e validação é utilizado o  Wandb. WandB é um dashboard central para acompanhar  hiperparâmetros, métricas do sistema e previsões permitindo comparar modelos ao vivo.


São apresentadas na seguinte figura as métricas de treinamento e validação para 10 épocas. Observa-se para o treinamento que a

Métricas de treinamento e validação para 10 épocas

<img src="img/media_images_Results_10_0326a98d691b6ae1c41b_Compilado.png" style="width: 600px">

Métricas de treinamento e validação para 20 épocas

<img src="img/media_images_Results_20_6afe5cd66e9ae3135d0e_Compilado.png" style="width: 600px">

Métricas de treinamento e validação para 50 épocas

São apresentadas na seguinte figura as métricas de treinamento e validação para 40 épocas. Observa-se para o treinamento que

<img src="img/media_images_Results_50_fa5a90ae58da257eb152_Compilado.png" style="width: 600px">

Na seguinte tabela são sumarizadas os melhores valores para as diferentes metricas apresentadas nas figuras anteriores para 10, 20 e 50 épocas.

|Epoch	|10	|20	|50	|min	|max|
|---|---|---|---|---|---|
|best/epoch	|9	|19	|36	|	||
|best/mAP_0.5	|0,92437	|0,92197	|0,9272	|0,92197	|0,9272|
|best/mAP_0.5:0.95	|0,6488	|0,67335	|0,68355	|0,6488	|0,68355|
|best/precision	|0,96137	|0,95388	|0,96394	|0,95388	|0,96394|
|best/recall	|0,8699	|0,87897	|0,87594	|0,8699	|0,87897|
|metrics/mAP_0.5	|0,92422	|0,92199	|0,92718	|0,92199	|0,92718|
|metrics/mAP_0.5:0.95	|0,64839	|0,67332	|0,68319	|0,64839	|0,68319|
|metrics/precision	|0,96136	|0,9536	|0,96584	|0,9536	|0,96584|
|metrics/recall	|0,86958	|0,87868	|0,87218	|0,86958	|0,87868|
|train/box_loss	|0,02579	|0,02291	|0,01966	|0,01966	|0,02579|
|train/cls_loss	|0	|0	|0	|0	|0|
|train/obj_loss	|0,00982	|0,00891	|0,00789	|0,00789	|0,00982|
|val/box_loss	|0,02359	|0,02229	|0,02196	|0,02196	|0,02359|
|val/cls_loss	|0	|0	|0	|0	|0|
|val/obj_loss	|0,00698	|0,00671	|0,00665	|0,00665	|0,00698|
|x/lr0	|0,00208	|0,00109	|0,0005	|0,0005	|0,00208|
|x/lr1	|0,00208	|0,00109	|0,0005	|0,0005	|0,00208|
|x/lr2	|0,00208	|0,00109	|0,0005	|0,0005	|0,00208|


São apresentadas a continuação figuras comas inferências para 10, 20 e 50 épocas respectivamente. Para proposito de comparação foi fixada a mesma imagem.

Observa-se para 10 épocas 

Inferencia para 2 épocas

<img src="img/predi2epoc.png" style="width: 600px">

Inferencia para 10 épocas

<img src="img/predi10epoc.png" style="width: 600px">

Inferencia para 20 épocas

<img src="img/predi20epoc.png" style="width: 600px">

Inferencia para 50 épocas

<img src="img/predi50epoc.png" style="width: 600px">


Trabalhos futuros

No presente trabalho foi abordado o problema de reconhecimento de uma classe, em futuros estudos pode ser abordado o problema de reconhecimento de diferentes tipos de aeronaves.  

### Conclusão 

Neste trabalho foi desenvolvido e estudado um modelo para deteção de aeronaves a partir de imagens remotas de sensoreamento. O modeo é treinado pelo YoloV5 em diferentes números épocas.


### Referências

Ammar, A., Koubaa, A., Ahmed, M., Saad, A., & Benjdira, B. (2021). Vehicle detection from aerial images using deep learning: A comparative study. Electronics, 10(7), 820.

Everingham, M., & Winn, J. (2012). The PASCAL visual object classes challenge 2012 (VOC2012) development kit. Pattern Anal. Stat. Model. Comput. Learn., Tech. Rep, 2007, 1-45.

Ieamsaard, J., Charoensook, S. N., & Yammen, S. (2021, March). Deep learning-based face mask detection using yolov5. In 2021 9th International Electrical Engineering Congress (iEECON) (pp. 428-431). IEEE.

Kasper-Eulaers, M., Hahn, N., Berger, S., Sebulonsen, T., Myrland, Ø., & Kummervold, P. E. (2021). Detecting heavy goods vehicles in rest areas in winter conditions using YOLOv5. Algorithms, 14(4), 114.

Lin, T. Y., Maire, M., Belongie, S., Hays, J., Perona, P., Ramanan, D., ... & Zitnick, C. L. (2014, September). Microsoft coco: Common objects in context. In European conference on computer vision (pp. 740-755). Springer, Cham.

Wu, Z. Z., Wan, S. H., Wang, X. F., Tan, M., Zou, L., Li, X. L., & Chen, Y. (2020). A benchmark data set for aircraft type recognition from remote sensing images. Applied Soft Computing, 89, 106132.

Zhao, A., Fu, K., Wang, S., Zuo, J., Zhang, Y., Hu, Y., & Wang, H. (2017). Aircraft recognition based on landmark detection in remote sensing images. IEEE Geoscience and Remote Sensing Letters, 14(8), 1413-1417.


---

Matrícula: 202.100.173

Pontifícia Universidade Católica do Rio de Janeiro

Curso de Pós Graduação *Business Intelligence Master*
