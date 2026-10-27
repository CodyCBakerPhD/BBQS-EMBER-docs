# Information Needed to Inform Standard(s) Used

The following information is used to determine best practices and what standard(s) to use for your data:

- **Modalities**: types of data streams (e.g., EEG, kinematic, EMG, audio, video, annotations)
- **Metadata**: critical information a secondary user would need to interpret values correctly and have sufficient context for the experiment (e.g., sampling rates, channel identity, stimulus timing, events)
- **Data Structure**: format of the data (e.g., do you have data split into different sections, are they continuous with each other, how does this structure connect to the greater context of the experiment, how are different experimental sessions represented)
- **Data Tier**: rawness of data you are sharing (e.g., straight off the device, featurized)

There can also be many nuances within the data that should be captured:

- **Uniquely formatted data**: example: spreadsheets of detailed notes relating to certain experiment sessions
- **Exceptions**: some recording sessions were not good, some channels were bad, some modalities were not recorded for a particular session
- **Long chronic data**: does it make sense to split up the chronic data by hour, day, weeks? 

Many of these above nuances can impact the logistical details of how data is packaged into a standard. The standardization plan and packaging strategy suited for your data may be informed by how you actually analyze the original data. If your analysis script uses data that has been curated into a single .mat file or .h5 for instance, NWB could be a natural reflection of that. In contrast, if your analysis script pulls in data stored in modality-separated folders, BIDS may fit better. 

You can see an example of reproducing analysis (figures) at [Example Standardization and Reproducibility](../example-standardization-and-reproducibility/Suthana_Zenodo.md) using data that was originally in .mat files and converted into NWB.

