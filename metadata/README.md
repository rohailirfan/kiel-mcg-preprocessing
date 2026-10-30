# Kiel Cardio Database 1.0

## Abstract

The Kiel Cardio Database, which includes magnetocardiography (MCG) measurements of seven healthy male volunteers, is presented in this publication. QuSpin QZFM Gen 1 sensors [1] were used in a magnetically shielded chamber at the University of Kiel. Each subject underwent 25 one-minute measurements, resulting in magnetic field recordings at 200 different positions for each subject. The dataset is intended to facilitate MCG research by making this expensive method [2] accessible to researchers without the necessary equipment. Future versions of the database will expand its utility for cardiac research and arrhythmogenic tissue localization by incorporating electrocardiography (ECG), magnetotomographic imaging (MRI), and annotation data.

![Kiel Cardio Database](images/kcd_overview.jpg)

## Table of Contents

- [Examples](#examples)
- [Background](#background)
- [Methods](#methods)
- [Data Description](#datadesrciption)
- [Usage Notes](#usagenotes)
- [Release Notes](#releasenotes)
- [Acknowledgements](#acknowledgements)
- [Conflicts Of Interest](#conflictsofinterest)
- [Authors](#authors)

## Examples

Below you can find examples of the preprocessed recordings.

![Filtered data – Patient 1, Trial 2, Channel 1](images/patient_1_trial_2_channel_1.png)

![Filtered data – Patient 4, Trial 5, Channel 2](images/patient_4_trial_5_channel_2.png)

![Filtered data – Patient 5, Trial 10, Channel 3](images/patient_5_trial_10_channel_3.png)

![Filtered data – Patient 7, Trial 13, Channel 4](images/patient_7_trial_13_channel_4.png)

## Background

While electrocardiography (ECG) is the most widely used diagnostic tool in cardiology, its magnetic equivalent, magnetocardiography (MCG), has not been widely adopted. One reason for this is the high cost of the necessary measurement setups [2]. Sufficiently sensitive sensors such as superconducting quantum interference devices (SQUIDs), optically pumped magnetometers (OPMs) or magnetoelectric (ME) sensors are expensive to produce. In addition, SQUIDs require constant cooling with liquid helium, which further increases the cost of ownership. In addition, OPMs and most SQUIDs do not operate in the Earth's magnetic field. Therefore, a magnetically shielded chamber is required for successful operation. Magnetoelectric sensors can operate in the Earth's magnetic field but are still in the research stage [3], [4].

At Kiel University we have the necessary equipment to perform MCG measurements. As part of the Collaborative Research Center 1261, we are investigating the non-invasive localization of arrhythmogenic tissue in the human heart. For this purpose, several non-invasive measurement modalities are used, namely ECG, MCG, and MRI. We combine all our data in the Kiel Cardio Database. The most recent version can be found on our website [5]. This first archived version includes MCG measurements from seven healthy volunteers. At the moment there are no other MCG databases available on PhysioNet. We hope that by making the measured data available, we will enable more people who do not have access to the necessary equipment to investigate this measurement modality. We plan to include ECG, MRI and annotation data in future versions of this database.

## Methods

Seven healthy male volunteers aged between 22 to 51 years were recorded in May 2022 for this database. All measurements were conducted in the magnetically shielded chamber at Kiel University's Faculty of Engineering.

The sensor array consists of four QuSpin QZFM Gen 1 optically pumped magnetometers (OPMs) [1] operating in two-axis mode. These sensors are labeled sensor 0, sensor 1, sensor 2, and sensor 3. Refer to the table below for the sequence and measuring directions of these sensors.

| Channel | Sensor | Measure­ment direc­tion |
| ------- | ------ | ----------------------- |
| 0       | 0      | -y                      |
| 1       | 0      | z                       |
| 2       | 1      | -y                      |
| 3       | 1      | z                       |
| 4       | 2      | -y                      |
| 5       | 2      | x                       |
| 6       | 3      | -y                      |
| 7       | 3      | x                       |

Each subject underwent 25 one-minute measurements, referred to below as trials, with different sensor positions, resulting in magnetic field recordings at 200 different positions for each subject. The subject remained lying on the patient bed while an operator moved the sensor array between measurements.

![Sensor](images/kcd_sensors.jpg)

The real-time software KiRAT [6], developed by the Digital Signal Processing and System Theory (DSS) group, was used to control and retrieve data from the sensors.

Two coordinate systems are used to describe the position of the sensor. The first is the global coordinate system, which is referenced to the subject. In this system, the x-axis extends slightly above the shoulders and runs from left to right. The y-axis runs from the back of the subject to the front. The z-axis is aligned with the subject's spine and runs from the feet to the head. The origin of this coordinate system, labeled `(0,0,0)`, is located on the bed at the intersection of the patient's spine and an orthogonal line touching the top of the shoulders.

![Coordinate system](images/coordinate_system.png)

The second coordinate system refers to the sensor array. It has the same axis directions as the first coordinate system and its origin is located at the center of the tip of sensor 0.

All four sensors are initially placed in a fixture, which is then moved between trials. The sensor position information is divided into three parts: the relative position of the sensors in the holder/sensor array, the initial position of the sensor array during the first trial, and the position offset for the subsequent 24 trials. All positions are given in centimeters.

The following table lists the positions of the sensors relative to the center of the
of the sensor array:

| Sensor   | X (cm) | Y (cm) | Z (cm) |
| -------- | ------ | ------ | ------ |
| Sensor 0 | 0      | 0      | 0      |
| Sensor 1 | -3     | 0      | 3      |
| Sensor 2 | -3     | 0      | 0      |
| Sensor 3 | 0      | 0      | 3      |

The initial position of the sensor array in trial 1 is (-11 cm, 17 cm, -14 cm).

The following table shows the offset of the sensor array from the initial position in cm. The y-offset is zero for all trials, the x-offset is shown in the column, and the z-offset is shown in the rows.

| Off­sets | 12 cm | 9 cm | 6 cm | 3 cm | 0 cm |
| -------- | ----- | ---- | ---- | ---- | ---- |
| 12 cm    | 25    | 24   | 23   | 22   | 21   |
| 9 cm     | 20    | 19   | 18   | 17   | 16   |
| 6 cm     | 15    | 14   | 13   | 12   | 11   |
| 3 cm     | 10    | 9    | 8    | 7    | 6    |
| 0 cm     | 5     | 4    | 3    | 2    | 1    |

![Sensor Positions](images/kcd_markings.jpeg)

## Data Description

This database contains time series data in the Waveform Database (WFDB) [7] format. A list of all records can be found in the RECORDS file.
For each trial, the raw data and the preprocessed data are provided. The preprocessing includes scaling by the sensitivity of the OPMs, 100 Hz low pass filtering, 1 Hz high pass filtering, and 50 Hz band stop filtering.
All data files are located in the 'data' directory and are named according to the following scheme 'subject<1-7>\_<preprocessed/raw>\_trial<1-25>'.
The corresponding header files contain the following additional information.

- <year of birth>: the year in which the subject was born.
- <sex>: the sex of the subject.
- <year of recording>: the year the data was recorded.
- <position sensor 0 [cm]>: the xyz position of sensor 0 in relation to the patient in cm.
- <position sensor 1 [cm]>: the xyz position of sensor 1 in relation to the patient in cm.
- <position sensor 2 [cm]>: the xyz position of sensor 2 in relation to the patient in cm.
- <position sensor 3 [cm]>: the xyz position of sensor 3 in relation to the patient in cm.
  A summary of the subject information is provided in subject-info.csv.

## Usage Notes

All data is provided in the widely used WFDB format [7]. For example, to read the data into a Python file, the Python implementation of WFDB [8] can be used.
The data can then be used to investigate various aspects of the magnetocardiographic data. For example, the distribution of the magnetic field at different times over the subject's torso, or the typical shape and frequency components of the MCG signals [9]. Users may also wish to test the applicability of ECG delineation algorithms to MCG data. Although since no annotations are provided as of this release, manual annotation would need to precede quantitative evaluations of such algorithms.

## Release Notes

Version 1.0.0: initial release

## Acknowledgments

This work was supported by the German Research Foundation (Deutsche Forschungsgemeinschaft, DFG) through the Collaborative Research Centre CRC 1261 “Magnetoelectric Sensors: From Composite Materials to Biomagnetic Diagnostics”.

## Conflict Of Interest

The authors declare no conflict of interest.

## References

[1] V. K. Shah and R. T. Wakai, “A compact, high performance atomic magnetometer for biomedical applications,” Phys. Med. Biol., vol. 58, no. 22, pp. 8153–8161, Nov. 2013, doi: 10.1088/0031-9155/58/22/8153.
[2] R. Fenici, D. Brisinda, and A. M. Meloni, “Clinical application of magnetocardiography,” Expert Rev. Mol. Diagn., vol. 5, no. 3, pp. 291–313, May 2005, doi: 10.1586/14737159.5.3.291.
[3] E. Elzenheimer et al., “Quantitative Evaluation for Magnetoelectric Sensor Systems in Biomagnetic Diagnostics,” Sensors, vol. 22, no. 3, p. 1018, Jan. 2022, doi: 10.3390/s22031018.
[4] E. Elzenheimer et al., “Investigation of Converse Magnetoelectric Thin-Film Sensors for Magnetocardiography,” IEEE Sens. J., vol. 23, no. 6, pp. 5660–5669, Mar. 2023, doi: 10.1109/JSEN.2023.3237910.
[5] E. Engelhardt, A. Zaman, N. Frey, and G. Schmidt, “Kiel Cardio Dataset (continuous versoin).” Aug. 07, 2023. [Online]. Available: https://biomagnetic-sensing.de/index.php/data-bases/datasets/kiel-cardio-database
[6] Digital Signal Processing and System Theory Group, “KiRAT - Kiel Real-time Application Toolkit.” [Online]. Available: https://kirat.de/
[7] Moody, George, Pollard, Tom, and Moody, Benjamin, “WFDB Software Package.” PhysioNet. doi: 10.13026/ZZPX-H016.
[8] C. Xie, L. McCullum, A. Johnson, T. Pollard, B. Gow, and B. Moody, “Waveform Database Software Package (WFDB) for Python.” PhysioNet. doi: 10.13026/9NJX-6322.
[9] E. Engelhardt, A. Zaman, E. Elzenheimer, N. Frey, and G. Schmidt, “Towards Analytically Computable Quality Classes for MCG Sensor Systems,” Curr. Dir. Biomed. Eng., vol. 8, no. 2, pp. 691–694, Sep. 2022, doi: 10.1515/cdbme-2022-1176.

## Authors

- [**Erik Engelhardt**](https://orcid.org/0000-0002-7012-7707)
- [**Norbert Frey**](https://www.researchgate.net/profile/Norbert-Frey)
- [**Gerhard Schmidt**](https://orcid.org/0000-0002-6128-4831)
