### 定义数据
TensorFlow中所有的变量必须经过初始化才能使用  
tf.constant()定义常量  
tf.Variable()定义变量  
eg:  
计算 a=(b+c)∗(c+2)  
import tensorflow as tf  
# 首先，创建一个TensorFlow常量=>2  
const = tf.constant(2.0, name='const')  
# 创建TensorFlow变量b和c  
b = tf.Variable(2.0, name='b')  
c = tf.Variable(1.0, dtype=tf.float32, name='c')  

### 定义运算(也称TensorFlow operation)

d = tf.add(b, c, name='d')
e = tf.add(c, const, name='e')
a = tf.multiply(d, e, name='a')
