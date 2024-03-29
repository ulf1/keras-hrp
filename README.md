[![PyPI version](https://badge.fury.io/py/keras-hrp.svg)](https://badge.fury.io/py/keras-hrp)
[![PyPi downloads](https://img.shields.io/pypi/dm/keras-hrp)](https://img.shields.io/pypi/dm/keras-hrp)
[![DOI](https://zenodo.org/badge/508984862.svg)](https://zenodo.org/badge/latestdoi/508984862)


# keras-hrp: Hashed Random Projection layer for TF2/Keras
Hashed Random Projection layer for TF2/Keras.

## Usage
<a href="demo/Hashed Random Projections.ipynb">Hashed Random Projections (HRP), binary representations, encoding/decoding for storage</a> (notebook)


### Generate a HRP layer with a new hyperplane
The random projection or hyperplane is randomly initialized.
The initial state of the PRNG (`random_state`) is required (Default: 42) to ensure reproducibility.

```py
import keras_hrp as khrp
import tensorflow as tf

BATCH_SIZE = 32
NUM_FEATURES = 64
OUTPUT_SIZE = 1024

# demo inputs
inputs = tf.random.normal(shape=(BATCH_SIZE, NUM_FEATURES))

# instantiate layer 
layer = khrp.HashedRandomProjection(
    output_size=OUTPUT_SIZE,
    random_state=42   # Default: 42
)

# run it
outputs = layer(inputs)
assert outputs.shape == (BATCH_SIZE, OUTPUT_SIZE)
```


### Instiantiate HRP layer with given hyperplane

```py
import keras_hrp as khrp
import tensorflow as tf
import numpy as np

BATCH_SIZE = 32
NUM_FEATURES = 64
OUTPUT_SIZE = 1024

# demo inputs
inputs = tf.random.normal(shape=(BATCH_SIZE, NUM_FEATURES))

# create hyperplane as numpy array
myhyperplane = np.random.randn(NUM_FEATURES, OUTPUT_SIZE)

# instantiate layer 
layer = khrp.HashedRandomProjection(hyperplane=myhyperplane)

# run it
outputs = layer(inputs)
assert outputs.shape == (BATCH_SIZE, OUTPUT_SIZE)

```


### Serialize Boolean to Int8
Python stores 1-bit boolean values always as 8-bit integers or 1-byte. 
Some database technologies behave in similar way, and use up 8x-times of the theoretically required storage space (e.g., Postgres `boolean` uses 1-byte instead of 1-bit).
In order to save memory or storage space, chuncks of 8 boolean vector elements can be transformed into one 1-byte int8 number.

```py
import keras_hrp as khrp
import numpy as np

# given boolean values
hashvalues = np.array([1, 0, 1, 0, 1, 1, 0, 0])

# serialize boolean to int8
serialized = khrp.bool_to_int8(hashvalues)

# deserialize int8 to boolean
deserialized = khrp.int8_to_bool(serialized)

# check
np.testing.assert_array_equal(deserialized, hashvalues)
```


## Appendix

### Installation
The `keras-hrp` [git repo](http://github.com/ulf1/keras-hrp) is available as [PyPi package](https://pypi.org/project/keras-hrp)

```sh
pip install keras-hrp
pip install git+ssh://git@github.com/ulf1/keras-hrp.git
```

### Install a virtual environment

```sh
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt --no-cache-dir
pip install -r requirements-dev.txt --no-cache-dir
pip install -r requirements-demo.txt --no-cache-dir
```

(If your git repo is stored in a folder with whitespaces, then don't use the subfolder `.venv`. Use an absolute path without whitespaces.)

### Python commands

* Jupyter for the examples: `jupyter lab`
* Check syntax: `flake8 --ignore=F401 --exclude=$(grep -v '^#' .gitignore | xargs | sed -e 's/ /,/g')`
* Run Unit Tests: `PYTHONPATH=. pytest`

Publish

```sh
# pandoc README.md --from markdown --to rst -s -o README.rst
python setup.py sdist 
twine upload -r pypi dist/*
```

### Clean up 

```sh
find . -type f -name "*.pyc" | xargs rm
find . -type d -name "__pycache__" | xargs rm -r
rm -r .pytest_cache
rm -r .venv
```


### Support
Please [open an issue](https://github.com/ulf1/keras-hrp/issues/new) for support.


### Contributing
Please contribute using [Github Flow](https://guides.github.com/introduction/flow/). Create a branch, add commits, and [open a pull request](https://github.com/ulf1/keras-hrp/compare/).

### Acknowledgements
The "Evidence" project was funded by the Deutsche Forschungsgemeinschaft (DFG, German Research Foundation) - [433249742](https://gepris.dfg.de/gepris/projekt/433249742) (GU 798/27-1; GE 1119/11-1).

### Maintenance
- till 31.Aug.2023 (v0.1.0) the code repository was maintained within the DFG project [433249742](https://gepris.dfg.de/gepris/projekt/433249742?context=projekt&task=showDetail&id=433249742&)
- since 01.Sep.2023 (v0.2.0) the code repository is maintained by [@ulf1](https://github.com/ulf1).

### Citation
Please cite the arXiv Preprint when using this software for any purpose.

```
@misc{hamster2023rediscovering,
      title={Rediscovering Hashed Random Projections for Efficient Quantization of Contextualized Sentence Embeddings}, 
      author={Ulf A. Hamster and Ji-Ung Lee and Alexander Geyken and Iryna Gurevych},
      year={2023},
      eprint={2304.02481},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
```
