# The Role of Race in Jury Selection
-----------------------------------------------
#### What is this?
In 1986, the U.S. Supreme Court ruled in Batson v. Kentucky that it is unconstitutional for prosecutors to use race as a reason for exercising peremptory strikes against African-American potential jurors.Despite the court’s Baton ruling, , research has found that there are still large disparities in the way that prosecutors and defense attorneys treat jurors of different races.

Journalists working on the APM Reports podcast “In The Dark” spent a year gathering and analyzing court records to build a dataset of peremptory strikes in the Fifth Circuit Court District of Mississippi from 1992 through 2017. This analysis was done as part of a larger investigation into the case of Curtis Flowers, a man who has been tried six times for the 1996 murder of four people in Winona, Mississippi. Three convictions were overturned on appeal and two other trials ended in a hung jury. Each time, Flowers was retried. The Mississippi Supreme Court overturned Flowers’ conviction three times. In one of those rulings, the court found that District Attorney Doug Evans committed a _Batson_ violation by striking jurors because of their race. The goal of our analysis was to determine whether  prosecutors’  use of race in jury selection in the Flowers case was part of a larger pattern in the Fifth Circuit Court District of Mississippi during Evans’ tenure.

Our analysis was done in two parts. First, we analyzed the raw disparities in how the prosecution and defense used peremptory strikes. Second, using transcripts from jury selection, reporters tracked how jurors responded to key questions from the lawyers. We used a binary logistic regression model to test the importance of race as a predictor of whether a juror was accepted or struck.

