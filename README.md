# Simple-HOHMM  

[![Build Status](https://travis-ci.org/jacobkrantz/Simple-HOHMM.svg?branch=master)](https://travis-ci.org/jacobkrantz/Simple-HOHMM)
[![Coverage Status](https://coveralls.io/repos/github/jacobkrantz/Simple-HOHMM/badge.svg?branch=master)](https://coveralls.io/github/jacobkrantz/Simple-HOHMM?branch=master)
[![Documentation Status](https://readthedocs.org/projects/simple-hohmm/badge/?version=latest)](http://simple-hohmm.readthedocs.io/en/latest/?badge=latest)  

Simple-HOHMM is an end-to-end sequence classifier using Hidden Markov Models. Let the builder construct a model for you based on chosen model attributes. Now you can solve the classic problems of HMMs: evaluating, decoding, and learning. Play with different orders of history to maximize the accuracy of your model!

## General

#### Solving Fundamental Problems
* **Evaluation**  
	Given an observation sequence and an HMM, determine the probability that the HMM would emit that exact observation sequence. Done with the *Forward Algorithm*.
* **Decoding**  
	Given an observation sequence and an HMM, determine the most likely hidden state sequence that would emit the observation sequence. Done with the *Viterbi Algorithm*.
* **Learning**  
	Given a set of observation sequences and an HMM, reestimate the model parameters so as to maximize the probabilities resulting from the Evaluation problem. Done with the *Baum Welch EM Algorithm*.

#### Features
* Learning is done in any manner desired: **supervised**, **semi-supervised**, or **unsupervised**. Supervised is done with training examples of explicit counts. Semi-supervised is generated with some examples followed by a learning algorithm. Unsupervised is done by creating a model of either uniformly or randomly distributed parameters followed by a learning algorithm.
* Discrete (Multinomial) emissions only.
* Ergotic state transitions are assumed by the model, but setting certain probabilities to zero effectively emulates unreachable states.
* Smoothing of model parameters is done with additive k-smoothing to avoid cases of zero probability, especially useful for higher order modeling.
* `HiddenMarkovModel` can be trained using `HiddenMarkovModelBuilder` or by passing in explicit HMM parameter values.

## Getting Started

#### Requirements
This project is currently written in pure python code with zero dependencies. Code has been tested and runs with both Python 2, Python 3, and [pypy](https://pypy.org/). Running with pypy offers drastic speed improvements, consider this when working with large models.

#### Installing Simple-HOHMM
No distribution exists on PyPI as the source code is still in flux. To use the code now, you can install it directly from the repository.  
Easy way:  
`pip install git+https://github.com/jacobkrantz/Simple-HOHMM.git`  
long way:  
```
>>> git clone https://github.com/jacobkrantz/Simple-HOHMM.git
>>> cd Simple-HOHMM
>>> python setup.py test
>>> python setup.py install
```

#### Documentation  
[Documentation](http://simple-hohmm.readthedocs.io/en/latest/?badge=latest) consisting of API reference and basic tutorials is live but has not been developed yet. For now we detail a basic example to get you up and running. Soon you will be able to take a more in-depth look at advanced modeling options.  
#### Example problem
(adapted from Wikipedia)  
Suppose villagers are either healthy or have a fever. Fevers are diagnosed by the doctor asking patients how they feel (normal, dizzy, or cold). Assuming their health can be modeled by a discrete Markov chain, the observations are `(normal, dizzy, cold)` and the hidden states are `(healthy, fever)`. The doctor has seen patients in the past, and kept that data. The observations are in one list and the states are in another such that `states[i]` corresponds to `observations[i]`:  
```python
observations = [
  ['normal', 'cold', 'dizzy', 'dizzy','normal','normal'],
  ['cold', 'cold', 'dizzy', 'normal','normal','normal'],
  ['dizzy', 'dizzy', 'cold', 'normal', 'dizzy', 'normal'],
  ['normal', 'normal', 'cold', 'dizzy', 'dizzy', 'dizzy']
]
states = [
	['healthy', 'healthy', 'fever', 'fever', 'healthy', 'healthy'],
	['healthy', 'fever', 'fever', 'healthy', 'healthy', 'fever'],
	['fever', 'fever', 'fever', 'healthy', 'healthy', 'healthy'],
	['healthy', 'healthy', 'healthy', 'fever', 'fever', 'fever']
]
```
We can now build a first order Hidden Markov Model based on the observations and states above:
```python
from SimpleHOHMM import HiddenMarkovModelBuilder as Builder
builder = Builder()
builder.add_batch_training_examples(observations, states)
hmm = builder.build()
```
Now suppose a patient has been seeing the doctor for three days and felt `(normal, cold, dizzy)`. What might the doctor guess about this patient's health? This is solved with Viterbi decoding:  
```python
obs =  ['normal', 'cold', 'dizzy']
states = hmm.decode(obs)
print(states) # prints: ['healthy', 'healthy', 'fever']
```
## Contributions
Contributions are welcome. We have not hashed out exactly what that will look like yet. For now, feel free to fork the repository and dive in as you see fit, whether that is making/improving documentation, tutorials, test cases, issues, or source code. Contributors should have all dependencies installed from `requirements.txt`. This can be done using:  
 `>>> pip install -r requirements.txt`

#### Testing
Run the unit tests before opening a pull request to ensure the build does not break.   
* Testing is done through the Python module `unittest`.
* Automated testing is performed by Travis CI.
* All test cases are located in `/test`.  

To run the entire suite of tests, execute:  
`>>> python -m unittest discover -s test`  
alternatively:  
`>>> python setup.py test`

#### Documentation
Docs are built using Sphinx and hosted using ReadTheDocs. You can edit the docs by updating the `.rst` files in the `/docs` folder.  
Make the documentation:
```
>>> cd docs
>>> make html
```  
View in browser:  
`>>> xdg-open build/html/index.html` (if using linux)  
`>>> open build/html/index.html` (if mac)

#### Viewing Code Coverage  
View code coverage before opening a pull request to ensure coverage is maintained or improved.   
run the unit tests using coverage:  
`>>> coverage run -m unittest discover -s test`  
View the coverage report:  
`>>> coverage report -m`  
