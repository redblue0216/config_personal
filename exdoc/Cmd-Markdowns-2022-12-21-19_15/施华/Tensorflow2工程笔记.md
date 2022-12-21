# Tensorflow2工程笔记

标签（空格分隔）： 施华

---

# Tensorflow基础操作
+ tensorflow使用张量作为数据的基本单位。张量在概念上等同于多维数组，它的重要属性是其形状、类型和值。可以通过张量的shape、dtype属性和numpy()方法获得。
+ tensorflow里有大量的基础操作，包含了大部分矩阵操作和初始化变量操作（tf.constant,tf.Variable）
+ tensorflow自动求导机制，在即时执行模式下，tensorflow引入了tf.GradientTape()这个“求导记录器”来实现自动求导
```python
import tensorflow as tf

x = tf.Variable(initial_value = 3.)
with tf.GradientTape() as tape:
    y = tf.square(x)
y_grad = tape.gradient(y,x)
print(y,y_grad)
```
+ tensorflow线性回归实例
    + 使用 tape.gradient(ys, xs) 自动计算梯度；
    + 使用 optimizer.apply_gradients(grads_and_vars) 自动更新模型参数。 
    ```python
    
    X = tf.constant(X)
    y = tf.constant(y)
    
    a = tf.Variable(initial_value=0.)
    b = tf.Variable(initial_value=0.)
    variables = [a, b]
    
    num_epoch = 10000
    optimizer = tf.keras.optimizers.SGD(learning_rate=5e-4)
    for e in range(num_epoch):
        # 使用tf.GradientTape()记录损失函数的梯度信息
        with tf.GradientTape() as tape:
            y_pred = a * X + b
            loss = tf.reduce_sum(tf.square(y_pred - y))
        # TensorFlow自动计算损失函数关于自变量（模型参数）的梯度
        grads = tape.gradient(loss, variables)
        # TensorFlow自动根据梯度更新参数
        optimizer.apply_gradients(grads_and_vars=zip(grads, variables))
    ```

+ 在这里，我们使用了前文的方式计算了损失函数关于参数的偏导数。同时，使用 tf.keras.optimizers.SGD(learning_rate=5e-4) 声明了一个梯度下降 优化器 （Optimizer），其学习率为 5e-4。优化器可以帮助我们根据计算出的求导结果更新模型参数，从而最小化某个特定的损失函数，具体使用方式是调用其 apply_gradients() 方法。
+ 注意到这里，更新模型参数的方法 optimizer.apply_gradients() 需要提供参数 grads_and_vars，即待更新的变量（如上述代码中的 variables ）及损失函数关于这些变量的偏导数（如上述代码中的 grads ）。具体而言，这里需要传入一个 Python 列表（List），列表中的每个元素是一个 （变量的偏导数，变量） 对。比如上例中需要传入的参数是 [(grad_a, a), (grad_b, b)] 。我们通过 grads = tape.gradient(loss, variables) 求出 tape 中记录的 loss 关于 variables = [a, b] 中每个变量的偏导数，也就是 grads = [grad_a, grad_b]，再使用 Python 的 zip() 函数将 grads = [grad_a, grad_b] 和 variables = [a, b] 拼装在一起，就可以组合出所需的参数了。

# Tensorflow模型建立与训练
+ Keras 有两个重要的概念： 模型（Model）和层（Layer）。层将各种计算流程和变量进行了封装（例如基本的全连接层，CNN 的卷积层、池化层等），而模型则将各种层进行组织和连接，并封装成一个整体，描述了如何将输入数据通过各种层以及运算而得到输出。在需要模型调用的时候，使用 y_pred = model(X) 的形式即可。Keras 在 tf.keras.layers 下内置了深度学习中大量常用的的预定义层，同时也允许我们自定义层。

