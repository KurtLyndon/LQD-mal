# LQDTU-23
# **Dataset description**

## **1. Pre-processing Dataset (Raw dataset)**

Download Links:

- Pre-Part-012:   <https://drive.google.com/drive/folders/1cGpI4aUXohR6IujEk3ZZvenHlE_cv-GX>
- Pre-Part-345:   <https://drive.google.com/drive/folders/1v9TdEmptdn7wqkfOYMmdc4FxSNYEG-8H>
- Pre-Part-67:    <https://drive.google.com/drive/folders/1l-qVoqjQSmumoW3Q6LjwUtfLp-FDGKj_> 
    
### 1.1. Benign 

- The total number of benign files collected is **16756** files, all of which are files with Portable Executable (PE) format or have extension files belonging to the PE file group. 
- Collected from the ***"C:/Windows"*** folder of computers with Windows 10 Pro operating system, version 22H2, collected on April 30, 2023. 
- All files that have been pre-processed and renamed to their MD5 code, some of which are similar to MD5 will be marked and named starting with "dup_" + timestamps in the prefix of the name. These files are identified as having the same content but different names or they simply have the same MD5 code.
- The preprocessed (raw) Benign dataset is located in the folder ***“data/2_pre-processing/vkl-benign”*** divided into two parts train and test, in the ratio 7:3 by the splitfolders library with seed parameter is 2501. 
- Each test or train part will contain two directories: 
    - **PE-format**: files with header format according to the structure of PE file. Inside that files have been classified into subfolders with the same folder names as their extension files, including: ***dll, exe, mui, mun, sys, winmd*** and the rest are placed in the others folder with the volumn of each type is not high.
    - **PE-extension**: are files with file extensions belonging to the PE file group but without the header format according to the PE file structure. Inside that files have been classified into subfolders with the same folder names as their extension files, including: ***dll, exe, js, mui, sys, ps1** and the rest are placed in the others folder with the volumn of each of which is not high.
    
### 1.2. Malware 
- The source is VirusShare.com (https://virusshare.com/): 
    - Derived from the **VirusShare_00468.zip** suite, with a total size of **52.63 GB**, containing **65536 malicious files**, updated on 30th April 2023. Name each file in the format "VirusShare_" + its MD5 code. 
	- We filtered and extracted **27533 files** belonging to the group with the format PE file. 
	- We submitted those files to VirusTotal.com (https://www.virustotal.com) to scan and get the scan results of VirusTotal to classify malware as well as base for division. train set, test later. 
	- Scan results of each file are saved in the folder ***"data/1_information/pre-processing/malware-virustotal-reports"*** in the format:
	- File name: “vt-report_” + name of malicious file 
	- Structure:
        >	    ++++++++++++++++++++++++++++++++ 
        >	    File: file name 
        >	    Scan ID: scan id 
        >	    Scan Message: scan return message 
        >	    Total: number of engines used 
        >	    Possitive: number of engines returning positive results 
        >	    --------------------------- 
        >	    Engine: engine name 
        >	    Detected: if True, then engine detects this is malicious code, False does not 
        >	    Result: the result message of the engine 
        >	    Version: version of the engine at the time of scan 
        >	    Update: when the engine was last updated 
        >	    ----------
	    Like that in turn are the results returned by each engine. 
	- The aggregate result of the number of positive detections of each engine is stored in the file ***"data/1_information/pre-processing/FileReportSumary.txt"*** with the basic format: 
	    * The first line is the number of engines used . 
	    * The following line pairs are the name of the engine and the number of positives detected by that engine. A total of 83 engines are used by VirusTotal, the top 5 most detected engines are ***Fortinet (26873 cases), FireEye (26290 cases), Cynet (25943 cases), APEX (25851 cases) and SentinelOne (25760 cases)***.
- We proceed to classify malicious code based on the results returned by the engines. We have compiled and exported the excel and csv file “malware-virustotal-reports-summary” in the folder “data/1_information/pre-processing/” 
    - In the above file, they describe a dataframe of **97** columns (not counting) index) as follows: 
        - ***name***: name of file 
        - ***status***: will have one of these values below: 
            - ***known***: if at least one engine (including ClamAV) detected positive. 
            - ***unknown-clamav***: if at least one engine (in which ClamAV is not included) detected positive. 
            - ***unknown***: if no engine detects positive. 
        - ***majority detected type***: the type of malware that engines identify the most.
        - The remaining **94** columns carry the value of malicious or undetectable code of the engines. 
    - We found that the majority of malicious code was identified by the engines as trojans, viruses and worms in which the highest number were trojans, so we divided them into main groups: **trojans, viruses, worms and others**.
- In addition, we build a training and test set based on the following criteria: 
    - Files are confirmed by VirusShare to be malicious but not detected by any engine on VirusTotal, we put them in the ***"unknown"*** set and make them a test set. 
    - Files that the ClamAV engine cannot detect, we put them in the ***"unknown-clamav"*** set and define them as a test set. However, because of the large number, we only took enough **3000 files** to use as a test set. 
    - The files that one or more engines (including ClamAV) can detect, we put them in the ***"known"*** set and then use the splitfolders library to divide them into two parts, train and test with a ratio of 7:3 with parameters. seed number is 2501.
- Summarize a total of **18122 malicious files** (after filtering the PE file and removing the number in the unknown-clamav section): 
    - Train set: includes **10488 files** in the directory ***"data/2_pre-processing/vkl-malware/train/known"***, including 4 groups of malwares: 
        - ***trojan***: 9830 files. 
        - ***viruses***: 421 files. 
        - ***worms***: 230 files. 
        - ***others***: 7 files. 
    - Test set: includes **7634 files** in the directory ***"data/2_pre-processing/vkl-malware/test"***, divided into 3 folders: 
        - ***known***: 4498 files, which are divided into folders classifying malware strains similar train set 
            - ***trojan***: 4213 files. 
            - ***virus***: 181 files. 
            - ***worms***: 100 files. 
            - ***others***: 4 files. 
        - ***unknown***: 136 files
        - ***unknown-clamav***: 3000 files, which are divided into folders that classify malware strains similar to train 
            - ***trojan***: 2150 files 
            - ***virus***: 575 files
            - ***worm***: 13 files 
            - ***others***: 262 files 
- Summarize the number of malware and benign and the ratio of training and test sets stored in the file: 
***"F://Caohoc34A/data/1_information/pre-processing/spliting-summary.xlsx"***
  
## **2. Processed Dataset**

## **3. Experiment & Evaluation**
