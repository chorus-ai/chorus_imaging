# CHoRUS Imaging Documentation

## Overview

This tool is designed to de-identify DICOM files by removing or patients health information (PHI) to ensure patient privacy. It is built using Python and utilizes the `pydicom` library for handling DICOM files. It also features multiprocessing to speed up the de-identification of large batches of files.

## Features

- Remove PHI identifiers from DICOM tags.
- Replace identifiers with pseudonyms or blanks.
- Option to retain certain tags based on user preference.
- Batch processing of multiple DICOM files.
- Logging of de-identification process.
- Multiprocessing for increased performance.
- Pixel redaction to PHI information burned in images.
- Identify Secondary Capture Images.
- Mapping DICOM image elements to the OMOP data model for observational research.


## Prerequisites

```bash
pip install pylibjpeg pylibjpeg-libjpeg pydicom python-gdcm
sudo apt-get install tesseract-ocr
python -m spacy download en_core_web_lg
```


## Python Code Snippets

### Remove Patient Identifiers Function

```python
def remove_identifiers(dicom_file):
    tags_to_remove = [
        'PatientID',
        'PatientName', 
        ...
    ]

    for tag in tags_to_remove:
        if tag in dicom_file:
            del dicom_file[tag]
```

### Replace Identifiers with Pseudonyms Function

```python
def replace_identifiers_with_pseudonyms(dicom_file):
    new_patient_name = "ERADICATED"
    new_manufacturer = "ERADICATED"
    new_institution_address = "ERADICATED"

    dicom_file.PatientName = new_patient_name
    dicom_file.Manufacturer = new_manufacturer
    dicom_file.InstitutionAddress = new_institution_address

```

    https://github.com/chorus-ai/deidentification_tools/blob/main/dicom_header_anonymized.ipynb


### Retain Certain Tags Function

```python
def retain_certain_tags(dicom_file, tags_to_retain):
    all_tags = dicom_file.dir()
    for tag in all_tags:
        if tag not in tags_to_retain and tag in dicom_file:
            del dicom_file[tag]
```

### Batch processing of multiple DICOM files

```python
import os
from pydicom.filereader import dcmread
from pydicom.filewriter import dcmwrite

def batch_process_directory(input_directory, output_directory):
    for filename in os.listdir(input_directory):
        if filename.endswith(".dcm"):
            input_file = os.path.join(input_directory, filename)
            output_file = os.path.join(output_directory, filename)
            dicom_data = dcmread(input_file)
            remove_identifiers(dicom_data)
            replace_identifiers_with_pseudonyms(dicom_data)
            dcmwrite(output_file, dicom_data)
            print(f"Processed file: {filename}")
```

### Logging Function

```python
def log_deidentification(input_file, output_file):
    logging.info(f"De-identified file: {input_file} -> {output_file}")
```

### Multiprocessing with batch files

```python
import os
from multiprocessing import Pool
from pydicom.filereader import dcmread
from pydicom.filewriter import dcmwrite
import logging

def deidentify_file(file_tuple):
    input_file, output_file = file_tuple
    try:
        dicom_file = dcmread(input_file)
        remove_identifiers(dicom_file)
        replace_identifiers_with_pseudonyms(dicom_file)
        dcmwrite(output_file, dicom_file)
        log_deidentification(input_file, output_file)
    except Exception as e:
        logging.error(f"Failed to process file: {input_file} due to {e}")

def batch_process_directory_with_multiprocessing(input_directory, output_directory, num_processes):
    file_tuples = []
    for filename in os.listdir(input_directory):
        if filename.endswith(".dcm"):
            input_file = os.path.join(input_directory, filename)
            output_file = os.path.join(output_directory, filename)
            file_tuples.append((input_file, output_file))

    with Pool(processes=num_processes) as pool:
        pool.map(deidentify_file, file_tuples)
```

### Pixel Redaction Demo

    https://github.com/chorus-ai/deidentification_tools/blob/main/deid_burnt_in_text_in_DICOM.ipynb


### Identify Secondary Capture Images 🕵️ 

- **Conversion Type Check:**  
  Look for the DICOM tag `(0008,0064)`. If the Conversion Type is set to `WSD` (Workstation), the image is likely a secondary capture.

- **SOP Class UID Check:**  
  Examine the DICOM tag `(0008,0016)` for the SOP Class UID. If it corresponds to the UID for Secondary Capture Image Storage, the image is identified as a secondary capture.


### Mapping dicom elements to OMOP 

In progress...


## Reporting Issues

Before submitting a pull request, please ensure you've searched for existing issues that may already address the problem you're encountering. If an issue doesn't already exist, you can create a new one:

1. Navigate to the repository's "Issues" tab.
2. Click on "New Issue".
3. Provide a descriptive title for the issue.
4. Fill in the template, detailing the problem, steps to reproduce, expected outcome, and any additional information that might help.
5. Attach any relevant screenshots or logs.
6. Submit the issue.


## Contact

To request access to contribution or for further queries: 

[zlyu@mgh.harvard.edu](mailto:zlyu@mgh.harvard.edu)

[dbold@emory.edu](mailto:dbold@emory.edu)


## License

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT). See the [LICENSE](LICENSE) file for more details.


## Acknowledgements


in progress...


