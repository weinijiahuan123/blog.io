---
layout: post
title: Decision Tree 1
date: 2017-10-26
categories: blog
tags: [decision, boosting]
description: Decision Tree 学习笔记

---
摘自：[Gradient Tree Boosting Algorithm](http://www.csuldw.com/2015/08/19/2015-08-19%20GBDT/)

看了大神的博客文章醍醐灌顶
##Boosting
Boosting: Converts a weak learner to a strong learner.

Weak learner: decision tree
##Adaboost
Adaboost算法是：在算法初始化阶段，为每一个样本赋予一个相等的weight，换言之，每个样本在开始都是一样重要的。接下来，每一次训练后得到的模型，对数据点的估计会有所差异，所以在每一步结束后，我们需要对weight进行处理，而处理的方式就是通过增加错分类的样本点的weight，同时减少分类正确的样本点的weight。这样能够确保，如果某些点经常被分错，那么就会被“严重关注”，也就会被赋予一个很高的weight。然后等进行了NN次迭代（迭代次数由用户指定），将会得到NN个简单的base learner，最后将它们组合起来，可以对它们进行加权（错误率越大的base learner 其权重值越小，错误率越小的基分类器权重值越大）、或者让它们进行投票等得到一个最终的模型。
##Gradient Boosting
gradient tree boosting 算法的核心在于，它的每棵树都是从上一次训练的所有树的残差中进行学习，进而拟合一棵回归（分类）树。在训练的时候，残差近似等于当前模型中损失函数的负梯度值。gradient boosting与传统的boosting有着很大的区别，它的每一次计算都是为了减少上一次的 residual，而为了减少这些residual，它会在residual减少的gradient方向上建立一个新的 model。所以说，在gradient boosting algorithm中，新model建立的目的是为了使先前模型的残差往梯度方向减少，与传统的boosting算法对正错样本赋予不同加权的做法有着极大的区别。

##A comparison of ensemble algorithms

基于决策树的组合算法常用的有三个，分别是Adaboost、RandomFrest以及本文的GBRT。

Adaboost是通过迭代的学习每一个基分类器，每次迭代中，把上一次错分类的数据权值增大，正确分类的数据权值减小，然后将基分类器的线性组合作为一个强分类器，同时给分类误差率较小的基本分类器以大的权值，给分类误差率较大的基分类器以小的权重值。Adaboost使用的是自适应的方法，其中概率分布式变化的，关注的是难分类的样本。

Random Forest与adaboost有些区别，可以说一种改进的bagging算法。Random Forest不仅对样本进行sampling，还对feature进行sampling。它通过随机的方式建立一个森林，森林里面有许多棵决策树，并且每一棵树之间是没有联系的。在得到森林之后，当有一个新的input sample进来的时候，就让森林中的每一棵决策树分别对其进行判断，看这个样本应该属于哪一类（就分类算法而言），然后看看哪一类选择最多（随机森林使用到的是 vote方式），就预测这个样本为该class。在建立每一棵决策树的过程中，有两点需要注意，即采样与完全分裂。首先是两个随机采样的过程，random forest对输入的数据要进行采样和列采样。对于行采样，是采用有放回的方式，即bootstrap sampling，也就是在采样得到的样本集合中，可能有重复的样本。举个例子，假设输入样本为N个，那么采样的样本也为N个。这样使得在训练的时候，每一棵树的输入样本都不是全部的样本，从某种程度上讲，相对不容易出现over-fitting。其次就是列采样，这个过程是从M个feature中，选择m个(m<<M)。之后就是对采样之后的数据使用完全分裂的方式建立决策树模型。最后得到的决策树，它的某一个叶子节点要么是无法继续分裂的，要么里面的所有样本的都是指向的同一类别。一般很多的决策树算法都一个重要的步骤-Pruning（剪枝），但是random forest并不这样干，由于之前的两个随机采样的过程保证了样本的随机性，所以替代了Pruning这个工作，也不太容易出现over-fitting。按照这种算法得到的random forest中的每一棵决策树都是非常弱的，但是当你把它们组合在一起的时候，就得对它刮目相看了。random forest可以这样来形容：每一棵决策树就是一个精通于某一领域的专家（因为我们从M个feature set中选择m个sub set让每一棵决策树进行学习），这样在random forest中就有了很多个精通不同领域的专家，对一个新的问题（input data），可以用不同的角度去看待它，最终由各个专家投票得到结果。random forest的分类准确率可以与adaboost媲美，但它对noise data更加鲁棒，运行速度比adaboost也快得多。

另外，random forest是可以实现并行化的，而adaboost无法并行。同时，random forest等bagging算法其本质是降variance的，而adaboost等boosting算法其实降的是bias。

最后，对于gradient tree boosting algorithm，它的每一次计算都是为了减少上一次训练模型的residual，而为了减少这些残差，可以在残差减少的梯度(Gradient)方向上建立一个新模型。这与adaboost和随机森林还是有很大区别的。