+ Keras 模型以类的形式呈现，我们可以通过继承tf.keras.Model这个Python类来定义自己的模型。在继承类中，我们需要重写 __init__() （构造函数，初始化）和call(input)（模型调用）两个方法，同时也可以根据需要增加自定义的方法。
```python
class MyModel(tf.keras.Model):
    def __init__(self):
        super().__init__()     # Python 2 下使用 super(MyModel, self).__init__()
        # 此处添加初始化代码（包含 call 方法中会用到的层），例如
        # layer1 = tf.keras.layers.BuiltInLayer(...)
        # layer2 = MyCustomLayer(...)

    def call(self, input):
        # 此处添加模型调用的代码（处理输入并返回输出），例如
        # x = layer1(input)
        # output = layer2(x)
        return output

    # 还可以添加自定义的方法
```
+ 继承 tf.keras.Model 后，我们同时可以使用父类的若干方法和属性，例如在实例化类 model = Model() 后，可以通过 model.variables 这一属性直接获得模型中的所有变量，免去我们一个个显式指定变量的麻烦。
+ MLP实例
    + 模型构建tf.keras.Model 和 tf.keras.layers
    ```python
    class MLP(tf.keras.Model):
        def __init__(self):
            super().__init__()
            self.flatten = tf.keras.layers.Flatten()    # Flatten层将除第一维（batch_size）以外的维度展平
            self.dense1 = tf.keras.layers.Dense(units=100, activation=tf.nn.relu)
            self.dense2 = tf.keras.layers.Dense(units=10)
    
        def call(self, inputs):         # [batch_size, 28, 28, 1]
            x = self.flatten(inputs)    # [batch_size, 784]
            x = self.dense1(x)          # [batch_size, 100]
            x = self.dense2(x)          # [batch_size, 10]
            output = tf.nn.softmax(x)
            return output
    ```
    + 模型的训练： tf.keras.losses 和 tf.keras.optimizer
    ```python
    num_batches = int(data_loader.num_train_data // batch_size * num_epochs)
    for batch_index in range(num_batches):
        X, y = data_loader.get_batch(batch_size)
        with tf.GradientTape() as tape:
            y_pred = model(X)
            loss = tf.keras.losses.sparse_categorical_crossentropy(y_true=y, y_pred=y_pred)
            loss = tf.reduce_mean(loss)
            print("batch %d: loss %f" % (batch_index, loss.numpy()))
        grads = tape.gradient(loss, model.variables)
        optimizer.apply_gradients(grads_and_vars=zip(grads, model.variables))
    ```
    + 模型的评估： tf.keras.metrics
    ```python
    sparse_categorical_accuracy = tf.keras.metrics.SparseCategoricalAccuracy()
    num_batches = int(data_loader.num_test_data // batch_size)
    for batch_index in range(num_batches):
        start_index, end_index = batch_index * batch_size, (batch_index + 1) * batch_size
        y_pred = model.predict(data_loader.test_data[start_index: end_index])
        sparse_categorical_accuracy.update_state(y_true=data_loader.test_label[start_index: end_index], y_pred=y_pred)
    print("test accuracy: %f" % sparse_categorical_accuracy.result())
    ```
    
# 自定义层、损失函数和评估指标 
+ 自定义层需要继承 tf.keras.layers.Layer 类，并重写 __init__ 、 build 和 call 三个方法，如下所示：
```python
class MyLayer(tf.keras.layers.Layer):
    def __init__(self):
        super().__init__()
        # 初始化代码

    def build(self, input_shape):     # input_shape 是一个 TensorShape 类型对象，提供输入的形状
        # 在第一次使用该层的时候调用该部分代码，在这里创建变量可以使得变量的形状自适应输入的形状
        # 而不需要使用者额外指定变量形状。
        # 如果已经可以完全确定变量的形状，也可以在__init__部分创建变量
        self.variable_0 = self.add_weight(...)
        self.variable_1 = self.add_weight(...)

    def call(self, inputs):
        # 模型调用的代码（处理输入并返回输出）
        return output
```
+ 自定义损失函数需要继承 tf.keras.losses.Loss 类，重写 call 方法即可，输入真实值 y_true 和模型预测值 y_pred ，输出模型预测值和真实值之间通过自定义的损失函数计算出的损失值。下面的示例为均方差损失函数:
```python
class MeanSquaredError(tf.keras.losses.Loss):
    def call(self, y_true, y_pred):
        return tf.reduce_mean(tf.square(y_pred - y_true))
```
+ 自定义评估指标需要继承 tf.keras.metrics.Metric 类，并重写 __init__ 、 update_state 和 result 三个方法。下面的示例对前面用到的 SparseCategoricalAccuracy 评估指标类做了一个简单的重实现：
```python
class SparseCategoricalAccuracy(tf.keras.metrics.Metric):
    def __init__(self):
        super().__init__()
        self.total = self.add_weight(name='total', dtype=tf.int32, initializer=tf.zeros_initializer())
        self.count = self.add_weight(name='count', dtype=tf.int32, initializer=tf.zeros_initializer())

    def update_state(self, y_true, y_pred, sample_weight=None):
        values = tf.cast(tf.equal(y_true, tf.argmax(y_pred, axis=-1, output_type=tf.int32)), tf.int32)
        self.total.assign_add(tf.shape(y_true)[0])
        self.count.assign_add(tf.reduce_sum(values))

    def result(self):
        return self.count / self.total
```

