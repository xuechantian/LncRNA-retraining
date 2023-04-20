# **Plant lncRNA Identification with improved models**





### **This is a repository of code used for identifying long non-coding RNAs (lncRNAs) in plants. In order to improve the accuracy of plant lncRNA identification, we retrained and updated the models of several mainstream software .**

#### **Email:** xuechan.tian@bjfu.edu.cn








## **1 Data sources.** 



  The training data sets used for model retraining were derived from the literature "Pattern recognition analysis on long noncoding RNAs: a tool for prediction in plants", [DOI Link](https://doi.org/10.1093/bib/bby034).




## **2 Model retraining of CPAT software and prediction of lncRNA.**



### **Prerequisite:**
CPAT (version 1.2.4)

    conda create -n py27 python=2.7 -y

    source activate py27

    pip2 install CPAT



### **Usage**


#### **Use "make_hexamer_tab.py" to create a hexamer table.**


    make_hexamer_tab.py -c mRNA_dataset.fasta -n lncRNA_dataset.fasta > Plant_Hexamer.tsv




#### **Use "make_logitModel.py" to build a logistic regression model.**


    make_logitModel.py  -x Plant_Hexamer.tsv -c mRNA_dataset.fasta -n lncRNA_dataset.fasta  -o Plant



#### **Identification of lncRNA with retrained plant model.**
 The coding probability (CP) cutoff: 0.46 (CP >=0.46 indicates coding sequence, CP < 0.46 indicates noncoding sequence).


    cpat.py -x Plant_Hexamer.tsv -d Plant.logit.RData -g test.fasta -o  CPAT_plant.output



#### **Identification of lncRNA with original model**


    cpat.py -x Human_Hexamer.tsv -d Human_logitModel.RData -g test.fasta -o CPAT_human.output





## **3 Model retraining of PLEK software and prediction of lncRNA.**


PLEK (version 1.2.0)


### **Prerequisite:**
   Download PLEK.1.2.tar.gz from [https://sourceforge.net/projects/plek/files/](https://sourceforge.net/projects/plek/files/).
   
    tar zvxf PLEK.1.2.tar.gz 
    
    cd PLEK.1.2
   
 
 
 
 
### **Usage**



#### **Build the plant model useing PLEKModelling.py**


    python PLEKModelling.py -mRNA mRNA_dataset.fasta -lncRNA lncRNA_dataset.f