???+ info "Relevant Standards"
    Before formatting your dataset, familiarize yourself with the standards supported by the BBQS Program and the EMBER archive (defined by the BBQS DCAIC Data Standards Working Group) using the [BBQS standardization document](ttps://docs.google.com/document/d/1vIJ01La9G76FfGywS3IbG4o1GR4qquS31MoJ2B4_4os/edit?usp=sharing).


    A quick summary relevant to common data modalities is outlined below:

    ??? question "BIDS: Brain Imaging Data Structure"
        - [BIDS specification](https://bids.neuroimaging.io/): Defines folder structure, file naming, file types and metadata conventions for different data types
        - BIDS extensions:
            - [iEEG/EEG](https://bids-specification.readthedocs.io/en/stable/modality-specific-files/intracranial-electroencephalography.html)
            - [MEG Data](https://bids-specification.readthedocs.io/en/stable/modality-specific-files/magnetoencephalography.html#meg-recording-data)
            - [BEP 032 (microelectrode extension)](https://bids.neuroimaging.io/extensions/beps/bep_032.html), used for depth electrodes, microelectrode arrays, and spike/LFP recordings
            - [BEP 047 (motion, audio, video)](https://bids.neuroimaging.io/extensions/beps/bep_047.html), used for video recordings (e.g., behavior, eye tracking), audio recordings (speech, environment), and motion capture
    ??? question "NWB: Neurodata Without Borders" 
        - [NWB specification](https://www.nwb.org/), well-suited for spike times, LFP, Rich electrophysiology metadata,general time series, all-in-one file approach, compatible with several [analysis and visualization tools]().
        - [NWB in Python (`pynwb`)]()
        - [NWB in MATLAB (`matnwb`)]()
        - [NWB extensions](https://nwb-extensions.github.io):
            - [pose estimation (`ndx-pose`)]()
            - [multiple subjects interacting together (`ndx-multisubjects`)](https://pypi.org/project/ndx-multisubjects/)
            - [audio (`ndx-sound`)]()
        - [streamlined converters in Python (`neuroconv`)]()
    !!! warning "Important"
        If using NWB files, we recommend the files should still be organized within a BIDS structure.


# Data Standardization Recommendation for Animal Data and De-identified Human Data: EMBER-DANDI


## Animal Data

COMING SOON
### Example 1 - Audio, Video, and pose estimation (SLEAP) in NWB
<!-- 
Let's suppose the original data is a single animal in cage with one camera and two microphones:

```
myExperimentSession/


```


Each of the audio and video files in the folder1 in myExperiment hold data that is continuous in time with the files of folder2, which is continuous with folder3 and so and and so forth. Altogether, folders 1 - 5 constitute a single recording session. This means that all the data should be contained inside a single NWB file.

To make it easier for a secondary user to parse the data, then, we will concatenate all video files together, all audio files together, and all pose data together within the single NWB file. 

The resulting NWB structure then looks like:

```
```
 -->

### Example 2 - Audio and Video in BIDS, and pose estimation (SLEAP) in NWB

<!-- Now let's suppose we want to instead use BIDS, which would allow us to maintain the original way we had split up the experiment session. We can also keep the original data formats for audio and video files. 

The BIDS layout would look like: 

```
dataset/
    sub-01/
        ses-01/
            beh/
               audio-part1.wav
               video-part1.mp4 
                
               
```
-->

### Example 3 - Multiple animals interacting


## De-identified Human Data
COMING SOON
### Example 1 - iEEG and motion in NWB

### Example 2 - iEEG and motion in hybrid BIDS/NWB






# Data Standardization Recommendation for PHI/PII Human Data: EMBER-Vault

This guide walks you through a suggested procedure on how to prepare your dataset for upload using the BIDS standard, with support for common data types such as intracranial recordings, audio/video, and behavioral data, and optional use of NWB files.

This covers:

<!-- 1. [The relevant standards](#1-relevant-data-standards) -->


1. [The standardization process and examples](#1-standardization-workflow)
2. [BIDS Standard](#2-bids-standard)
3. [Audio/Video](#3-audio-and-video-bep047)
4. [Validation](#4-validation-required-before-upload)
5. [Common Pitfalls](#5-common-pitfalls)
6. [Recommended Workflow](#6-recommended-workflow-step-by-step)

<!-- ## 1. Relevant Data Standards
Before formatting your dataset, familiarize yourself with the standards supported by the BBQS Program and the EMBER archive (defined by the BBQS DCAIC Data Standards Working Group):

<https://docs.google.com/document/d/1vIJ01La9G76FfGywS3IbG4o1GR4qquS31MoJ2B4_4os/edit?usp=sharing>

A quick summary relevant to sensitive, multimodal human datasets (such as audio/video of participants) is outlined below.

### Core BIDS

- BIDS specification: [https://bids.neuroimaging.io/](https://bids.neuroimaging.io/)
- Defines folder structure, file naming, file types and metadata conventions for different data types

### BIDS Extensions Supported

#### iEEG / EEG

<https://bids-specification.readthedocs.io/en/stable/modality-specific-files/intracranial-electroencephalography.html>

#### MEG Data

<https://bids-specification.readthedocs.io/en/stable/modality-specific-files/magnetoencephalography.html#meg-recording-data>

#### BEP032 (microelectrode extensions)

[https://bids.neuroimaging.io/extensions/beps/bep_032.html](https://bids.neuroimaging.io/extensions/beps/bep_032.html)

Use for:

- Depth electrodes
- Microelectrode arrays
- Spike/LFP recordings

#### Audio / Video / Behavioral

##### BEP047 (motion, audio, video)

[https://bids.neuroimaging.io/extensions/beps/bep_047.html](https://bids.neuroimaging.io/extensions/beps/bep_047.html)

Use for:

- Video recordings (e.g., behavior, eye tracking)
- Audio recordings (speech, environment)
- Motion capture

##### BEP044 (Stimuli)

<https://bids.neuroimaging.io/extensions/beps/bep_044.html>

Use for:

- Stimuli presented to participants (images, audio, video, text)
- Stimulus metadata and provenance

### NWB (Neurodata Without Borders)

[https://www.nwb.org/](https://www.nwb.org/)

Recommended for:

- Spike times
- LFP
- Rich electrophysiology metadata
- General time series

!!! info "Important"
    If using NWB files, we recommend the files should still be organized within a BIDS structure.



NWB extensions can be found at [https://nwb-extensions.github.io](https://nwb-extensions.github.io)

For multi-subject data (multiple subjects interacting, recorded at the same time), refer to [`ndx-multisubjects`](https://pypi.org/project/ndx-multisubjects/) -->


---
## 1. Standardization Workflow

Preparing your dataset involves three main steps:

### Step 1: Organize Files into BIDS Structure

- Create folders and filenames according to BIDS rules
- Convert to allowable filetypes if needed

### Step 2: Add Metadata

- JSON sidecars
- TSV files (events, channels, electrodes, etc.)
- Advise to use LinkML to create/describe their metadata. For advanced: point to complete systems like from Juelich
- Advise to use established lab notebook systems to formalize

### Step 3: Validate Locally

- Use the dandi-cli (which used the NWB inspector, BIDS Validator (<https://bids.neuroimaging.io/tools/validator.html>) etc) before uploading.
- Please see the instructions here: <https://github.com/dandi/dandi-cli>

---
## 2. BIDS Standard 

### 2.1. Folder Structure (Core BIDS Template)

BIDS specifies a particular set of folders and file names to organize data:

<https://bids.neuroimaging.io/getting_started/folders_and_files/index.html>

More examples will be made available at:

<https://github.com/brain-bbqs>

Here is a minimal multimodal dataset layout as an example, including iEEG in NWB formats and audio and video files:

```text
my_dataset/
├── dataset_description.json
├── participants.tsv
├── participants.json
├── sub-01/
│   ├── ses-01/
│   │   ├── ieeg/
│   │   │   ├── sub-01_ses-01_task-rest_ieeg.nwb
│   │   │   ├── sub-01_ses-01_task-rest_ieeg.json
│   │   │   ├── sub-01_ses-01_task-rest_channels.tsv
│   │   │   ├── sub-01_ses-01_task-rest_electrodes.tsv
│   │   │   └── sub-01_ses-01_task-rest_events.tsv
│   │   │
│   │   ├── beh/
│   │   │   ├── sub-01_ses-01_task-rest_beh.tsv
│   │   │   └── sub-01_ses-01_task-rest_beh.json
│   │   │
│   │   ├── video/
│   │   │   ├── sub-01_ses-01_task-rest_video.mp4
│   │   │   └── sub-01_ses-01_task-rest_video.json
│   │   │
│   │   └── audio/
│   │       ├── sub-01_ses-01_task-rest_audio.wav
│   │       └── sub-01_ses-01_task-rest_audio.json
```

---

### 2.2. BIDS File Naming Rules

As you go, please be sure to refer to the BIDS examples: https://github.com/bids-standard/bids-examples 

BIDS uses structured filenames:

```text
sub-<participant>[_ses-<session>]_task-<task>[_modality]
```

Examples:

- `sub-01_ses-01_task-rest_ieeg.nwb`
- `sub-01_ses-01_task-speaking_audio.wav`
- `sub-01_ses-01_task-walk_video.mp4`

---

### 2.3. Required Metadata Files for BIDS


#### 2.3.1 dataset_description.json

```json
{
  "Name": "Example Multimodal Dataset",
  "BIDSVersion": "1.9.0",
  "DatasetType": "raw",
  "Authors": ["Jane Doe", "John Smith"]
}
```

#### 2.3.2 participants.tsv

```tsv
participant_id	age	sex
sub-01	29	M
sub-02	34	F
```

#### 2.3.3 iEEG Metadata

##### channels.tsv

```tsv
name	type	units	sampling_frequency
chan01	microelectrode	uV	30000
```

##### electrodes.tsv

```tsv
name	x	y	z
elec01	12.3	-45.2	30.1
```

#### 2.3.4 events.tsv

```tsv
onset	duration	trial_type
0.0	1.0	stimulus
2.0	1.5	response
```

#### 2.3.5 Sidecar JSON Example

For an iEEG file:

```json
{
  "SamplingFrequency": 30000,
  "PowerLineFrequency": 60,
  "iEEGReference": "common average",
  "TaskName": "rest"
}
```

---

### 2.4. Using NWB within BIDS

If using NWB files within your project, they can be used to represent key time series data within BIDS, such as iEEG.

#### Recommended approach

- Store electrophysiology data as `.nwb` files
- Keep behavioral, events, and metadata in BIDS sidecars
- Organize the dataset with NWB files within the BIDS structure, getting the best of both worlds

Example:

```text
sub-01_ses-01_task-rest_ieeg.nwb
sub-01_ses-01_task-rest_events.tsv
sub-01_ses-01_task-rest_ieeg.json
```

When combining these standards, our key recommendations are:

- NWB = data container
- BIDS = dataset organization + metadata standard

---

## 3. Audio and Video (BEP047)

Supported formats:

- Video: `mp4`, `avi`
- Audio: `wav`

Example sidecar:

```json
{
  "AudioSampleRate": 48000,
  "ManufacturersModelName: "Shure SM7B"
}
```

For video:

```json
{
  "VideoFrameRate": 30,
  "ImageWidth": "1920",
  "ImageHeight": "1080"
}
```

---

## 4. Validation (Required Before Upload)

Data uploaded to either EMBERVault or EMBER-DANDI must be properly standardized.
<!-- ***Can this section be updated to just use the dandi validator?*** -->
### Command Line Validation for BIDS (Recommended)

You can find detailed instructions on use of the BIDS validator here:

<https://bids.neuroimaging.io/tools/validator.html>

IMPORTANT: Keep in mind that some tools such as online validators may send parts of your data outside your local system. It is the job of the Principal Investigator to ensure that all data handling, especially for sensitive human data, is in alignment with existing laws, regulations, and policy, including terms of data sharing as agreed by the human participants through the informed consent process.

<https://bids-validator.readthedocs.io/en/latest/user_guide/command-line.html>

#### Install

(Use the Mac Terminal, Linux Command Line, or Windows Subsystem for Linux)

Using Deno:

<https://docs.deno.com/runtime/getting_started/installation/>

```bash
deno install -ERWN -g -n bids-validator jsr:@bids/validator
```

### Run

```bash
deno run -ERWN jsr:@bids/validator <dataset>
```

When using a BEP standard, you must use the flag `--schema` with the link to the pull request specific to the extension.

#### BEP032

```bash
--schema https://bids-specification--2307.org.readthedocs.build/en/2307/schema.json
```

#### BEP047

```bash
--schema https://bids-specification--2231.org.readthedocs.build/en/2231/schema.json
```


### Expected Output

- No errors → ready to upload
- Warnings → usually acceptable but should be reviewed
- Errors → must fix before upload to EMBER-Vault

!!! warning
    If you are using NWB files at all, you will need to validate those separately using the [DANDI CLI](../user-guide/uploading-data.md#upload-data-to-your-dandiset)

---

## 5. Common Pitfalls

### Incorrect filenames

- Missing `task-` or `sub-` labels

### Missing JSON sidecars

- Especially for iEEG and video/audio

### Inconsistent sampling rates

- Must match metadata

### Time misalignment

- Ensure events align across modalities

If your team is struggling, the EMBER archive team can help. Reach out at `help@emberarchive.org`.

---

## 6. Recommended Workflow (Step-by-Step)

1. Prepare raw data
    - Export from acquisition systems
    - Convert relevant modalities to NWB if appropriate, convert other data types to appropriate BIDS formats

2. Create folder structure

    ```bash
    mkdir -p my_dataset/sub-01/ses-01/ieeg
    ```

3. Rename files to BIDS format

4. Create metadata files
    - `dataset_description.json`
    - `participants.tsv`
    - sidecars

5. Add events + behavioral data

6. Validate BIDS dataset

7. Fix errors

8. Upload to platform