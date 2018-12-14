## base code procedure
1. read raw data file
h5py format - train, dev
keys: pid, b_cate, m_cate, s_cate, d_cate, product, model, maker, brand img_feature

2. split and parse raw data into tmp/base.chunk.{idx} files
It parses only product name
(pid, label as categorical binary vector, tuple of words + frequencies)

3. write data file
create 'train' 'dev' group
each group crates 4 datasets (dataset is similar to numpy array)
dataset - 'uni' (# of data, 32), int32            ==> words
dataset - 'w_uni', (# of data, 32), float32       ==> frequencies
dataset - 'cate', (# of data, num_classes), int32 ==> label
dataset - 'pid', (# of data,), S12                ==> pid

example of uni and w_uni
[32345 78545 78772 73344 87719 52697     0     0     0     0     0     0
     0     0     0     0     0     0     0     0     0     0     0     0
     0     0     0     0     0     0     0     0]
[2. 1. 1. 1. 1. 1. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
 0. 0. 0. 0. 0. 0. 0. 0.]

4. write meta file (cPickle serialize)
y_vocab

5. TextOnly model
[INFO    ] 2018-12-14 09:59:38 [classifier.py] [train:137] # of classes: 4215
[INFO    ] 2018-12-14 09:59:38 [classifier.py] [train:143] # of train samples: 799667
[INFO    ] 2018-12-14 09:59:38 [classifier.py] [train:144] # of dev samples: 200312
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] Layer (type)                    Output Shape         Param #     Connected to                     
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] ==================================================================================================
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] input_1 (InputLayer)            (None, 32)           0                                            
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] input_2 (InputLayer)            (None, 32)           0                                            
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] uni_embd (Embedding)            (None, 32, 128)      12800128    input_1[0][0]                    
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] reshape_1 (Reshape)             (None, 32, 1)        0           input_2[0][0]                    
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] dot_1 (Dot)                     (None, 128, 1)       0           uni_embd[0][0]                   
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65]                                                                  reshape_1[0][0]                  
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] reshape_2 (Reshape)             (None, 128)          0           dot_1[0][0]                      
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] dropout_1 (Dropout)             (None, 128)          0           reshape_2[0][0]                  
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] relu1 (Activation)              (None, 128)          0           dropout_1[0][0]                  
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] dense_1 (Dense)                 (None, 4215)         543735      relu1[0][0]                      
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] ==================================================================================================
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] Total params: 13,343,863
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] Trainable params: 13,343,863
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] Non-trainable params: 0
[INFO    ] 2018-12-14 09:59:38 [network.py] [<lambda>:65] __________________________________________________________________________________________________

Loss is getting smaller, and top1_accuracy is going up when training each epoch
...
Epoch 49/100
781/781 [==============================] - 448s 574ms/step - loss: 0.0014 - top1_acc: 0.3317 - val_loss: 0.0016 - val_top1_acc: 0.3707
Epoch 50/100
781/781 [==============================] - 435s 557ms/step - loss: 0.0014 - top1_acc: 0.3376 - val_loss: 0.0016 - val_top1_acc: 0.3763
Epoch 51/100
781/781 [==============================] - 434s 555ms/step - loss: 0.0014 - top1_acc: 0.3429 - val_loss: 0.0016 - val_top1_acc: 0.3822

...


## Idea for model
(product + model) seems having similar context
(brand + maker) seems having similar context
need to remove meaningless word such as 해당없음, 상세설명참조 etc.

CNN would be Good start to train text data either.
- hypothesize that sequence of word in text data is not important in product description.
- Expect that exact word matching is hard to be achieved.

