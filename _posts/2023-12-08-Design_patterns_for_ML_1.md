# Design patterns for Machine Learning

There exists something called "Machine learning design patterns": patterns that reappear all the time in ML/AI. But what I have in mind here is different: I actually want to deal with programming design patterns, in python, but having in mind a data scientist.
The motivation is that the literature about design patterns is usually written for a generic user, and the examples taken do not resonate at all with me: example classes about "payroll applications" (with classes like Manager, Employee, etc...) or Cars or whatever.
I simply thought that one could adapt some of these patterns to situations actually encountered by a data scientist.

## The Adapter pattern
So, let's start with the **Adapter pattern**. 

This pattern allows objects with incompatible interfaces to work together. In data science, it can be useful when we deal with various libraries and APIs that have different interfaces for similar functionalities.


**Problem** : For a particular project, I need to use both scikit-learn and TensorFlow. Unfortunately these libraries have different interfaces for training models. Therefore I want to write a unified interface to train and evaluate models regardless of the underlying library.

**Solution** : Implement an adapter that provides a consistent interface, regardless of the machine learning library being used.

## Example Code:

First, let's define the standard interface for a machine learning model:
```python
class ModelInterface:
    def fit(self, X, y):
        pass

    def predict(self, X):
        pass
```

Now, let's say we have two different model classes, one from scikit-learn and one from TensorFlow:

```python
# Scikit-learn model
from sklearn.ensemble import RandomForestClassifier

class SklearnModel:
    def __init__(self):
        self.model = RandomForestClassifier()

    def train(self, X, y):
        self.model.fit(X, y)

    def evaluate(self, X):
        return self.model.predict(X)

# TensorFlow model
import tensorflow as tf

class TensorFlowModel:
    def __init__(self):
        self.model = tf.keras.models.Sequential([
            tf.keras.layers.Dense(10, activation='relu'),
            tf.keras.layers.Dense(1, activation='sigmoid')
        ])
        self.model.compile(optimizer='adam', loss='binary_crossentropy')

    def train(self, X, y):
        self.model.fit(X, y, epochs=10)

    def evaluate(self, X):
        return self.model.predict(X)

```



Now, I need to create adapters for these models to conform to our ModelInterface:


```python
class SklearnModelAdapter(ModelInterface):
    def __init__(self, sklearn_model):
        self.model = sklearn_model

    def fit(self, X, y):
        self.model.train(X, y)

    def predict(self, X):
        return self.model.evaluate(X)

class TensorFlowModelAdapter(ModelInterface):
    def __init__(self, tensorflow_model):
        self.model = tensorflow_model

    def fit(self, X, y):
        self.model.train(X, y)

    def predict(self, X):
        return self.model.evaluate(X)

```

## How to use it


```python
# Using the adapter
X_train, y_train, X_test = load_dataset() # Assume you wrote previously some dataloader

# Use scikit-learn model
sklearn_model = SklearnModel()
model_adapter = SklearnModelAdapter(sklearn_model)
model_adapter.fit(X_train, y_train)
predictions = model_adapter.predict(X_test)

# Use TensorFlow model
tensorflow_model = TensorFlowModel()
model_adapter = TensorFlowModelAdapter(tensorflow_model)
model_adapter.fit(X_train, y_train)
predictions = model_adapter.predict(X_test)
```

Now, the two adapters SklearnModelAdapter and TensorFlowModelAdapter adapt the SklearnModel and TensorFlowModel to the ModelInterface. I can use both models interchangeably without worrying about their underlying implementations.

The whole situation can be summarized with some OOP class diagrams as well:


<html>
<body>
<p>
      <div style="text-align: center;">
	<img src="/images/design_patterns_1_class_diagram.png" alt="Adapter design pattern" width="50%" >
      </div>
</p>
</body>
</html>


<!--- 
classDiagram
    class ModelInterface {
        <<interface>>
        +train()
        +predict()
    }

    class SklearnModel {
        +sklearn_specific_train()
        +sklearn_specific_predict()
    }

    class TensorFlowModel {
        +tensorflow_specific_train()
        +tensorflow_specific_predict()
    }

    class SklearnModelAdapter {
        +train()
        +predict()
    }

    class TensorFlowModelAdapter {
        +train()
        +predict()
    }

    ModelInterface <|.. SklearnModelAdapter
    ModelInterface <|.. TensorFlowModelAdapter
    SklearnModelAdapter --> SklearnModel
    TensorFlowModelAdapter --> TensorFlowModel
-->




