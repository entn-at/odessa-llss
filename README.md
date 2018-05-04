# Low-latency speaker spotting
Low-latency speaker spotting with online diarization and detection
## Installation
```bash
$ conda create --name pyannote python=3.6 anaconda
$ source activate pyannote
$ conda install -c yaafe yaafe=0.65
$ pip install pyannote.audio==0.3.1
$ pip install pyannote.db.odessa.ami==0.7
$ git clone https://gitlab.eurecom.fr/odessa/llss.git
```

## Citation
If you use this tool, please cite the following paper:
```
@inproceedings{patino2018low,
  title={{Low-latency Speaker Spotting with Online Diarization and Detection}},
  author={Patino, Jose and Yin, Ruiqing and Delgado, H\'ector and Bredin, Herv\'e and Komaty, Alain and Wisniewski, Guillaume and Barras, Claude and Evans, Nicholas and Marcel, S\'ebastien },
  booktitle={The Speaker and Language Recognition Workshop (Odyssey 2018)},
  year={2018}
}
```

## Usage
### Embedding training and extraction
Please follow the [`documentation `](https://github.com/pyannote/pyannote-audio/tree/master/tutorials/speaker-embedding) in [`pyannote.audio`](https://github.com/pyannote/pyannote-audio) to extract embeddings. A pretrained model (trained and validated in AMI database) is available in `speaker-embedding-pretrained-models/1s/`

### Low-latency speaker spotting with segmental diarization
```bash
$ export $EMBEDDING_DIR=/path/of/extracted/embeddings
$ python speaker-spotting.py segmental EMBEDDING_DIR AMI.SpeakerSpotting.MixHeadset  /path/of/output/file.json

```
### Other options 
```bash
$ python speaker-spotting.py -h

Speaker-spotting
Usage:
  speaker-spotting.py oracle [--subset=<subset>] <embedding_path> <database.task.protocol> <output_file>
  speaker-spotting.py automatic [--subset=<subset>] <embedding_path> <database.task.protocol> <diarization.mdtm> <output_file>
  speaker-spotting.py segmental [--subset=<subset> --automatic-sad --sad=<sad.mdtm>] <embedding_path> <database.task.protocol> <output_file>
  speaker-spotting.py -h | --help
  speaker-spotting.py --version
Options:
  <database.task.protocol>   Set evaluation protocol (e.g. "Etape.SpeakerDiarization.TV").
  --subset=<subset>          Evaluated subset (train|developement|test) [default: development]
  <embedding_path>           Path of embedding which is trained by triplet loss.
  <output_file>              File path to store the score.
  <diarization.mdtm>         The diarization results in mdtm format.
  --sad=<sad.mdtm>           The speech activity detection result in mdtm format.
  -h --help                  Show this screen.

"oracle" mode:
        Low-latency speaker spotting system based on oracle diarization result.
"automatic" mode:
        Low-latency speaker spotting system based on automatic diarization result.
"segmental" mode:
        Low-latency speaker spotting system based on segmental diarization result.

```

### Evaluation
```bash
$ pyannote-metrics.py spotting --subset=test --latency=1 --latency=5 --latency=10 AMI.SpeakerSpotting.MixHeadset path/of/output/file.json
Trial (test set): 115110it [00:31, 3709.05it/s]
Trial (test set): 115110it [00:35, 3251.75it/s]
> path/of/output/file.det.absolute.XXs.txt
> path/of/output/file.det.speaker.XXs.txt

latency      EER% @ 1s    EER% @ 5s    EER% @ 10s
---------  -----------  -----------  ------------
absolute         46.96        26.99         23.99
speaker          41.72        21.40         19.65

```
