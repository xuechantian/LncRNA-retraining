
![Workflow of the model retraining](https://github.com/xuechantian/LncRNA-retraining/blob/master/model-retraining.png)





# **Plant lncRNA Identification with improved models**





### **This is a repository of code used for identifying long non-coding RNAs (lncRNAs) in plants. In order to improve the accuracy of plant lncRNA identification, we retrained and updated the models of several mainstream software .**

#### **Email:** xuechan.tian@bjfu.edu.cn








## **1. Data sources.** 



  The training data sets used for model construction came from the literature "Pattern recognition analysis on long noncoding RNAs: a tool for prediction in plants", [DOI Link](https://doi.org/10.1093/bib/bby034).




## **2. Model retraining of CPAT software and prediction of lncRNA.**



### **Prerequisite:**
CPAT (version 1.2.4)

    conda create -n py27 python=2.7 -y

    source activate py27

    pip2 install CPAT



### **Usage**


#### **Use "make_hexamer_tab.py" to create a hexamer table.**


    make_hexamer_tab.py -c ./data/training/mRNA.fasta -n ./data/training/lncRNA.fasta > Plant_Hexamer.tsv




#### **Use "make_logitModel.py" to build a logistic regression model.**


    make_logitModel.py  -x Plant_Hexamer.tsv -c ./data/training/mRNA.fasta -n ./data/training/lncRNA.fasta  -o Plant



#### **Identification of lncRNA with retrained plant model.**
 The coding probability (CP) cutoff: 0.46 (CP >=0.46 indicates coding sequence, CP < 0.46 indicates noncoding sequence).


    cpat.py -x Plant_Hexamer.tsv -d Plant.logit.RData -g ./test/test.fasta -o  Vitis_vinifera.output



#### **Identification of lncRNA with original model**


    cpat.py -x ./Model/Human_Hexamer.tsv -d ./Model/Human_logitModel.RData -g ./test/test.fasta -o CPAT_human.output





## **3. Model retraining of PLEK software and prediction of lncRNA.**


PLEK (version 1.2.0)


### **Prerequisite:**
    Download PLEK.1.2.tar.gz from https://sourceforge.net/projects/plek/files/ and decompress it.
   
    tar zvxf PLEK.1.2.tar.gz 
   
 
 
 
 
### **Usage**



#### **Build the plant model useing PLEKModelling.py**


    python PLEKModelling.py -mRNA ./data/training/mRNA.fasta -lncRNA ./data/training/lncRNA.fasta -prefix plant -thread 10



#### **Identification of lncRNA with plant model**


    python PLEK.py -fasta ./test/test.fasta -out plek_plant_result -range plant.range -model plant.model -thread 10



#### **Identification of lncRNA with original model**


    python PLEK.py -fasta ./test/test.fasta -out plek_original_result -thread 10





## **4. Model retraining of LncFinder software and prediction of lncRNA.**


### **Prerequisite:**

##### R Package

    install.packages("LncFinder")
  
    install.package("seqinr")
  
    library(e1071)
  
    library(LncFinder)
  
    library(seqinr)
  
  
  
##### import data
    mRNA <- seqinr::read.fasta(file ="./data/training/mRNA.fasta")
  
    lncRNA <- seqinr::read.fasta(file ="./data/training/lncRNA.fasta")




### **Usage**
#### **Use "make_frequencies" function to generate the feature file, which will be used for model training in the second step.**


    frequencies <- make_frequencies(cds.seq = mRNA, lncRNA.seq = lncRNA, SS.features = FALSE, cds.format = "DNA", lnc.format = "DNA", check.cds = TRUE, ignore.illegal = TRUE)







#### **Use "build_model" function to construct a SVM model based on the extracted feature file and known mRNA and lncRNA sequences.** 



    plant <- build_model(lncRNA.seq = lncRNA, mRNA.seq = mRNA, frequencies.file = frequencies, SS.features = FALSE, lncRNA.format = "DNA", mRNA.format = "DNA", parallel.cores = 10, folds.num = 10, seed = 1, gamma.range = (2^seq(-5, 0, 1)), cost.range = c(1, 4, 8, 16, 24, 32))




#### **Identification of lncRNA with retrained plant model**



**loading the model**

    plant = readRDS("Plant_model.rda")

    Seqs <- seqinr::read.fasta(file ="./test/test.fasta")

    Plant_results <- LncFinder::lnc_finder(Seqs, SS.features = FALSE, format = "DNA", frequencies.file = frequencies, svm.model = plant, parallel.cores = 2)




#### **Identification of lncRNA with original "human" model**


    Human_results <- LncFinder::lnc_finder(Seqs, SS.features = FALSE, format = "DNA", frequencies.file = "human", svm.model = "human", parallel.cores = 2)



#### **Identification of lncRNA with original "wheat" model**



    Wheat_results <- LncFinder::lnc_finder(Seqs, SS.features = FALSE, format = "DNA", frequencies.file = "wheat", svm.model = "wheat", parallel.cores = 2)
