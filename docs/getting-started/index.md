# Getting Started

## What is EMBER?

EMBER is the Ecosystem for Multi-Modal Brain-behavior Experimentation and Research.

The EMBER Archive enables storage, sharing, and analysis of multimodal neurophysiological and behavioral data, primarily supporting the [Brain Behavior Quantification and Synchronization (BBQS) Program](https://braininitiative.nih.gov/research/systems-neuroscience/brain-behavior-quantification-and-synchronization-program).

## Storage and Analytics Platforms

EMBER has two data storage platforms:


- **EMBER-DANDI**: supports multimodal data from both animals and humans. Human data must not contain any PHI/PII and must be completely deidentified. 
- **EMBER-Vault**: supports HIPAA aligned, restricted human data storage


EMBER is also planning development a cloud-based analysis ecosystem cocnnected to the data storage resources:

- **EMBER-Hearth**: enables analysis pipelines such as spike sorting and pose estimation inference in the cloud through both of these platforms. 


<br>
<figure style="text-align: center;">
  <div style="display: flex; flex-direction: column; gap: 16px; align-items: center;">
    
    <div style="width: 100%;">
      <img src="/assets/emberdandi_vault_hearth.png"
           alt="EMBER-DANDI, EMBER-Vault, EMBER-Hearth"
           style="width: 100%;">
      <p style="font-size: 0.9em; margin-top: 4px;">
        <strong>(A)</strong> EMBER architecture
      </p>
    </div>

    <div style="width: 100%;">
      <img src="/assets/emberdandi_vault_detail.png"
           alt="Details of EMBER-DANDI and EMBER-Vault"
           style="width: 100%;">
      <p style="font-size: 0.9em; margin-top: 4px;">
        <strong>(B)</strong> EMBER-DANDI and EMBER-Vault
      </p>
    </div>

  </div>

  <figcaption style="margin-top: 8px;">
    EMBER-DANDI is the open data archive that can store animal and de-identified human data.
    EMBER-Vault is a HIPAA-compliant storage for identifiable human data, such as raw video and audio.
  </figcaption>
</figure>



## How do I upload data?

1. Create an account for each appropriate storage platform using instructions in the [User Guide](../user-guide/index.md).
2. Standardize your data using community standards [Brain Imaging Data Structure (BIDS)](https://bids-specification.readthedocs.io/en/stable/) and/or [Neurodata without Borders (NWB)](https://nwb-schema.readthedocs.io/en/latest/format.html#type-specifications).
3. Upload the standardized data to the appropriate storage platform(s) using instructions in the [User Guide](../user-guide/index.md).



### Standardizing Data
Data standardization is required for uploading to either of the EMBER storage platforms to enable easy and accessible reuse of data by secondary users who were not involved in the original study that created the data. 

The recommended approach is to organize your dataset using the BIDS schema and convert relevant file contents per session into NWB. 

BIDS broadly describes how to rename files and folders and organize them into a standardized directory. It also requires associated metadata files to help explain the experiment and data.

NWB is a specific file type that can store one or more modalities within per experimental session. Relevant metadata is also stored directly inside the NWB file.

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



### I'm new to these standards - how can I get help? 

More specific instructions for how to standardize data can be found in the [User Guide](../user-guide/index.md). 

You are welcome to consult external sources (websites, LLMs, coding harnesses) to help you standardize your data, but please ensure that converted data has been checked for quality. 

If you are using an AI-based or -assisted workflow, please make sure to exercise due diligence to check for quality/accuracy, understanding that it is ultimately the Principal Investigator's responsibility to make the data truly reusable by other investigators.

If you need help, please reach out to [help@emberarchive.org](mailto:help@emberarchive.org).

<!-- If you are a member of the **BBQS consortium**, you can also reach out to the Data Ingest Task Force by starting a discussion on the [BBQS Helpdesk](https://github.com/orgs/brain-bbqs/discussions) to request a streamlined pathway for uploading and data standardization. -->