Read about our results, how we gathered the data, and a visualization of peremptory strikes [on our website](https://features.apmreports.org/in-the-dark/mississippi-district-attorney-striking-blacks-from-juries/). Read a detailed methodology on how we analyzed the data [here]( https://www.apmreports.org/files/peremptory_strike_methodology.pdf).

### The Data

This is the raw data that our analysis is based on. We are releasing the data as three separate csv spreadsheets:
1. Trials
2. Jurors
3. Voir dire answers

#### Trials
Each row in the trials spreadsheet represents one trial.
- id
  - Unique ID
- defendant_name
  - The first defendant's name as it appeared on the indictment in the court records
- cause_number
  - Each cause number is structured like `YEAR-TRAIL NUMBER`. This is the number used by the state court system to track trials. Re-trials were assigned the same cause number so we added brackets to cause numbers to differentiate between different trials under the same cause number.
- state_strikes
  - Boolean, did the state use all its peremptory strikes.
- defense_strikes
  - Boolean, did the defense use all its peremptory strikes.
- county
  - Which county did the trial take place in
- defendant_race
  - The race of the first defendant listed on the indictment
- second_defendant_race
  - The race of the second defendant listed on the indictment, if any
- third_defendant_race
  - The race of the third defendant listed on the indictment, if any
- fourth_defendant_race
  - The race of the fourth defendant listed on the indictment, if any
- more_than_four_defendants
  - Boolean, true if there are more than four defendants listed on the indictment
- judge
  - The judge who heard the case
- prosecutor_1 : prosecutor_3
  - The names of the prosecutors listed on the case. It was often very difficult to determine which prosecutor/defense attorney argued a case unless we also had the transcript, which said which lawyers were in the room for the trial. While we tried to be as accurate as possible, it is advisable to only use the lawyers' names when the trial also had a transcript (where `voir_dire_present=True`). “In the Dark” only used the prosecutor's name in our analysis for trials that we had transcripts for. Otherwise we cited 'Doug Evans and his assistants'
- prosecutors_more_than_three
  - Boolean, were there were more than three prosecutors on the case
- def_attny_1 : def_attny_3
  - names of the defense attorneys on the case
- def_attnys_more_than_three
  - Boolean, more than three defense attorneys on the case
- offense_code_1 : offense_code_6
  - The code violation, as written on the indictment
- offense_title_1 : offense_title_6
  - The plain English offense. This is not consistent across trials. When doing analysis, In the Dark used only the offense code
- more_than_six
  - Boolean, more than six charges on the indictment
- verdict
  - The verdict in the case.
- case_appealed
  - Boolean, was the case appealed
- batson_claim_by_defendant
  - Did the defendant make a _Batson_ challenge? We have this data only when the trial also had a transcript (when `voir_dire_present=True`).
- batson_claim_by_state
    - Did the state make a _Batson_ challenge? We have this data only when the trial also had a transcript (when `voir_dire_present=True`).
- voir_dire_present
  - Boolean, did we have a transcript of jury selection.

#### Jurors
Each row is one potential juror
- id
  - Unique ID
- trial
  - Plain English name of the trial for which the potential juror was summoned.
- trial__id
  - Unique ID of the trial for which the potential juror was summoned.
- race
  - Race of the potential juror
- gender
  - Gender of the potential juror
- race_source
  - The source of the race information
- gender_source
  - The source of the gender information
- struck_by
  - Was the juror struck for cause, by the state, by the defense, or accepted
- strike_eligibility
  - Not every juror is eligible to be accepted or rejected by each side. Strike eligibility tracks which side had the option of rejecting or accepting the juror. For example, if the state has run out of peremptory strikes and the defense hasn't, a juror would be marked as only defense eligible. The prosecution's and defense's strike rates should only be calculated using jurors they were eligible to strike.

#### Voir Dire Answers
Each row is a single juror's answers from a single trial. This is the raw data that we used to track answers as we read through transcripts. To see how we cataloged juror answers and how we transformed the raw data for our logistic regression model, please read our [methodology paper]( https://www.apmreports.org/files/peremptory_strike_methodology.pdf). Most of these variables are booleans.
- id
  - Unique ID for the voir dire answers row
- juror_id
  - Unique ID for the juror the answers are connected to
- juror_id__trial__id
  - Unique ID of the trial
- no_responses
  - Juror did not respond to questions during voir dire
- married
  - Not boolean, juror's martial status
- children
  - Not boolean, does the juror have children
- religious
  - Not boolean, is the juror religious
- education
  - Not boolean, what is the juror's education level
- leans_state
    - Juror expressed a bias in favor of the prosecution
- leans_defense
  - Juror expressed a bias in favor of the defense
- leans_ambi
  - Juror expressed a bias but not clear if it favors the state or the defense
- moral_hardship
  - Juror expressed a moral or emotional hardship
- job_hardship
  - Juror said his or her  occupation would make serving difficult
- caretaker
  - Juror had caretaker obligations
- communication
  - Juror had difficulty communicating or understanding
- medical
  - Juror had medical problems preventing the juror from serving
- employed
  - Juror is unemployed
- social
  - Juror said prior social obligations would make serving difficult
- prior_jury
  - Juror has served on a jury before
- crime_victim
  - Juror has been the victim of a crime
- fam_crime_victim
  - Juror has friends or family that have been the victims of a crime
- accused
  - The juror was accused of being involved in criminal activity
- fam_accused
  - The juror has friends or family accused of being involved in criminal activity
- eyewitness
  - The juror was an eyewitness to a crime
- fam_eyewitness
  - The juror has friends or family that were eyewitnesses to a crime
- military
  - The juror served in the military
- law_enforcement
  - The juror works or has worked in law enforcement
- fam_law_enforcement
  - The juror has friends or family who work or have worked in law enforcement
- premature_verdict
  - Juror admitted to a fixed opinion on guilt or innocence
- premature_guilt
  - Juror admitted they believe the defendant is guilty
- premature_innocence
  - Juror admitted he or she believes the defendant is innocent
- def_race
  - Juror admitted the race of the defendant would affect his or her decision
- vic_race
  - Juror admitted the race of the victim would affect his or her decision
- def_gender
  - Juror admitted the gender of the defendant would affect his or her decision
- vic_gender
  - Juror admitted the gender of the victim would affect his or herdecision
- def_social
  - Juror admitted the social class of the defendant would affect his or her decision
- vic_social
  - Juror admitted the social class of the victim would affect his or her decision
- def_age
  - Juror admitted the age of the defendant would affect his or her decision
- vic_age
  - Juror admitted the age of the victim would affect his or her decision
- def_sexpref
  - Juror admitted the sexual preference of the defendant would affect his or her decision
- vic_sexpref
  - Juror admitted the sexual preference of the victim would affect his or herdecision
- def_incarcerated
  - Juror admitted knowing the defendant was previously incarcerated would affect his or her decision
- vic_incarcerated
  - Juror admitted knowing the victim was previously incarcerated would affect his or herdecision
- beliefs
  - Juror admitted to moral/religious/conscientious beliefs that would affect his or her decision or prevent them from sitting in judgement
- other_biases
  - Juror admitted another reason the juror would not be able to be fair
- innocence
  - Juror said he or she would not be able to presume defendant is innocent until proven guilty
- take_stand
  - Juror said he or she would not be impartial if the defendant did not take the stand
- arrest_is_guilt
  - Juror admitted to believing that being arrested is evidence of guilt
- cant_decide
  - Juror said he or shewould have difficulty making a decision during deliberation
- cant_affirm
  - Juror said he or she would have difficulty affirming verdict in an open court if polled
- cant_decide_evidence
  - Juror said he or shewould have difficulty making decisions based only on evidence
- cant_follow
  - Juror said he or she would have difficulty following court's instructions
- know_def
  - Juror has prior familiarity with defendant through either personal or professional channels
- know_vic
  - Juror has prior familiarity with victim through either personal or professional channels
- know_wit
  - Juror has prior familiarity with witnesses through either personal or professional channels
- know_attny
  - Juror has prior familiarity with attorneys or the judge through personal or professional channels
- civil_plantiff
  - Juror was a plaintiff in a civil dispute
- civil_def
  - Juror was a defendant in a civil dispute
- civil_witness
  - Juror was a witness in a civil dispute
- witness_defense
  - Juror was a witness for the defense in a criminal case
- witness_state
  - Juror was a witness for the state in a criminal case
- prior_info
  - Juror had prior information on the case
- death_hesitation
  - Juror expressed reservations about imposing the death penalty either because of moral, religious or ethical reasons
- no_death
  - Juror said he or she could not or would not impose the death penalty
- no_life
  - Juror expressed reservations about imposing a life sentence because of moral, religious or ethical reasons
- no_cops
  - Juror expressed that he or she was less likely to believe the testimony of the police over other witnesses
- yes_cops
  - Juror expressed that he or she was more likely to believe the testimony of the police over other witnesses
- legally_disqualified
  - Juror was not legally allowed to serve on the jury
- witness_ambi
  - Juror was a witness in a criminal case but not specific about which side

#### Licensing
All data files are available under the [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/legalcode) license.

Any questions or comments should be directed to [Will Craft](mailto:wcraft@apmreports.org).

#### Credits
Data gathering and entering: Will Craft, David Montgomery, Rehman Tungekar, Parker Yesko

Data fact-checking: Angela Caputo, David Montgomery, Geoff Hing, Rehman Tungekar, Parker Yesko

Data Analysis: Will Craft
