# CipherFace

<div align="center">

[![Downloads](https://static.pepy.tech/personalized-badge/cipherface?period=total&units=international_system&left_color=grey&right_color=blue&left_text=downloads)](https://pepy.tech/project/cipherface)
[![Stars](https://img.shields.io/github/stars/serengil/cipherface?color=yellow&style=flat&label=%E2%AD%90%20stars)](https://github.com/serengil/cipherface/stargazers)
[![License](http://img.shields.io/:license-MIT-green.svg?style=flat)](https://github.com/serengil/cipherface/blob/master/LICENSE)
[![Tests](https://github.com/serengil/cipherface/actions/workflows/tests.yml/badge.svg)](https://github.com/serengil/cipherface/actions/workflows/tests.yml)

[![Blog](https://img.shields.io/:blog-sefiks.com-blue.svg?style=flat&logo=wordpress)](https://sefiks.com)
[![YouTube](https://img.shields.io/:youtube-@sefiks-red.svg?style=flat&logo=youtube)](https://www.youtube.com/@sefiks?sub_confirmation=1)
[![Twitter](https://img.shields.io/:follow-@serengil-blue.svg?style=flat&logo=x)](https://twitter.com/intent/user?screen_name=serengil)

[![Support me on Patreon](https://img.shields.io/endpoint.svg?url=https%3A%2F%2Fshieldsio-patreon.vercel.app%2Fapi%3Fusername%3Dserengil%26type%3Dpatrons&style=flat)](https://www.patreon.com/serengil?repo=deepface)
[![GitHub Sponsors](https://img.shields.io/github/sponsors/serengil?logo=GitHub&color=lightgray)](https://github.com/sponsors/serengil)
[![Buy Me a Coffee](https://img.shields.io/badge/-buy_me_a%C2%A0coffee-gray?logo=buy-me-a-coffee)](https://buymeacoffee.com/serengil)

</div>

CipherFace is a homomorphic encryption-driven python framework for secure cloud-based facial recognition. It combines [DeepFace](https://github.com/serengil/deepface) and [TenSEAL](https://github.com/OpenMined/TenSEAL) libraries.

## Installation [![PyPI](https://img.shields.io/pypi/v/cipherface.svg)](https://pypi.org/project/cipherface/)

The easiest way to install CipherFace is to download it from [`PyPI`](https://pypi.org/project/cipherface/). It's going to install the library itself and its prerequisites as well.

```shell
$ pip install cipherface
```

Alternatively, you can also install deepface from its source code. Source code may have new features not published in pip release yet.

```shell
$ git clone https://github.com/serengil/cipherface.git
$ cd cipherface
$ pip install -e .
```

Once you installed the library, then you will be able to import it and use its functionalities.

```python
from cipherface import CipherFace
```

## On Prem Encryption

When you initialize a CipherFace object, it sets up an FHE cryptosystem. Currently, CipherFace supports the [`VGG-Face`](https://sefiks.com/2018/08/06/deep-face-recognition-with-keras/), [`Facenet`](https://sefiks.com/2018/09/03/face-recognition-with-facenet-in-keras/), and [`Facenet512`](https://sefiks.com/2018/09/03/face-recognition-with-facenet-in-keras/) facial recognition models, as well as Euclidean and cosine distance metrics.

```python
# build a cryptosystem
onprem = CipherFace(
    facial_recognition_model="Facenet",
    distance_metric="euclidean",
)

# export keys of built cryptosystem
onprem.export_private_key("private.txt")
onprem.export_public_key("public.txt")

# create vector embedding for 1st image and encrypt in one shot
source_embedding_encrypted = onprem.securely_embed(img_path="dataset/img1.jpg")
```

The on-prem system should generate embeddings for its facial database and encrypt them in advance. This process only needs to be done once to extract the encrypted embeddings. Once encrypted, these embeddings can be securely stored in the cloud.

## Encrypted Distance Calculation On Cloud

The cloud can also generate vector embeddings and encrypt them since encryption only requires a public key. Additionally, it can compute the encrypted distance between a recently generated encrypted embedding and an encrypted embedding created on the on-prem side.

```python
# cloud loads cryptosystem with public key
onprem = CipherFace(
    facial_recognition_model="Facenet",
    distance_metric="euclidean",
    cryptosystem="public.txt",
)

# create vector embedding for target image and encrypt in one shot
target_embedding_encrypted = cloud.securely_embed(img_path="dataset/target.jpg")[0]

encrypted_distance = cloud.calculate_encrypted_distance(
    target_embedding_encrypted,
    source_embedding_encrypted
)
```

## On Prem Verification

Once the cloud calculates the encrypted distance, only the on-prem system can decrypt it since it holds the private key of the cryptosystem. This allows the on-prem system to determine whether the source and target images belong to the same person or different individuals.

```python
# on prem loads cryptosystem with private key
onprem = CipherFace(
    facial_recognition_model="Facenet",
    distance_metric="euclidean",
    cryptosystem="private.txt",
)

# on prem restores distance
decrypted_distance = onprem.restore(encrypted_distance)

# verification
is_verified = onprem.verify(decrypted_distance)

if is_verified is True:
    print("they are same person")
else:
    print("they are different persons")
```

In this setup, the cloud system performs the distance calculation, utilizing most of the computational power. The on-prem system, holding the private key, is only responsible for decrypting the distances to determine whether the images belong to the same person or different individuals.

## Contribution

Pull requests are more than welcome! If you are planning to contribute a large patch, please create an issue first to get any upfront questions or design decisions out of the way first.

Before creating a PR, you should run the unit tests and linting locally by running `make test && make lint` command. Once a PR sent, GitHub test workflow will be run automatically and unit test and linting jobs will be available in [GitHub actions](https://github.com/serengil/cipherface/actions) before approval.

## Support

There are many ways to support a project - starring⭐️ the GitHub repo is just one 🙏

If you do like this work, then you can support it financially on [Patreon](https://www.patreon.com/serengil?repo=deepface), [GitHub Sponsors](https://github.com/sponsors/serengil) or [Buy Me a Coffee](https://buymeacoffee.com/serengil).

<a href="https://www.patreon.com/serengil?repo=deepface">
<img src="https://raw.githubusercontent.com/serengil/deepface/master/icon/patreon.png" width="30%" height="30%">
</a>

<a href="https://github.com/sponsors/serengil">
<img src="https://raw.githubusercontent.com/serengil/deepface/refs/heads/master/icon/github_sponsor_button.png" width="37%" height="37%">
</a>

<a href="https://buymeacoffee.com/serengil">
<img src="https://raw.githubusercontent.com/serengil/deepface/master/icon/bmc-button.png" width="25%" height="25%">
</a>

## Citation

Please cite CipherFace in your publications if it helps your research. Here is its BibTex entry:

```bibtex
@misc{cipherface,
  author       = {Serengil, Sefik Ilkin},
  title        = {CipherFace: A Homomorphic Encryption-Driven Framework for Secure Cloud-Based Facial Recognition},
  year         = {2024},
  url          = {https://github.com/serengil/cipherface},
  note         = {GitHub repository}
}
```

## Licence

CipherFace is licensed under the MIT License - see [`LICENSE`](https://github.com/serengil/cipherface/blob/master/LICENSE) for more details.