# tensorflow训练工程辅助 
+ tensorflow使用checkpoint
    + 注意保存和加载的时候要保持键名的一致。
```python
# train.py 模型训练阶段

model = MyModel()
# 实例化Checkpoint，指定保存对象为model（如果需要保存Optimizer的参数也可加入）
checkpoint = tf.train.Checkpoint(myModel=model)
# ...（模型训练代码）
# 模型训练完毕后将参数保存到文件（也可以在模型训练过程中每隔一段时间就保存一次）
checkpoint.save('./save/model.ckpt')
```
```python
# test.py 模型使用阶段

model = MyModel()
checkpoint = tf.train.Checkpoint(myModel=model)             # 实例化Checkpoint，指定恢复对象为model
checkpoint.restore(tf.train.latest_checkpoint('./save'))    # 从文件恢复模型参数
# 模型使用代码
```

+ TensorBoard：训练过程可视化 
```python
summary_writer = tf.summary.create_file_writer('./tensorboard')
# 开始模型训练
for batch_index in range(num_batches):
    # ...（训练代码，当前batch的损失值放入变量loss中）
    with summary_writer.as_default():                               # 希望使用的记录器
        tf.summary.scalar("loss", loss, step=batch_index)
        tf.summary.scalar("MyScalar", my_scalar, step=batch_index)  # 还可以添加其他自定义的变量
```
+ 查看Graph和Profile信息
```python
tf.summary.trace_on(graph=True, profiler=True)  # 开启Trace，可以记录图结构和profile信息
# 进行训练
with summary_writer.as_default():
    tf.summary.trace_export(name="model_trace", step=0, profiler_outdir=log_dir)    # 保存Trace信息到文件
```
+ SaveModel模型导出/导入

    + 使用继承 tf.keras.Model 类建立的 Keras模型同样可以以相同方法导出，唯须注意call方法需要以@tf.function 修饰，以转化为 SavedModel 支持的计算图 
    ```python
        class MLP(tf.keras.Model):
        def __init__(self):
            super().__init__()
            self.flatten = tf.keras.layers.Flatten()
            self.dense1 = tf.keras.layers.Dense(units=100, activation=tf.nn.relu)
            self.dense2 = tf.keras.layers.Dense(units=10)
    
        @tf.function
        def call(self, inputs):         # [batch_size, 28, 28, 1]
            x = self.flatten(inputs)    # [batch_size, 784]
            x = self.dense1(x)          # [batch_size, 100]
            x = self.dense2(x)          # [batch_size, 10]
            output = tf.nn.softmax(x)
            return output
    
    model = MLP()
    ```
    + 模型导入并测试性能的过程也相同，唯须注意模型推断时需要显式调用 call 方法，即使用：
    ```python
    y_pred = model.call(data_loader.test_data[start_index: end_index])
    ```
    + 主要步骤
    ```python
    tf.saved_model.save(model, "保存的目标文件夹名称")
    model = tf.saved_model.load("保存的目标文件夹名称")
    ```
    
    




