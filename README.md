# Predicting Fall Risk Based on a Validated Balance Scale

This is an overview of my MSc thesis in the field of Machine Learning **(code is not published)**, under the supervision of Prof Ilan Shimshoni and Prof. Hagit Hel-Or, in which our task was to predict the BBS balance scale of adults, and thus estimate their chance of losing balance and falling in regular day-to-day tasks.

I have presented the paper in an international online CVPR workshop on June 2020. The full paper can be found at https://openaccess.thecvf.com/content_CVPRW_2020/papers/w19/Masalha_Predicting_Fall_Probability_Based_on_a_Validated_Balance_Scale_CVPRW_2020_paper.pdf, and my thesis can be found here: https://eu01.alma.exlibrisgroup.com/discovery/delivery/972HAI_MAIN:AlmaGeneralView/12230844110002791.


## Background

According to Cameron et al., 2005, fatal fall rates among adults increase exponentially as people grow older. Moreover, according to Burns E, Kakara R., the number of deaths from falls among adults above the age of 65 keeps increasing over the years. This is where the BBS comes into place; a physical test used to evaluate stability and fall risk among adults. It includes 14 tasks scored from 0 to 4 according to the subject's performance, and are summed to a single score totalling a maximum of 56. The importance and reliability of the BBS test are supported by the Shumway-Cook model, which states that a BBS score of 36 or less indicates nearly a 100% chance of falling within 6 months.

| | | |
|-|-|-|
| ![chair transfer task](https://user-images.githubusercontent.com/78589884/120074237-97ddd800-c0a4-11eb-982c-787634a1bc51.png)  | ![chair transfer task](https://user-images.githubusercontent.com/78589884/120074247-a62bf400-c0a4-11eb-92e7-c0cff65bd56c.png)  | ![sitting unsupported task](https://user-images.githubusercontent.com/78589884/120074246-a4fac700-c0a4-11eb-81e5-14a8770063c0.png)  |
| ![shoe pick up task](https://user-images.githubusercontent.com/78589884/120074249-a75d2100-c0a4-11eb-8cf5-6499100eff84.png)  | ![reach forward task](https://user-images.githubusercontent.com/78589884/120074252-a88e4e00-c0a4-11eb-9b2b-fbb2ca4bfb6c.png)  | ![step on a stool task](https://user-images.githubusercontent.com/78589884/120074253-a926e480-c0a4-11eb-931b-a65329eb1522.png)  |

_pics source: https://www.youtube.com/watch?v=HBKXu9fHnuo_

## Automatic Assessment of BBS

Nowadays an appointment at the hospital is needed to perform the BBS test, and patients need to wait for a physiotherapist to be available for guiding the test. These appointments can be due to a long time, and require human resources that might not be available.
Thus, our goal is to develop an automated system to perform the BBS test, eliminating the need for hospital visits, reducing workload at crowded hospitals, and at the same time increasing the number of elderly that can be diagnosed.

## Data Representation

For the data representation we have used a novel multi camera tracking system, which consists of two self-calibrated Kinect cameras, and recorded 130 subjects performing the 14 BBS tasks while being supervised by medical professionals who have given the score labels for each session.
The output of the system is: RGB color frames, depth images which illustrate the 3D points around the subjects and on the floor, and body skeletons which consist of 25 3D points that describe the posture and position of the subject.

![skeleton](https://user-images.githubusercontent.com/78589884/120074551-0cfddd00-c0a6-11eb-86a5-5e44a03dabc8.png)
<img src="https://github.com/masalha-alaa/fall-risk-prediction/blob/master/docs/task9.gif" width="250" alt="depth image gif"/>

## Feature Extraction

As mentioned earlier, output of the recording system is skeletal, RGB, and depth data representing each frame of the session. For example, frame #42 of a particular patient in a particular task could have the left foot skeleton joint at (1.2, -0.7, 0.5) relative to the Kinect camera. From these data we have built additional features which exploit these coordinates, such as several angles and distances between body joints (per frame), thus coming up with a features table per each recording.
It's worth mentioning that the features were chosen by the collaboratoin of the whole team (me, my supervisors, and the physiotherapists).
After building the per-frame feature tables, we had to make a collective features row for each recording, which would represent the recording in the final features table (a table whose rows are sessions, e.g. "patient 42, task 12", and its columns are features). To make the representative feature columns, we calculated various measurements of the per-patient features, such as averages / STDs / histograms / etc., and used them as our new and final features.

## Classifier

For the classification process we used 14 Random Forest Classifiers, and tuned them with sklearn's GridSearchCV. The evaluation step was done using a Leave One Out Cross Validation strategy [(LOOCV)](https://en.wikipedia.org/wiki/Cross-validation_(statistics)#Leave-one-out_cross-validation). We have achieved an average accuracy of 73% per task (random chance baseline is 50%) and an average MSE of 0.44.

After predicting the BBS score of ecah task for all patients, we have created an additional Random Forest Classifier to predict the final BBS score for each patient (we could've simply summed the results of the initial 14 classifiers, but that would lead to a poor accuracy because the individual tasks' predictions were not 100% accurate. Building a new classifier allows adding new features as well as (in a sense) putting weights on existing features and using only the important ones).

After tuning up the final classifier with GridSearchCV, we achieved a final accuracy of 77.97% and an MSE of 0.27 in predicting the total BBS score.

<img src="https://user-images.githubusercontent.com/78589884/120075949-0a9e8180-c0ac-11eb-87d9-9ae9fba8087c.png" width="450" alt="BBS confusion matrix"/>

## Final Notes

Please head over to the [published paper](https://openaccess.thecvf.com/content_CVPRW_2020/papers/w19/Masalha_Predicting_Fall_Probability_Based_on_a_Validated_Balance_Scale_CVPRW_2020_paper.pdf) mentioned above for more details about the used algorithms and methodologies, as well as challenges we have faced and how we solved them.

## Thanks

I would like to express special thanks and gratitude towards my supervisors, Prof. Ilan Shimshoni, and Prof. Hagit Hel-Or, for accompanying me throught this project, and having profound belief in my abilities, as well as the physiotherapy team Adi Toledano and Daphna Niv, without whom this project could not have been carried out.
