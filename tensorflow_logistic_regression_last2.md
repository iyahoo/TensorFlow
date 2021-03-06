# ロジスティック回帰

2つの正規分布を使いロジスティック回帰をおこなう。

## Sample

```python
# coding:utf-8
import tensorflow as tf
import numpy as np

x1 = np.random.randn(500,2) + np.array([0,-2])
x2 = np.random.randn(500,2) + np.array([0,2])

X = np.vstack([x1, x2]).astype(np.float32)
Y = np.array([0]*500 + [1]*500)

N = len(Y)
T = np.zeros((N,2))
for i in xrange(N):
	T[i, Y[i]] = 1

tfX = tf.placeholder(tf.float32, [None, 2])
tfY = tf.placeholder(tf.float32, [None, 2])

W = tf.Variable(tf.random_normal([2,2], stddev=0.01), name="weight")
b = tf.Variable(tf.random_normal([2], stddev=0.01), name="bias")

learning_rate = 0.01
activation = tf.nn.softmax(tf.matmul(tfX, W) + b)

init_op = tf.initialize_all_variables()

cost = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(activation, T))
train_op = tf.train.GradientDescentOptimizer(learning_rate).minimize(cost)

correct_prediction = tf.equal(tf.argmax(activation,1), tf.argmax(T,1))
accuracy_op = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))

with tf.Session() as sess:
	sess.run(init_op)

	for i in xrange(1000):
		sess.run(train_op, feed_dict={tfX: X, tfY: T})
		
		if i % 100 == 0:
			a,c = sess.run([accuracy_op,cost], feed_dict={tfX: X, tfY: T})
			print "step %d, cost %f, accuracy %f" % (i,c,a)
```

実行結果

```
step 0, cost 1380.991089, predict 0.685000, accuracy 0.685000
step 100, cost 992.892090, predict 0.980000, accuracy 0.980000
step 200, cost 769.331665, predict 0.982000, accuracy 0.982000
step 300, cost 634.025146, predict 0.983000, accuracy 0.983000
step 400, cost 545.197266, predict 0.983000, accuracy 0.983000
step 500, cost 482.741455, predict 0.983000, accuracy 0.983000
step 600, cost 436.451721, predict 0.983000, accuracy 0.983000
step 700, cost 400.731537, predict 0.983000, accuracy 0.983000
step 800, cost 372.287750, predict 0.983000, accuracy 0.983000
step 900, cost 349.065369, predict 0.983000, accuracy 0.983000
```

## 参考

* [Breast Cancer Wisconsin (Prognostic) Data Set](http://archive.ics.uci.edu/ml/datasets/Breast+Cancer+Wisconsin+(Prognostic))
