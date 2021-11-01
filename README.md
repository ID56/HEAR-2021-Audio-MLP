# Audio-MLP

MLP-based models for learning audio representations. Submission for [HEAR-2021@NeurIPS'21](https://neuralaudio.ai/hear2021-holistic-evaluation-of-audio-representations.html).

## Setup

```
git clone https://github.com/ID56/HEAR-2021-Audio-MAE.git
python3 -m pip install HEAR-2021-Audio-MAE
```
## Usage
The module to be imported after installation is `audiomlp`.

```python
from audiomlp import load_model, get_timestamp_embeddings, get_scene_embeddings

model = load_model("checkpoints/audiomae.pth")

b, ms, sr = 2, 1000, 16000
dummy_input = torch.randn(b, int(sr * ms / 1000))

embeddings, timestamps = get_timestamp_embeddings(dummy_input, model)
scene_embeddings = get_scene_embeddings(dummy_input, model)
```

The installation can also be verified by the [validator](https://github.com/neuralaudio/hear-validator):

```
hear-validator audiomlp --model checkpoints/audiomae.pth
```

---

The model can also be used independent of the HEAR common API:

```python
from audiomlp.models import AudioMLP_Wrapper

model = AudioMLP_Wrapper(
    sample_rate=16000,
    timestamp_embedding_size=8,
    scene_embedding_size=1584,
    encoder_type="audiomae",
    encoder_ckpt="checkpoints/audiomae.pth"
)
```

## Models

|   Model Name    | # Params† | GFLOPS*† | Sampling Rate | Hop Length | Timestamp Embedding | Scene Embedding |  Location     |
| --------------- | --------- | -------  | ------------- | ---------- | ------------------- | --------------- | ------------- |
|     kwmlp       |    424K   | 0.045    |    16000      |    10ms    |  64                 |   1024          |  [kwmlp(1.7Mb)](checkpoints/kwmlp.pth)   |
|    audiomae     |    213K   | 0.046    |    16000      |    10ms    |  8                  |   1584          |  [audiomae(1.7Mb)](checkpoints/audiomae.pth)   |

† <sub>Only considering the encoder, which is used for generating embeddings.</sub><br>
\* <sub>Although there is no direct way to count FLOPS like parameters, you can use [facebookresearch/fvcore](https://github.com/facebookresearch/fvcore/blob/main/docs/flop_count.md). The FLOPS measured are per 1 single input (spectrogram, tensor of shape `(1, 40, 98)`).</sub>

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/80x15.png" /></a><br />The trained checkpoints are licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>, as per HEAR-2021 requirements. You may also download them from drive: [ [audio-mae-f4-v1](https://drive.google.com/uc?id=1Fw60-jSVDMabhKaZIqnzAYHYZoNyRX8s&export=download) | [audio-mae-f8-v2](https://drive.google.com/uc?id=14p4i3JkE-OFtv43OiWS43hsoTACZhOBd&export=download) ].

## Notes

All models were trained on:
- A standard Kaggle environment: a single 16GiB NVIDIA Tesla P100, CUDA 11.0, CuDNN 8.0.5, python 3.7.10.
- KW-MLP was trained on Google Speech Commands V2-35, and the weights are a direct port of its paper [1].
- AudioMAE was trained on the training splits from the HEAR2021 Open tasks.
- Both models are based on gated-MLPs [2].