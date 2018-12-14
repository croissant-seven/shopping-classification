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


## Idea for model
(product + model) seems having similar context
(brand + maker) seems having similar context
need to remove meaningless word such as 해당없음, 상세설명참조 etc.

CNN would be Good start to train text data either.
- hypothesize that sequence of word in text data is not important in product description.
- Expect that exact word matching is hard to be achieved.

