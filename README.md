# DC4SC Term Project - UNSW-NB15 Network-Intrusion-Detection

## 簡介

本次使用[UNSW-NB15](https://www.unsw.adfa.edu.au/unsw-canberra-cyber/cybersecurity/ADFA-NB15-Datasets/)作為入侵檢測系統 (Intrusion detection system, IDS) 的資料集，並使用機器學習等，嘗試找出相關特徵並順利判別出潛在的入侵行為。

## 介紹

UNSW-NB資料集是由Australian Centre for Cyber Security (ACCS) 所建立，混合了現代正常網路活動紀錄與攻擊紀錄。

* UNSW-NB15包含了九種攻擊，分別如下:

    1. Fuzzers
    2. Analysis
    3. Backdoors
    4. DoS
    5. Exploits
    6. Generic
    7. Reconnaissance
    8. Shellcode
    9. Worms

* 使用Argus, Bro-IDS開發了12種演算法生成了總共49個features，主要分為以下類別:

    1. Flow Features
    2. Base Features
    3. Content Features
    4. Time Features
    5. Additional Generated Features
    6. Labelled Features

* UNSW-NB15總共包含2,540,044列，並分散在4個csv檔案中，本次將使用UNSW-NB15提供的訓練集和測試集，分別具有175,341列、82,332列。

相較於KDD CUP 1999資料集，UNSW-NB15資料集更符合現今的網路流量型態，部分攻擊方式例如stealthy或spy的風格在近年來更趨於與正常流量相同，此外無論是訓練集和測試集的資料集分布十分相同，降低了預測偏向部分類別的可能性。


## EDA

### Correlation Matrix
![](https://i.imgur.com/Cnnqibf.png)


1. 將資料及的類別性特徵如proto, service, state轉變為標籤性
2. 將training set和testing set獨有的特徵增加至對方，並設為0
3. 將attack_cat的類別性資料轉變為整數表示
4. 在使用StandardScaler, PCA等前處理後，發現準確度較以原資料訓練低

    ### PCA (Original)
    ![](https://i.imgur.com/UqQxZ4R.png)
    
    ### PCA (StandardScaler)
    ![](https://i.imgur.com/YqXGy6h.png)


## Model

因資料集提供了兩種Labelled Features，分別為表示各種攻擊類別的attack_cat和判斷是否為惡意攻擊的label。以下將分別對兩類進行模型訓練並預測。

* 在分辨label方法中，使用了Logistic Regression, Naive Bayes, Decision Tree, Random Forest	AdaBoost, Neural Network等方式，欲藉由各種演算法找到最佳分類模型。

    以下為神形網路模型之架構：

        Model: "sequential"
        _________________________________________________________________
        Layer (type)                 Output Shape              Param #   
        =================================================================
        dense (Dense)                (None, 32, 100)           19700     
        _________________________________________________________________
        dense_1 (Dense)              (None, 32, 50)            5050      
        _________________________________________________________________
        dense_2 (Dense)              (None, 32, 20)            1020      
        _________________________________________________________________
        dense_3 (Dense)              (None, 32, 1)             21        
        =================================================================
        Total params: 25,791
        Trainable params: 25,791
        Non-trainable params: 0
        _________________________________________________________________

* 在分辨attack_cat方法中，使用了Logistic Regression, Naive Bayes, Decision Tree等擅長於多類別類的模型進行訓練。

## Evaluation
### Classify Binary Classes

以下為各模型的訓練成果：

|     Model     | Logistic Regression | Naive Bayes | Decision Tree | Random Forest | AdaBoost | Neural Network |
|:-------------:|:-------------------:|:-----------:|:-------------:|:-------------:|:--------:|:--------------:|
|    Accuracy   |         0.81        |     0.77    |      0.94     |      0.95     |   0.92   |      0.72      |
|   Precision   |         0.81        |     0.76    |      0.93     |      0.94     |   0.92   |      0.77      |
|     Recall    |         0.95        |     0.84    |      0.94     |      0.95     |   0.93   |      0.63      |
|    F1 Score   |         0.86        |     0.82    |      0.95     |      0.96     |   0.94   |      0.74      |


![](https://i.imgur.com/oBwXR1a.png)


結論：Decision Tree, Random Forest, AdaBoost等決策樹相關的分類器和集成學習分類器在二元分類皆具有不錯的分類能力

### Classify Multi Classes

以下為各模型的訓練成果：

|   Model   | Logistic Regression | Decision Tree |   AdaBoost   |
|:---------:|:-------------------:|:-------------:|:------------:|
| Accuracy  |         0.66        |      0.80     |      0.57    |
| Precision |         0.66        |      0.80     |      0.65    |
|  Recall   |         0.66        |      0.80     |      0.57    |
| F1 Score  |         0.65        |      0.80     |      0.58    |

![](https://i.imgur.com/wytLrQ9.png)


結論：以條件機率預測的Naive Bayes有著最好的判別能力，相關指標皆為各模型中最高。

## 參考資料
Moustafa, Nour & Slay, Jill. (2016). The evaluation of Network Anomaly Detection Systems: Statistical analysis of the UNSW-NB15 data set and the comparison with the KDD99 data set. 1-14. 10.1080/19393555.2015.1125974. 
