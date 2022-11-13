# 《人工智能安全》作业

# Train CIFAR10 with PyTorch

I'm playing with [PyTorch](http://pytorch.org/) on the CIFAR10 dataset.

## Prerequisites
- Python 3.6+
- PyTorch 1.0+
- Ubuntu18.04 is recommended

## QuickStart
```
# Start training with: 

python main.py

# and CIFAR10 datasets are downloaded automatically
```
## Attention
- batch size
- epochs


## some inspiration
1.batch_size选择。对于小数据量的模型，可以全量训练，这样能更准确的朝着极值所在的方向更新。但是对于大数据，全量训练将会导致内存溢出，因此需要选择一个较小的batch_size。
如果这时选择batch_size为1，则此时为在线学习，每次修正方向为各自样本的梯度方向修正，难以达到收敛。batch_size增大，处理相同数据量的时间减少，但是达到相同精度的轮数增多。实际中可以逐步增大batch_size，随着batch_size增大，模型达到收敛，并且训练时间最为合适，这是一个trade-off
本人针对DLA尝试了5个epoch下的16batch_size和8batch_size，发现8batch_size下能更快收敛，而更大的batch_size则会out-of-memory

2.dropout。数据第一次跑模型的时候可以不加dropout，后期调优的时候dropout用于防止过拟合有比较明显的效果，特别是数据量相对较小的时候。

3.设置了断点继续训练，每个epochtrain完会保存

4.数据集载入的时，num_workers>0时在windows不可行，因为windows不支持多进程

5.学习速率的设置第一次可以设置大一点的学习率加快收敛，后续慢慢调整；也可以采用动态变化学习速率的方式（比如，每一轮乘以一个衰减系数或者根据损失的变化动态调整学习速率）。
学习率默认是0.1，我在学习率这里尝试了0.01和0.1两种不同的学习率作比较，一开始使用了默认的0.1,而后测试了0.05和0.01这两种学习率，发现这几种学习率都可以收敛，速率小收敛速度慢，没有出现因为学习率过大导致不收敛的情况

6.训练轮数。模型收敛即可停止迭代，如果连续几轮模型损失都没有相应减少，则停止迭代，这里因为显卡等原因，没有尝试过多epoch，尝试了5轮和10轮作比较，5轮的准确率达到了54%，10轮准确率达到了60%

7.正则化。为了防止过拟合，可通过加入l1、l2正则化。从公式可以看出，加入l1正则化的目的是为了加强权值的稀疏性，让更多值接近于零。而l2正则化则是为了减小每次权重的调整幅度，避免模型训练过程中出现较大抖动。

8：激活函数。常用的激活函数为sigmoid、tanh、relu、leaky relu、elu等。采用sigmoid激活函数计算量较大，而且sigmoid饱和区变换缓慢，求导趋近于0，导致梯度消失。sigmoid函数的输出值恒大于0，这会导致模型训练的收敛速度变慢。

## some result
![3轮结果](result1.jpg)
![5轮结果](result2.jpg)
![10轮结果](result3.jpg)