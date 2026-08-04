# EMBER-Vault: Submission of Multimodal Human Data (BIDS + NWB)

EMBER-Vault is the subsystem of the EMBER archive designed to store and control access to sensitive human data, including human health data and identifiable human data. It is suitable for human neural and behavioral experiments with sensitive data, and is not appropriate for open data release or animal data. It is compliant and aligned with relevant standards for PHI/PII data storage.

This guide walks you through a suggested procedure on how to prepare your dataset for upload using the BIDS standard, with support for intracranial recordings, audio/video, and behavioral data, and optional use of NWB files.

This covers:

1. [The relevant standards](#1-relevant-data-standards)
2. [The standardization process and examples](#2-standardization-workflow)
3. [Upload instructions for EMBER-Vault](#11-upload-to-ember-vault-via-globus)

Your team will standardize and validate data client side, then upload to the EMBER-Vault archive using Globus. This resource is under active development, and we anticipate EMBER-Vault will be available soon!

---

# 1. Relevant Data Standards
Before formatting your dataset, familiarize yourself with the standards supported by the BBQS Program and the EMBER archive (defined by the BBQS DCAIC Data Standards Working Group):

[https://docs.google.com/document/d/1vIJ01La9G76FfGywS3IbG4o1GR4qquS31MoJ2B4_4os/edit?usp=sharing]()

A quick summary relevant to sensitive, multimodal human datasets (such as audio/video of participants) is outlined below.

## Core BIDS

- BIDS specification: [https://bids.neuroimaging.io/]()
- Defines folder structure, file naming, file types and metadata conventions for different data types

## BIDS Extensions Supported

### Intracranial / Microelectrode Data

[https://bids-specification.readthedocs.io/en/stable/modality-specific-files/intracranial-electroencephalography.html]()

### MEG Data

[https://bids-specification.readthedocs.io/en/stable/modality-specific-files/magnetoencephalography.html#meg-recording-data]()

### BEP032 (iEEG / microelectrode extensions)

[https://bids.neuroimaging.io/extensions/beps/bep_032.html]()

Use for:

- Depth electrodes
- Microelectrode arrays
- Spike/LFP recordings

### Audio / Video / Behavioral

#### BEP047 (motion, audio, video)

[https://bids.neuroimaging.io/extensions/beps/bep_047.html]()

Use for:

- Video recordings (e.g., behavior, eye tracking)
- Audio recordings (speech, environment)
- Motion capture

## NWB (Neurodata Without Borders)

[https://www.nwb.org/]()

Recommended for:

- Spike times
- LFP
- Rich electrophysiology metadata

**Important:** If using NWB files, we recommend the files should still be organized within a BIDS structure for sensitive human data.

---

# 2. Standardization Workflow

Preparing your dataset involves three main steps:

## Step 1: Organize Files into BIDS Structure

- Create folders and filenames according to BIDS rules
- Convert to allowable filetypes if needed

## Step 2: Add Metadata

- JSON sidecars
- TSV files (events, channels, electrodes, etc.)
- Advise to use LinkML to create/describe their metadata. For advanced: point to complete systems like from Juelich
- Advise to use established lab notebook systems to formalize

## Step 3: Validate Locally

- Use the dandi-cli (which used the NWB inspector, BIDS Validator ([https://bids.neuroimaging.io/tools/validator.html]()) etc) before uploading.
- Please see the instructions here: [https://github.com/dandi/dandi-cli]()

## Step 4: Upload via Globus 
- Please see the instructions here: [Upload to EMBER-Vault via Globus](#11-upload-to-ember-vault-via-globus)

---

# 3. Folder Structure (Core BIDS Template)

BIDS specifies a particular set of folders and file names to organize data:

[https://bids.neuroimaging.io/getting_started/folders_and_files/index.html]()

More examples will be made available at:

[https://github.com/brain-bbqs]()

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

# 4. BIDS File Naming Rules

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

# 5. Required Metadata Files for BIDS


## 5.1 dataset_description.json

```json
{
  "Name": "Example Multimodal Dataset",
  "BIDSVersion": "1.9.0",
  "DatasetType": "raw",
  "Authors": ["Jane Doe", "John Smith"]
}
```

## 5.2 participants.tsv

```tsv
participant_id	age	sex
sub-01	29	M
sub-02	34	F
```

## 5.3 iEEG Metadata (BEP032)

### channels.tsv

```tsv
name	type	units	sampling_frequency
chan01	microelectrode	uV	30000
```

### electrodes.tsv

```tsv
name	x	y	z
elec01	12.3	-45.2	30.1
```

## 5.4 events.tsv

```tsv
onset	duration	trial_type
0.0	1.0	stimulus
2.0	1.5	response
```

## 5.5 Sidecar JSON Example

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

# 6. Using NWB within BIDS

If using NWB files within your project, they can be used to represent key time series data within BIDS, such as iEEG.

## Recommended approach

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

# 7. Human Audio / Video (BEP047)

Supported formats:

- Video: `mp4`, `avi`
- Audio: `wav`

Example sidecar:

```json
{
  "SamplingFrequency": 48000,
  "Microphone": "Shure SM7B"
}
```

For video:

```json
{
  "FrameRate": 30,
  "Resolution": "1920x1080"
}
```

---

# 8. Validation (Required Before Upload)

To use EMBERvault, you must validate your dataset locally. The EMBERvault system is intended to store standardized data for reuse but does not currently format and validate data in-platform.

## Command Line Validation for BIDS (Recommended)

You can find detailed instructions on use of the BIDS validator here:

[https://bids.neuroimaging.io/tools/validator.html]()

We do not recommend using the online data validator for datasets containing PHI/PII.

[https://bids-validator.readthedocs.io/en/latest/user_guide/command-line.html]()

### Install

(Use the Mac Terminal, Linux Command Line, or Windows Subsystem for Linux)

Using Deno:

[https://docs.deno.com/runtime/getting_started/installation/]()

```bash
deno install -ERWN -g -n bids-validator jsr:@bids/validator
```

### Run

```bash
deno run -ERWN jsr:@bids/validator <dataset>
```

When using an extension, you must use the flag `--schema` with the link to the pull request specific to the extension.

### BEP032

```bash
--schema https://bids-specification--2307.org.readthedocs.build/en/2307/schema.json
```

### BEP047

```bash
--schema https://bids-specification--2231.org.readthedocs.build/en/2231/schema.json
```

## Expected Output

- No errors → ready to upload
- Warnings → usually acceptable but should be reviewed
- Errors → must fix before upload to EMBER-Vault

---

# 9. Common Pitfalls

## Incorrect filenames

- Missing `task-` or `sub-` labels

## Missing JSON sidecars

- Especially for iEEG and video/audio

## Inconsistent sampling rates

- Must match metadata

## Time misalignment

- Ensure events align across modalities

If your team is struggling, the EMBER archive team can help. Reach out at `info@emberarchive.org`.

---

# 10. Recommended Workflow (Step-by-Step)

1. Prepare raw data
    - Export from acquisition systems
    - Convert electrophysiology to NWB if appropriate, convert other data types to appropriate BIDS formats

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

---

# 11. Upload to EMBER-Vault via Globus 

## Account Setup
TBD

## Data Upload
Once your user account and project have been set up, you are ready to upload your data to EMBER-Vault!

**1. Log in via Globus & EMBER-Vault**
<ol type="a">
  <li>Login to Globus using your institutional or any other authentication method you use to access the Globus Web UI</li>

  <li>Once you're logged in to Globus, navigate to your collections and ensure the default "Recent Tasks" filter is deactivated
    <img src="/assets/VaultUpload_Fig1_Collections.png" alt="Collections screen in Globus Web UI"/>
  </li>

  <li>Search for "EMBER" and you should find your project's collection located on the <code>Ember-Vault Endpoint</code>
    <img src="/assets/VaultUpload_Fig2_GlobusCollectionSearch.png" alt="Collections ember search results screen in Globus Web UI"/>
  </li>

  <li>Select your project's EMBER Vault collection by clicking on the name. In this case, it is <code>EMBER-Vault YOUR-PROJECT-NAME</code></li> 
  
  <li>This should take you to the collection manager. Here, you should select <code>Open in File Manager</code> as indicated in the figure below
    <img src="/assets/VaultUpload_Fig3_ProjectCollectionManager.png" alt="Project collection manager in Globus Web UI"/>
  </li>

  <li>In the File Manager, you will be met with a warning that you are not authenticated with <code>auth.emberarchive-vault.org</code>. Press the <code>Continue</code> button in order to authenticate with EMBER-Vault
    <img src="/assets/VaultUpload_Fig4_ProjectFileManagerUnauth.png" alt="Project collection file manager in Globus Web UI"/>
  </li>
</ol>


!!! note
    If you have not yet created an account with EMBER-Vault and thus do not possess Keycloak credentials, please refer to the above section titled ``Account Setup``

<ol type="a" start="7">

<li>You will then be redirected to EMBER-Vault's user login page. Here, you will sign in using your EMBER-Vault credentials which were generated during account setup. <br><br>
These should be a username provided to you by admins ending in <code>@auth.emberarchive-vault.org</code> and the password you set up. <br><br>
After this screen you will also be prompted to enter a one-time code from your MFA application
  <img src="/assets/VaultUpload_Fig5_Keycloak.png" alt="Account login screen in EMBER-Vault Keycloak"/>
</li>

</ol>

**2. Select File/Folder to Upload**
<ol type="a">

  <li>Once you have signed in to Globus and EMBER-Vault, you can now access your EMBER-Vault project via the file manager. To begin uploading your data, click on the <code>Upload</code> button circled in the figure below.
    <img src="/assets/VaultUpload_Fig6_CollectionFileManager.png" alt="File manager for EMBER-Vault project collection in Globus web UI"/>
  </li>

  <li>Clicking the <code>Upload</code> button will bring up an option to select files or a folder to upload to the collection from your local machine. For this demonstration, we will be uploading a folder
    <img src="/assets/VaultUpload_Fig7_CollectionUpload.png" alt="File manager file/folder upload for EMBER-Vault project collection in Globus web UI"/>
  </li>

  <li>You will be prompted to select a folder in your local system to upload to EMBER-Vault via Globus. Select your desired folder and click <code>Upload</code>
    <img src="/assets/VaultUpload_Fig8_SelectFolder.png" alt="File manager file/folder select for upload to EMBER-Vault project collection in Globus web UI"/>
  </li>

  <li>You will then be prompted to confirm your upload, to do so select <code>Upload</code>
    <img src="/assets/VaultUpload_Fig9_ConfirmUpload.png" alt="File manager confirm upload to EMBER-Vault project collection in Globus web UI"/>
  </li>

  <li>You should then see in real time the upload progress of your file or each file in the folder you selected. Congratulations! You have successfully uploaded your data to EMBER-Vault :) 
    <img src="/assets/VaultUpload_Fig10_UploadSuccess.png" alt="File manager successful upload to EMBER-Vault project collection in Globus web UI"/>
  </li>

</ol>
