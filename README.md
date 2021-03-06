# Fearless Steps Challenge Phase-02
# Scoring Toolkit (FS02-Sctk)
Scoring Toolkit for the Fearless Steps Challenge Phase-02 Tasks

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

* [Overview](#overview)
* [Credits](#credits)
* [FS02 Scoring Metrics](#metrics)
* [Requirements](#requirements)
* [Folder Structure](#folder-structure)
* [Usage](#usage)
* [References](#references)
* [Author](#author)
* [License](#license)
* [Acknowledgements](#acknowledgements)

<!-- /code_chunk_output -->

## Overview
This software was developed at the University of Texas at Dallas, Center for Robust Speech Systems (UTD-CRSS). It serves as a wrapper around multiple third-party open-source code listed below. (See [Credits](#credits)).
This toolkit intends to provide a simple scoring mechanism streamlined for FS02 Challenge usage for all tasks mentioned in [FS02 Scoring Metrics](#metrics). For more details, please see the [FS02 Challenge Website](https://fearless-steps.github.io/ChallengePhase2/)


### FS02 Scoring Metrics
The scoring metrics for FS02 Challenge per Task are:

  1. Speech Activity Detection (SAD):                   Detection Cost Function (DCF)

  2. Speaker Identification (SID):                      Top-5 Accuracy (% Top-5 Acc.)

  3. Speaker Diarization (SD):                          Diarization Error Rate (% DER)
      a. Track 1: SD using system SAD     (SD_track1)
      b. Track 2: SD using reference SAD  (SD_track2)

  4. Automatic Speech Recognition (ASR):                Word Error Rate (% WER)
      a. Track 1: Continuous stream ASR       (ASR_track1) 
      b. Track 2: ASR using Diarized Segments (ASR_track2) 


## Requirements
* Linux (not tested on Windows)
* Python >= 3.6 (3.7 recommended: Tested with Python 3.6.X, and 3.7.X.)
* Kaldi [Kaldi Installation](http://kaldi-asr.org/doc/install.html) (*Required for ASR evaluation)
* intervaltree>=3.0.0     (see requirements.txt)
* numpy>=1.16.2           (see requirements.txt)
* scipy>=0.17.0           (see requirements.txt)
* tabulate>=0.5.0         (see requirements.txt)
* sortedcontainers==2.1.0 (see requirements.txt)


## Credits
This toolkit makes use of three open source software:

[KALDI](http://kaldi-asr.org): The ASR python script uses compute-wer tool from the Kaldi Speech Recognition Toolkit. 
for more info, refer: [Kaldi Tools](http://kaldi-asr.org/doc/tools.html).
If you have used the toolkits' ASR scripts, please consider citing the following paper:
``` Povey, Daniel, Arnab Ghoshal, Gilles Boulianne, Lukas Burget, Ondrej Glembek, Nagendra Goel, Mirko Hannemann et al. *"The Kaldi speech recognition toolkit."* In IEEE 2011 workshop on automatic speech recognition and understanding, no. CONF. IEEE Signal Processing Society, 2011.``` [PDF](https://infoscience.epfl.ch/record/192584/files/Povey_ASRU2011_2011.pdf)

[DSCORE](https://github.com/nryant/dscore): The DIARIZATION python script uses the 'dscore' toolkit developed by Neville Ryant for generating DER scores. for more info, refer: [Dihard Challenge](https://coml.lscp.ens.fr/dihard/index.html)
If you have used the toolkits' SD scripts, please consider citing the following paper:
```Ryant, Neville, Kenneth Church, Christopher Cieri, Alejandrina Cristia, Jun Du, Sriram Ganapathy, and Mark Liberman. "First DIHARD challenge evaluation plan." 2018, tech. Rep. (2018).``` [PDF](https://coml.lscp.ens.fr/dihard/2018/docs/first_dihard_eval_plan_v1.3.pdf)

[NIST openSAT](https://www.nist.gov/itl/iad/mig/opensat) This script uses scoreFile_SAD.pl developed by NIST. for more info, refer: [OpenSAT Evaluation](https://www.nist.gov/itl/iad/mig/nist-open-speech-activity-detection-evaluation)
```Byers, Fred, Fred Byers, and Omid Sadjadi. 2017 Pilot Open Speech Analytic Technologies Evaluation (2017 NIST Pilot OpenSAT): Post Evaluation Summary. US Department of Commerce, National Institute of Standards and Technology, 2019.``` [PDF](https://nvlpubs.nist.gov/nistpubs/ir/2019/NIST.IR.8242.pdf)


## Folder Structure
```
    ./FS02_Scoring_Toolkit 
  ├── egs                             -----------      (Example test files to run scripts on)
  │   ├── ref_gt                      -----------      (Example ground truth samples)
  │   │   ├── ASR
  │   │   │   ├── ASR_track1
  │   │   │   └── ASR_track2
  │   │   ├── SAD
  │   │   ├── SD
  │   │   │   ├── RTTM
  │   │   │   └── UEM
  │   │   └── SID
  │   └── sys_results                 -----------      (Example system output samples)
  │       ├── ASR
  │       │   ├── ASR_track1
  │       │   └── ASR_track2
  │       ├── SAD
  │       ├── SD
  │       └── SID
  ├── LICENSE                         -----------      (FS02_Scoring_Toolkit License)
  ├── README.md                       -----------      (This file)
  ├── requirements.txt                -----------      (Setup file)
  ├── scripts
  │   ├── cfg_path.sh
  │   ├── scoreFS02_ASR.sh
  │   ├── scoreFS02_SAD.sh
  │   ├── scoreFS02_SD.sh
  │   └── scoreFS02_SID.sh
  ├── scutils
  │   ├── dscore                       -----------      (Diarization Toolkit)
  │   │   ├── LICENSE                  -----------      (Diarization Toolkit License)
  │   │   ├── scorelib
  │   │   ├── score.py
  │   │   └── validate_rttm.py
  │   ├── fs02utils.py
  │   ├── scoreFile_SAD.pl
  │   ├── scoreFS02ASR.py
  │   ├── scoreFS02SAD.py
  │   ├── scoreFS02SD.py
  │   └── scoreFS02SID.py
  └── submission_packet                ------------    (for FS Challenge Participants)
      ├── crss@utdallas.edu_ASR_track2_Submission_1
      │   ├── Dev
      │   │   └── Dev_Readme
      │   ├── Eval
      │   │   └── Eval_Readme
      │   └── FS02_System_Description_eg.pdf
      ├── crss@utdallas.edu_ASR_track2_Submission_1.tar.gz
      └── Evaluation_and_Submission_Rules.txt
```


## Usage
The toolkit can be used through bash shell scripts provided in ```./scripts/``` directory. 
```
  bash ./scripts/scoreFS02_<task_name>.sh <ref_path> <hyp_path> <out_path>

      ref_path: Reference (Ground Truth) Path   (Directory Path or File Path depending on the Task and track)
      hyp_path: Hypothesis (System Output) Path (Directory Path or File Path depending on the Task and track)
      out_path: File Path to write scores
```
   
  **REQUIRED** Before running any script: Configuring the ```./scripts/cfg_path.sh``` script.
      Variables required to be set by users: 
      ```kaldi_path``` and ```track_num``` (for running ASR track 1 and 2 scripts only)
      ```python_path``` : path to the desired python with the packages mentioned in [Requirements](#requirements).
      Users can leave this variable as-is, if running scripts through a virtual environment (Tested with conda and venv).
      USAGE: ```pip install -r requirements.txt```

  Optional: variables users can set (to evaluate system performance over different parameters): 
  ```sad_collar``` , ```sd_collar``` ,  and ```topN_eval```. 

**For more details on the usage, please check the individual shell scripts.** 

Additional log files will be automatically generated, and the log path will be displayed on the terminal.
Users can also use the python scripts directly from ```./scutils/scoreFS02<task-name>.py```
(Usage and Documentation provided through shell scripts).


## References
If you have used the toolkit or a part of the toolkit, please consider citing the following papers:

Hansen, J. H., Aditya Joglekar, M. Chandra Shekhar, Vinay Kothapally, Chengzhu Yu, Lakshmish Kaushik, and Abhijeet Sangwan. "The 2019 inaugural fearless steps challenge: A giant leap for naturalistic audio." In proc. Interspeech, vol. 2019. 2019. [PDF](https://www.isca-speech.org/archive/Interspeech_2019/pdfs/2301.pdf)
```
    @inproceedings{Hansen2018,
      author={John H.L. Hansen and Abhijeet Sangwan and Aditya Joglekar and Ahmet E. Bulut and Lakshmish Kaushik and Chengzhu Yu},
      title={Fearless Steps: Apollo-11 Corpus Advancements for Speech Technologies from Earth to the Moon},
      year=2018,
      booktitle={Proc. Interspeech 2018},
      pages={2758--2762},
      doi={10.21437/Interspeech.2018-1942},
      url={http://dx.doi.org/10.21437/Interspeech.2018-1942}
    }
```

Hansen, John HL, Abhijeet Sangwan, Aditya Joglekar, Ahmet Emin Bulut, Lakshmish Kaushik, and Chengzhu Yu. "Fearless Steps: Apollo-11 Corpus Advancements for Speech Technologies from Earth to the Moon." In Interspeech, pp. 2758-2762. 2018. [PDF](https://www.isca-speech.org/archive/Interspeech_2018/pdfs/1942.pdf)
```
    @inproceedings{Hansen2019,
      author={John H.L. Hansen and Aditya Joglekar and Meena Chandra Shekhar and Vinay Kothapally and Chengzhu Yu and Lakshmish Kaushik and Abhijeet Sangwan},
      title={{The 2019 Inaugural Fearless Steps Challenge: A Giant Leap for Naturalistic Audio}},
      year=2019,
      booktitle={Proc. Interspeech 2019},
      pages={1851--1855},
      doi={10.21437/Interspeech.2019-2301},
      url={http://dx.doi.org/10.21437/Interspeech.2019-2301}
    }
```


## Author
  * Aditya Joglekar, CRSS, UT-Dallas, 2020
  
  please contact FearlessSteps@utdallas.edu, for any questions regarding this repository.


## License
This toolkit is licensed under a Creative Commons Attribution-ShareAlike 4.0 International License. See  LICENSE for more details


## Acknowledgements
This project was supported in part by  AFRL  under  contractFA8750-15-1-0205, NSF-CISE Project 1219130, and partially by the University of Texas at Dallas from the Distinguished University Chair in Telecommunications Engineering held by J.H. L. Hansen. We would also like to thank Tatiana Korelsky and the National Science Foundation (NSF) for their support on this scientific and historical project.
