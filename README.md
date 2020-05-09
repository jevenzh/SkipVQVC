# SkipVQVC
Implementation of SkipVQVC with variant settings. Skip connection is an powerful technique in deep learning. However, in auto-encoder based voice conversion(VC) domain, skip connection is often no-used. Skip-connection cause model learning too fast, and overfitting on reconstruction, and such a model cannot fullfill VC anymore. In this paper, we discuss how quantization can form a strong bottleneck that skip-connection VC can fullfilled.

## preprocessing
```bash
python preprocessing.py [input_dir (VCTK/wav48)] [output_dir npy dir]
```
## File architecture
```bash
# File 
- SkipVQVC
  |- logger (some utlis used in tensorboard)
  |  |.
  |
  |- trainer (differnt trainer have different properties)
  |  |- train_normal.py
  |  |- train_rhythm.py (split speech to rhythm fator, shoud use vqvc+_rhythm model)
  |  |- train_mean_std.py (train with input normalized by mean and std)
  |
  |- model (different models like normal, speaker vae, rhythm, )
  | |- .
  | |- .
  |
  |- utils
```
##  Training config

- **-train\_dir** is your training dir
- **-test\_dir** is your testing dir (unseen speakers)
- **-m** which model do you want in model/* (for example: vqvc+)
- **-n** number of vectors in codebook
- **-ch** channels in encoder and decoder
- **-t** which trainer do you want in trainer/* (for example: train_normal)
- **--load_checkpoint**, if you want to load checkpoint(if it is in the checkpoint dir, for example: True)

checkpoint and output dir is auto generated by you model, trainer n_embed and channel. Load checkpoint it auto load the files match its setting.

## Example
```bash
python train.py -train_dir /homes/aa/mel/mel.melgan -m vqvc+ -n 128 -ch 128 -t train_normal
--> "Saving model and optimizer state at iteration 0 to checkpoint/vqvc+_n128_ch128_train_normal/gen"
--> "Saving model and optimizer state at iteration 100 to checkpoint/vqvc+_n128_ch128_train_normal/gen"
```

## Tensorboard
```bash
tensorboard --logdir output/vqvc+_n128_ch128_train_normal
```


## The Whole model are still in investigation to find the best parameters.
```bash
# if you want to recover the result in papers.
python train.py -train_dir your-path-to-npy-dir -m vqvc+ -n 64 -ch 64 -t train_normal

# if you want to train with rhythm information ( adjust rhythm )
python train.py -train_dir your-path-to-npy-dir -m vqvc+_rhythm -n 128 -ch 128 -t train_rhythm

# if you find that normal trainging is not very good for one-shot, you can train resample. It resample the quantized code which eliminate more speaker infomration from content

python train.py -train_dir your-path-to-npy-dir -m vqvc+_resample -n 512 -ch 512 -t train_normal

# We find that normalization on embeeding space imporve the result, you can try this
python train.py -train_dir your-path-to-npy-dir -m vqvc+ -n 64 -ch 512 -t train_simple_normalize


# Still in investigation...., speaker quantize <--> cav on speaker embedding

```